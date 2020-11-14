# Build Commands LE 9.0.x

| Image | Command |
| :--- | :--- |
| Generic \(x86\_64\) | PROJECT=Generic ARCH=x86\_64 make image |
| Raspberry Pi 0/1 | PROJECT=RPi ARCH=arm DEVICE=RPi make image |
| Raspberry Pi 2/3 | PROJECT=RPi ARCH=arm DEVICE=RPi2 make image |
| Raspberry Pi 4 | PROJECT=RPi ARCH=arm DEVICE=RPi4 make image |
| Allwinner A20 | PROJECT=Allwinner ARCH=arm DEVICE=A20 make image |
| Allwinner A64 | PROJECT=Allwinner ARCH=arm DEVICE=A64 make image |
| Allwinner H3 | PROJECT=Allwinner ARCH=arm DEVICE=H3 make image |
| Allwinner H5 | PROJECT=Allwinner ARCH=arm DEVICE=H5 make image |
| Allwinner H6 | PROJECT=Allwinner ARCH=arm DEVICE=H6 make image |

## Notes

The Virtual project was combined into the Generic image. Running `make image` will output an OVA file in addition to the normal .img.gz and .tar files.

Amlogic, Raspberry Pi, and Rockchip build projects require a `DEVICE=<name>` to be specified. For example, to build an Amlogic image for a LibreComputer LePotato board:

```text
PROJECT=Amlogic ARCH=arm DEVICE=LePotato make image
```

## Environment

Official releases were built using Ubuntu 16.04 and 18.04 LTS.

