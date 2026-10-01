# Unofficial Linux builds of VIAMD

Ready-to-run Linux builds of [VIAMD](https://github.com/scanberg/viamd).

> [!WARNING]
> **These are unofficial builds.** They are not made, endorsed or supported by the VIAMD developers.
> For the software itself, its documentation and support, see the [VIAMD repository](https://github.com/scanberg/viamd) and its [wiki](https://github.com/scanberg/viamd/wiki). Problems with these builds (the program won't start, missing libraries) should be reported here, not upstream.

## Install on Debian 12

For yourself, in the current directory:

```sh
curl -fL https://github.com/VachaLab/viamd_linux_builds/releases/latest/download/viamd-debian12-x86_64-avx2.tar.gz | tar -xz
cd viamd-debian12-x86_64-avx2
./viamd
```

The folder can live anywhere: VIAMD finds its `basis/` and `datasets/` folders next to the executable, also when started through a symlink.

## Other systems and CPUs

The file names follow the pattern `viamd-<system>-x86_64-<cpu>.tar.gz`. Replace `debian12-x86_64-avx2` in the commands above with the one for your machine.

| `<system>`     | For                  |
| -------------- | -------------------- |
| `debian12`     | Debian 12 (bookworm) |
| `ubuntu-22.04` | Ubuntu 22.04         |
| `ubuntu-24.04` | Ubuntu 24.04         |

| `<cpu>`    | Runs on                                                       |
| ---------- | ------------------------------------------------------------- |
| `avx2`     | almost any x86-64 CPU from about 2015 on (needs AVX2 and FMA) |
| `baseline` | any x86-64 CPU (slower)                                       |
| `avx512`   | only CPUs with AVX-512                                        |

To see what your CPU supports:

```sh
grep -o -w -E 'avx2|fma|avx512f' /proc/cpuinfo | sort -u
```

If it prints `avx2` and `fma`, use the `avx2` build.

## Versions and updates

The `releases/latest/download/...` address always gives the newest VIAMD release. For a specific version, put the version in its place:

```sh
curl -fL https://github.com/VachaLab/viamd_linux_builds/releases/download/v0.1.48/viamd-debian12-x86_64-avx2.tar.gz | tar -xz
```

To update, delete the old folder first (so no files from the old version are left behind) and
then run the install commands again:

```sh
rm -rf viamd-debian12-x86_64-avx2
```

`BUILD_INFO.txt` in the folder says which VIAMD version you have and how it was built.

To check that a download is complete and unmodified, download the `.sha256` file next to it and compare:

```sh
curl -fLO https://github.com/VachaLab/viamd_linux_builds/releases/latest/download/viamd-debian12-x86_64-avx2.tar.gz
curl -fLO https://github.com/VachaLab/viamd_linux_builds/releases/latest/download/viamd-debian12-x86_64-avx2.tar.gz.sha256
sha256sum -c viamd-debian12-x86_64-avx2.tar.gz.sha256
```

## Troubleshooting

**`curl: (22) The requested URL returned error: 404`:** there is no file with that name in the release. Check the name, or look on the [Releases page](https://github.com/VachaLab/viamd_linux_builds/releases) for what is available for your system.

**"Illegal instruction" right after starting:** your CPU lacks an instruction set the build uses. Check with the `grep` command above and use a `baseline` build.

**"error while loading shared libraries":** the run-time libraries are not installed.

**Wayland desktops:** VIAMD runs through XWayland, which is part of standard GNOME and KDE installations.

## License

VIAMD is MIT-licensed by its authors. Every archive contains VIAMD's `LICENSE`, and the licenses of the libraries compiled into it in `licenses/`.
