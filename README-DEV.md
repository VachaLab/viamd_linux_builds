# Maintainer notes

How the unofficial [VIAMD](https://github.com/scanberg/viamd) builds are produced. Instructions for users are in [README.md](README.md).

## Requesting a build

The workflows never run on their own. To start one, open **Actions**, select the platform to target in the sidebar, click **Run workflow**, fill in the inputs and confirm. The Ubuntu workflow builds for both 22.04 and 24.04.

| Input       | Default  | Meaning                                                                                   |
| ----------- | -------- | ----------------------------------------------------------------------------------------- |
| `viamd_ref` | `master` | VIAMD branch or [tag](https://github.com/scanberg/viamd/tags) to build (no commit hashes) |
| `simd`      | `avx2`   | CPU instruction set: `avx2`, `baseline` or `avx512`                                       |

For builds other people will install, use a release tag. `avx2` suits practically all current machines, including those with AVX-512; `baseline` is only for very old CPUs.

## Releases

Every successful run publishes its archives, with `.sha256` files, to a release in this repository:

| Built `viamd_ref`                    | Release              | Type              |
| ------------------------------------ | -------------------- | ----------------- |
| the newest VIAMD tag, e.g. `v0.1.48` | `v0.1.48`            | marked **Latest** |
| an older VIAMD tag                   | same name as the tag | normal            |
| a branch, e.g. `master`              | `master-<commit>`    | pre-release       |

- Both workflows publish into the same release for the same VIAMD version. Running a workflow again replaces its files there.
- File names contain no version, so `releases/latest/download/<file>` (used in README.md) always serves the file from the release marked Latest. **When you publish a new VIAMD version, run both
  workflows**, otherwise the `latest` address returns 404 for the system that is missing.
- Archives are published only after the launch test has passed.

## What a run does

The workflow clones VIAMD with its submodules and builds it (Release, `VIAMD_ENABLE_QM=ON`, the chosen `simd` level, unit tests off). It checks that every library resolves, then packs `build/bin` with the licenses, `BUILD_INFO.txt` and `RUNTIME_PACKAGES.txt` into `viamd-<system>-x86_64-<simd>.tar.gz`. `RUNTIME_PACKAGES.txt` lists the packages that provide the linked libraries, plus `libGL`, which VIAMD loads at run time.

A launch test then starts VIAMD under a virtual X display with software OpenGL and passes if it is still running after 20 seconds. For Debian this happens in a fresh `debian:12` container with
only `RUNTIME_PACKAGES.txt` installed. Finally the archive is published.
