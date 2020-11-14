# Building \(Basics\)

The LibreELEC “build-system” is a collection of scripts that simplify the complex task of cross-compiling hundreds of inter-dependent source packages into a working LibreELEC image with a few simple commands. It is the "secret sauce" of the project, as it allows users of all experience levels to tinker and experiement with the distro. Forking our codebase and experimenting is encouraged!

## Environment

The team currently builds official releases and snapshots on Ubuntu LTS server 18.04 or 20.04. It is possible to use other Linux distrutions \(Arch, Debian, Fedora, Gentoo, Manjaro, etc.\) but Ubuntu is the most-used and proven. If you plan to build on an existing Linux desktop we recommend building in a Docker container to isolate the build folders from the rest of the OS \(Dockerfiles can be found in the tools folder of the main LibreELEC git repo\). Another option is building in a Virtual Machine, e.g. Oracle Virtualbox \($free\) or vmware Workstation.

Note: If you want to compile older LibreELEC releases you will need to use an era-appropriate Ubuntu LTS release, e.g. LibreELEC 7.0 will need Ubuntu 14.04 or 16.04.

## Hardware

Image build times depend on CPU and I/O performance so the more CPU cores you have or can allocate, the faster your build times will be. For example:

Approx. 3 hours build time:

* 2x Core i3 CPU
* 4GB RAM \(with swap enabled\)
* 50GB HDD

Approx. 1.5 hours build time:

* 4x Core i7 CPU
* 8GB RAM
* 100GB SSD

Approx. 30 mins build time:

* 32x Core AMD Threadripper2
* 64GB RAM
* 1TB NVME SSD

Testing with large CPU counts shows build times continue to decrease as CPU count increases \(as you'd expect\) although over ~16x CPUs the gains per-CPU reduce, and overall build times are limited by the inherently sequential nature of the initial stages of building \(creating the cross-compile toolchain\). The current build record is ~22 mins \(64x CPUs, build folders on a ramdisk\).

## Dependencies

Before you can build, it is important to update the OS and install build dependencies:

```text
sudo apt update
sudo apt upgrade
sudo apt install gcc make git unzip wget xz-utils bc gperf zip unzip makeinfo g++ mkfontscale mkfontdir bdftopcf xsltproc java
```

Note: The first time the build-system runs it will validate and prompt you to install any missing package dependencies.

## Cloning

Use `git` to clone the LibreELEC sources to your user folder:

```text
cd ~
git clone https://github.com/LibreELEC/LibreELEC.tv.git
cd LibreELEC.tv
```

## Versions

After cloning you are now at the current `HEAD` position of the `master` development branch. To compile a specific LibreELEC release we need to `checkout` the specific version `tag` or `githash` associated with the release, e.g. to build the LibreELEC 9.2.0 release we checkout the `9.2.0` tag:

```text
git checkout 9.2.0
```

You can also build at a specific point in the git revision history identified by a commit githash:

```text
git checkout 65136474fda5302f427f492d40da66645b125cf1
```

You are now in the root folder of the build-system and ready to build!

## Sources

The first time the build-system runs it will download and cache package sources, but there are ~260-380 packages to cache \(depending on the build `PROJECT`\) and some sources are large, and others are download from super-slow servers. To download sources in advance of building, use `download-tool`, e.g.

```text
PROJECT=Generic ARCH=x86_64 tools/download-tool
```

## Building

As a normal user \(not as root, and not using sudo\) run a build command, e.g.

```text
PROJECT=Generic ARCH=x86_64 make image
```

This will compile the `Generic` build `PROJECT` for the `x86_64` arch, and `make image` will generate an .img.gz file that can be written to USB/SD media for installation, and a .tar file that can update an existing installtion \(.img.gz files can also be used for updates, but .tar files are faster\).

It is not uncommon for first-time builds to take 6-8 hours due to the number of sources that must be downloaded. Once the build completes the finished image files will be in the `~/LibreELEC.tv/target/` directory.

## Rebuilding

After completing your first build, you probably want to make some nip-tuck changes and then rebuild the image to include them. If you re-run the same build command the build-system will detect most changes and will clean and rebuild only the packages that have been changed. This means a "respin" with minor changes often takes only 1-2 minutes. As a general rule you can keep rebuilding \(respinning\) unless there are changes to the "toolchain" packages. If these are changed, you will need to clean or remove the build folders and make a "clean build" again.

## Cleaning

If you see strange or transient compilation errors, start by cleaning the sources for the failing package and clear the compiler cache \(ccache\), e.g. to clean the linux package:

```text
PROJECT=Generic ARCH=x86_64 scripts/clean linux
```

You can also remove all build directories \(keeping ccache\):

```text
make clean
```

Or just for one project:

```text
PROJECT=Generic ARCH=x86_64 make clean
```

To remove everything and ccache:

```text
make distclean
```

Or just for one project:

```text
PROJECT=Generic ARCH=x86_64 make distclean
```

