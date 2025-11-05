# Build Commands LE 12.0.x

## Build Commands LE 12.0.x

| Image                        | Command                                                       |
| ---------------------------- | ------------------------------------------------------------- |
| Allwinner A64                | PROJECT=Allwinner ARCH=aarch64 DEVICE=A64 make image          |
| Allwinner H3                 | PROJECT=Allwinner ARCH=arm DEVICE=H3 make image               |
| Allwinner H5                 | PROJECT=Allwinner ARCH=aarch64 DEVICE=H5 make image           |
| Allwinner H6                 | PROJECT=Allwinner ARCH=aarch64 DEVICE=H6 make image           |
| Allwinner R40                | PROJECT=Allwinner ARCH=arm DEVICE=R40 make image              |
| Amlogic GXBB/GXL/GXM/G12/SM1 | PROJECT=Amlogic ARCH=aarch64 DEVICE=AMLGX make image          |
| Generic x86\_64 GBM          | PROJECT=Generic DEVICE=Generic ARCH=x86\_64 make image        |
| Generic x86\_64 X11 (legacy) | PROJECT=Generic DEVICE=Generic-legacy ARCH=x86\_64 make image |
| NXP/Freescale iMX.6          | PROJECT=NXP ARCH=arm DEVICE=iMX6 make image                   |
| NXP/Freescale iMX.8          | PROJECT=NXP ARCH=aarch64 DEVICE=iMX8 make image               |
| Qualcomm DragonBoard         | PROJECT=Qualcomm ARCH=aarch64 DEVICE=Dragonboard make image   |
| Raspberry Pi 2/3             | PROJECT=RPi ARCH=arm DEVICE=RPi2 make image                   |
| Raspberry Pi 4               | PROJECT=RPi ARCH=aarch64 DEVICE=RPi4 make image               |
| Raspberry Pi 5               | PROJECT=RPi ARCH=aarch64 DEVICE=RPi5 make image               |
| Samsung Exynos               | PROJECT=Samsung ARCH=arm DEVICE=Exynos make image             |
| Rockchip RK3328              | PROJECT=Rockchip ARCH=arm DEVICE=RK3328 make image            |
| Rockchip RK3288              | PROJECT=Rockchip ARCH=arm DEVICE=RK3288 make image            |
| Rockchip RK3399              | PROJECT=Rockchip ARCH=arm DEVICE=RK3399 make image            |

### Notes

To improve GHA/CI automation with ARM SoC `$PROJECT`(s) that support multiple `$DEVICE` types `make image` will iterate through all board/u-boot configurations defined in `scripts/uboot_helper` resulting in \~3-10 images in the target folder. To avoid this behaviour and build a single board-specific image `UBOOT_SYSTEM=<board>` can be appended to the build command, e.g. to build an Amlogic image for a LibreComputer LePotato board:

```
PROJECT=Amlogic ARCH=aarch64 DEVICE=AMLGX UBOOT_SYSTEM=lepotato make image
```

Several ARM SoC devices have a `UBOOT_SYSTEM=box` configuration which excludes u-boot and provides all device-trees, allowing the image to boot using the Android/BSP u-boot on emmc/spi.

### Environment

Official releases were built using Ubuntu 24.04 LTS.
