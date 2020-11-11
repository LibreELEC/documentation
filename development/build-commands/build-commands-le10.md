# Build Commands LE 10.0.x

| Image | Command |
| :--- | :--- |
| Allwinner A20 | PROJECT=Allwinner ARCH=arm DEVICE=A20 make image |
| Allwinner A64 | PROJECT=Allwinner ARCH=arm DEVICE=A64 make image |
| Allwinner H3 | PROJECT=Allwinner ARCH=arm DEVICE=H3 make image |
| Allwinner H5 | PROJECT=Allwinner ARCH=arm DEVICE=H5 make image |
| Allwinner H6 | PROJECT=Allwinner ARCH=arm DEVICE=H6 make image |
| Amlogic GXBB/GXL/GXM | PROJECT=Amlogic ARCH=arm DEVICE=AMLGX make image |
| Amlogic G12A/G12B/SM1 | PROJECT=Amlogic ARCH=arm DEVICE=AML12 make image |
| Generic Intel/AMD x86_64 | PROJECT=Generic ARCH=x86_64 make image |
| NXP/Freescale iMX.6 | PROJECT=NXP ARCH=arm DEVICE=imx6 make image |
| Qualcomm DragonBoard | PROJECT = Qualcomm ARCH=arm DEVICE=Dragonboard make image |
| Raspberry Pi 0/1 | PROJECT=RPi DEVICE=RPi ARCH=arm make image |
| Raspberry Pi 2/3 | PROJECT=RPi DEVICE=RPi2 ARCH=arm make image |
| Raspberry Pi 4 | PROJECT=RPi DEVICE=RPi4 ARCH=arm make image |
| Samsung Exynos | PROJECT=Samsung ARCH=arm DEVICE=Exynos make image |
| Rockchip RK3328 | PROJECT=Rockchip DEVICE=RK3328 ARCH=arm make image |
| Rockchip RK3288 | PROJECT=Rockchip DEVICE=RK3288 ARCH=arm make image |
| Rockchip RK3399 | PROJECT=Rockchip DEVICE=RK3399 ARCH=arm make image |

## Notes

To improve Jenkins/CI automation with ARM SoC `$PROJECT`(s) that support multiple `$DEVICE` types `make image` will iterate through all board/u-boot configurations defined in `scripts/uboot_helper` resulting in ~3-10 images in the target folder. To avoid this behaviour and build a single board-specific image `UBOOT_SYSTEM=<board>` can be appended to the build command, e.g. to build an Amlogic image for a LibreComputer LePotato board:

```
PROJECT=Amlogic ARCH=arm DEVICE=AMLGX UBOOT_SYSTEM=lepotato make image
```

Several ARM SoC devices have a `UBOOT_SYSTEM=box` configuration which excludes u-boot and provides all device-trees, allowing the image to boot using the Android/BSP u-boot on emmc/spi.

## Environment

Official releases were built using Ubuntu 18.04 and 20.04 LTS.
