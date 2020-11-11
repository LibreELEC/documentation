# Build Commands LE 7.0.x

| Image | Command |
| :--- | :--- |
| Freescale iMX6 | PROJECT=imx6 ARCH=arm make image |
| Generic (x86_64) | PROJECT=Generic ARCH=x86_64 make image |
| Raspberry Pi 0/1 | PROJECT=RPi ARCH=arm make image |
| Raspberry Pi 2/3 | PROJECT=RPi2 ARCH=arm make image |
| VMware (not Virtualbox) | PROJECT=Virtual ARCH=x86_64 make image |
| WeTek Play, WeTek OpenELEC | PROJECT=WeTek_Play ARCH=arm make image |
| WeTek Core | PROJECT=WeTek_Core ARCH=arm make image |

## Environment

Official releases were built using Ubuntu 14.04 and 16.04 LTS.
