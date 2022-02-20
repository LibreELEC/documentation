---
description: Release management is as simple as plan, build, test, prepare and deploy.
---

# Release Management

## Package management&#x20;

LibreELEC is made up of over 800 packages which are in themselves developed by many other teams and groups across the Internet. These packages are maintained in the `packages` directory. An example of a package is **Linux** which has its main **LibreELEC** `makefile` as `packages/linux/package.mk`, or **Kodi** `packages/mediacenter/kodi/package.mk`.

The project team and Contributors prepare and regularly update the code that makes up the LibreELEC distribution. This is done by "bumping" the `PKG_VERSION` in the `package.mk` file and updating the other variables, code, patches and dependencies that make up the distribution.  These are then rolled up into a [#pullrequests](git-tutorial.md#pullrequests "mention")and submitted as a change.

### Release Monitoring&#x20;

The `update-scan` tool has been developed to check `PKG_VERSION` of packages against release monitoring sites. This tool currently uses **Anitya** from [https://release-monitoring.org/](https://release-monitoring.org) with the following distribution [https://release-monitoring.org/distro/LibreELEC/](https://release-monitoring.org/distro/LibreELEC/)

An example of the output from `update-scan` is below:

```
$ tools/update-scan
Github api usage activated

Updates found:

Package            | LE git master         | upstream location
--------------------------------------------------------------
Pillow             | 8.4.0                 | 9.0.1
Python3            | 3.8.12                | 3.11.0a5
RPi.GPIO           | 0.7.1a4               | 0.7.0
.
.
.
```

The tool provides a report at the end of the currency of the packages in the current checked out **LibreELEC** code.

```
Current 449:
Jinja2 Mako MarkupSafe ...

Ignored 84:
adafruit-libraries alsa ...

Packages not known at tracker 23:
RTL8192CU configtools dvb-latest edid-decode firmware-dragonboard \
firmware-imx gcc-riscv64-unknown-linux-gnu getscancodes \
gnulib jdk-aarch64-zulu jdk-arm-zulu jdk-x86_64-zulu kmscube \
lan951x-led-ctl media_tree oscam szap-s2 t2scan tune-s2 unfsd \
vdr-plugin-dummydevice vdr-plugin-wirbelscancontrol vdr-plugin-wirbelscan
```

Packages are ignored if either the `PKG_VERSION` _or_ `PKG_URL` __ are empty or hosted by **LibreELEC**.

For packages not known at the tracker - this means that the package is either not release monitored or not configured for release monitoring at [https://release-monitoring.org/distro/LibreELEC/](https://release-monitoring.org/distro/LibreELEC/).

The following image shows the **Projects of LibreELEC** monitored by **release-monitoring**.

![](<../.gitbook/assets/image (3).png>)

Adding a project to the distro can be made by logging in using your **Fedora** ID.

![](<../.gitbook/assets/image (4) (1).png>)

Search for the project that you have developed the package.mk for and you want release-monitoring to monitor. In the example - we have added the distro **LibreELEC** to `aixlog`.

![](<../.gitbook/assets/image (5).png>) &#x20;

Click the `Add new distribution mapping` button within the Project [https://release-monitoring.org/project/141477/](https://release-monitoring.org/project/141477/) (**aixlog** example.)

![](<../.gitbook/assets/image (6).png>)

You need to choose **LibreELEC** from the **Distribution** drop down. Then click `+ Add mapping to project`.

{% hint style="info" %}
The Package Name must match the `PKG_NAME` variable from the **LibreELEC** `package.mk` file. The `Project name` from the Project in release-monitoring.org does not have to match the **LibreELEC** `PKG_NAME`.&#x20;
{% endhint %}
