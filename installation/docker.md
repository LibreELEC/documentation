# Containers

The awesome folks at [LinuxServer.io](https://linuxserver.io) maintain a range of popular Docker containers installable as Kodi add-ons. After installing the Docker add-on (from the LibreELEC add-on repo) you can install the LinuxServer repo add-on (from the LibreELEC add-on repo) and then install container add-ons from within the Kodi GUI. To make maintenance easier LinuxServer add-ons are designed to pull the latest Docker image automatically on Kodi startup to keep the containers fresh.

It is also possible to install Docker containers manually from the SSH console using `docker pull` commands. This allows you to install a container from any public container repository. This is also how you can access the full [fleet of LinuxServer containers](https://fleet.linuxserver.io).

## Container Architecture

Docker containers often ship in multiple CPU "architecture" or "arch" variants. To run, the container architecture must match the "userspace" CPU arch of the LibreELEC image which can be different from "kernel" CPU architecture. The userspace arch is noted in the image filename, e.g.

* LibreELEC-Generic.**x86\_64**-11.0.1.img.gz has `x86_64` userspace
* LibreELEC-RPi4.**arm**-11.0.1.img.gz has `arm` userspace
* LibreELEC-RPi4.**aarch64**-12.0-nightly-20230310.img.gz has `aarch64` userspace

Userspace CPU arch can also be read from the `/etc/os-release` file in the filestem, e.g.

```
RPi4:~ # cat /etc/os-release 
NAME="LibreELEC"
VERSION="11.0.3"
ID="libreelec"
VERSION_ID="11.0"
PRETTY_NAME="LibreELEC (official): 11.0.3"
HOME_URL="https://libreelec.tv"
BUG_REPORT_URL="https://github.com/LibreELEC/LibreELEC.tv"
BUILD_ID="1189cdb0f43e6f4208f747d54fefb70478ebee9d"
LIBREELEC_ARCH="RPi4.arm"  <= This contains the userspace CPU arch
LIBREELEC_BUILD="official"
LIBREELEC_PROJECT="RPi"
LIBREELEC_DEVICE="RPi4"
```

Docker containers may also use different arch naming, e.g.

* The `arm` arch may be referred to as `linux/arm` and `linux/arm/v7`&#x20;
* The `aarch64` arch may be referred to as `linux/arm64` and `linux/arm64/v8`
* The `x86_64` arch may be referred to as `amd64`&#x20;

{% hint style="warning" %}
LibreELEC 12 will switch most ARM SoC devices capable of running a 64-bit kernel from `arm` to `aarch64` userspace. This will require affected users to remove `arm` containers before they update and redeploy `arm64` versions post-update.
{% endhint %}
