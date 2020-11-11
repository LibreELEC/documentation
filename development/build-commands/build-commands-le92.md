# Build Commands LE 9.2.x

Amlogic images based on the legacy Linux 3.14 kernel were dropped for this release, with development shifting to the mainline Linux kernel.

| Image | Command |
| :--- | :--- |
| Allwinner A64 | PROJECT=Allwinner ARCH=arm DEVICE=A64 make image |
| Allwinner H3 | PROJECT=Allwinner ARCH=arm DEVICE=H3 make image |
| Allwinner H6 | PROJECT=Allwinner ARCH=arm DEVICE=H6 make image |
| Generic Intel/AMD x86_64 | PROJECT=Generic ARCH=x86_64 make image |
| Raspberry Pi 0/1 | PROJECT=RPi ARCH=arm DEVICE=RPi make image |
| Raspberry Pi 2/3 | PROJECT=RPi ARCH=arm DEVICE=RPi2 make image |
| Raspberry Pi 4 | PROJECT=RPi ARCH=arm DEVICE=RPi4 make image |
| Rockchip RK3328 | PROJECT=Rockchip DEVICE=RK3328 ARCH=arm make image |
| Rockchip MiQi | PROJECT=Rockchip DEVICE=MiQi ARCH=arm make image |
| Rockchip Tinkerboard/S | PROJECT=Rockchip ARCH=arm DEVICE=Tinkerboard make image |
| Rockchip RK3399 | PROJECT=Rockchip ARCH=arm DEVICE=RK3399 make image |

## Notes

To improve Jenkins/CI automation with ARM SoC projects that support multiple `$DEVICE` types `make image` will iterate through all board/u-boot configurations for the device defined in `scripts/uboot_helper` resulting in ~3-10 images in the target folder. To avoid this behaviour and build a single board-specific image `UBOOT_SYSTEM=<board>` can be appended to the build command, e.g. to build an Amlogic image for a LibreComputer LePotato board:

```
PROJECT=Amlogic ARCH=arm DEVICE=AMLGX UBOOT_SYSTEM=lepotato make image
```

Several ARM SoC devices have a `UBOOT_SYSTEM=box` configuration which excludes u-boot and provides all device-trees, allowing the image to boot using the Android/BSP u-boot on eMMC/SPI.

## Environment

Official releases were built using Ubuntu 18.04 LTS.
