# Containers

The awesome folks at [LinuxServer.io](https://linuxserver.io) maintain a range of popular Docker containers installable as Kodi add-ons. After installing the Docker add-on from the LibreELEC repo, you can install the LinuxServer add-on repo \(from the LibreELEC repo\) and then install specific container add-ons. The add-ons are designed to auto-pull the latest Docker image on Kodi startup to keep your Dockers fresh.

It is also possible to install LinuxServer Docker containers from the SSH console, which is also how to access the full [fleet of LinuxServer containers](https://fleet.linuxserver.io).

## A note on Docker Container Architecture

LibreELEC's kernel and user space may support different architectures. For instance, starting with version 10, LibreELEC on ARM64-capable devices (e.g. Raspberry Pi 3/4) uses a 64-bit kernel whereas the user space is 32-bit.

For Docker this means that it can only run containers from the images compatible with LibreELEC's user space. For example, if you want to run a Docker container in LibreELEC 11 on Raspberry Pi 4, the Docker image should be built for the platform `linux/arm` (same as `linux/arm/v7` or `linux/arm/v8`), but not `linux/arm64` (same as `linux/arm64/v8`) as Docker will fail to run those.

Starting with LibreELEC 12, certain ARMv8 releases of LibreELEC have a 64-bit user space to accompany the 64-bit kernel.

