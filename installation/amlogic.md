# Amlogic

Images for Amlogic hardware using modern, a.k.a "mainline" Linux kernels use different boot processes and device-tree files that are not compatible with Android or older LibreELEC images that use the Amlogic Linux 3.14 or 4.9 kernels. The change to boot processes means you cannot update from older releases you must make a new/clean installation.

We currently ship the `AMLGX` image with "box" and "board" configurations for the following SoCs:

* GXBB \(S905\)
* GXL \(S805X/S905X/D/W/L\)
* GXM \(S912\)
* G12A \(S905X2/D2\)
* G12B \(S922X/A311D\)
* SM1 \(S905X3/D3\)

The image type is identified by the -suffix appended, e.g. `LibreELEC-AMLGX.arm-9.95.0-box.img.gz` or `LibreELEC-AMLG12.arm-10.0.0-khadas-vim3.img.gz`.

## Box Images

The "box" image supports Set-Top "Box" \(STB\) and other devices with Android or Legacy Kernel Linux images \(and vendor U-Boot\) installed on the internal eMMC storage. It is common for box vendors to make device-specific U-Boot customisations so it is best to run LibreELEC from an SD card via the original bootloader. To use a box image you trigger "update" mode in the vendor U-Boot. This causes U-Boot to look for some standard filenames which we have tweaked to load the LibreELEC `KERNEL` and `SYSTEM` files to boot the device.

As `box` images can be used on many devices, you must configure the device-tree file to use first. This is done by editing the uEnv.ini file in the root folder of the SD card and changing `@@DTB_NAME@@` with the name of the .dtb file to use. The device-tree files are in the `dtb` folder.

For example, here is the default `uEnv.ini` file:

```text
dtb_name=/dtb/@@DTB_NAME@@
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

To boot a Beelink GT-King Pro box change `@@DTB_NAME` to `meson-g12b-gtking-pro.dtb` like this:

```text
dtb_name=/dtb/meson-g12b-gtking-pro.dtb
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

Once the device-tree name has been set you can insert the SD card in the box and power on. Some box devices will detect the card automatically. Others need you to trigger recovery mode using a reset button on the device. Sometimes the reset button is obvious. Sometimes it is hidden behind a small hole in the case requiring a paper-clip or needle to press it. Sometimes it is hidden at the end of the 3.5mm audio jack requiring a toothpick to press it. If a reset button is required you press the button and hold it then apply power to the box, and after 5-7 seconds you release the button. Due to differences in vendor U-Boot configuration, the exact timing varies and you may need to experiment a few times to get it right.

## Board Images

These images are built for Single Board Computer \(SBC\) devices which boot modern \(mainline\) U-Boot via an SD card or removable eMMC module. Installation is normally simple requiring you to write the image to the SD card or eMMC module and then boot the device. If the board has non-removable eMMC storage it may be necessary to boot from a "box" image first. Once booted to a box image you can write the `board` image for your device to eMMC \(overwriting Android or other factory-installed images\).

## installtointernal

Community created images using the legacy Amlogic kernels often include the `installtointernal` script to reconfigure the factory boot process and run LibreELEC from the internal emmc storage. In the past when most box devices had 1GB RAM and SD cards were slow \(or badly written software ran them slower\) the performance difference was substantial, so the script evolved a cult following and many users believe they _must_ install to internal eMMC or their box will be unusable. This is wrong advice. Using modern boxes with 2GB+ RAM and better SD card support the performance difference is marginal. You are not missing out by booting from an SD card!

The main reason we do not provide or support emmc installs on "box" devices is the high level of support issues. Software and hardware quality in Android STB hardware is not great, and this complicates the process of successfully installing to the internal eMMC storage so many installs have problems resulting in a "bricked" box. Amlogic builds factory-restore mechanisms into their hardware and software that mean it is possible to recover the box, but this usually depends on finding a working Android image for the device, and the process is challenging for less technical users. Our forum staff are all volunteers who give time to the project for fun. Helping a never-ending stream of pissed-off inexperienced users recover bricked boxes is not fun.

If you want to run LibreELEC from eMMC storage please purchase a "board" device that supports it. If you find and run an `installtointernal` script and something messes up; your problem is not our problem!

