# Amlogic

Images for Amlogic hardware using modern, a.k.a "mainline" Linux kernels use different boot processes and device-tree files that are not compatible with Android or the older LibreELEC images using Amlogic Linux 3.14 or 4.9 kernels. The change to boot processes means you cannot update from older releases. You must make a new/clean installation.

The `AMLGX` image ships with "box" and "board" configurations for the following SoCs:

* GXBB \(S905\)
* GXL \(S805X/S905X/D/W/L\)
* GXM \(S912\)
* G12A \(S905X2/D2/Y2\)
* G12B \(S922X/A311D\)
* SM1 \(S905X3/D3\)

The image type is identified by the -suffix appended:

* `LibreELEC-AMLGX.arm-10.0.0-box.img.gz` is the "box" image for Android devices
* `LibreELEC-AMLGX-arm-10.0.0-khadas-vim3.img.gz` is a "board" image \(for Khadas VIM3\)

## Box Images

The "box" image supports Set-Top "Box" \(STB\) and other devices with Android or Legacy Kernel Linux images \(and vendor u-boot\) installed on the internal eMMC storage. It is common for box vendors to make device-specific u-boot customisations so it is best to run LibreELEC from an SD card via the original bootloader. To use a box image you trigger "update" mode in the vendor u-boot. This causes u-boot to look for some standard filesnames which we have tweaked to load the LibreELEC `KERNEL` and `SYSTEM` files to boot the device.

As `box` images can be used on many devices, you must configure the device-tree file to use first. This is done by editing the uEnv.ini file in the root folder of the SD card and changing `@@DTB_NAME@@` with the name of the .dtb file to use. The device-tree files are in the `dtb` folder.

For example, here is the default `uEnv.ini` file:

```text
dtb_name=/dtb/@@DTB_NAME@@
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

To boot a Beelink GT-King Pro box change `@@DTB_NAME` to `meson-g12b-gtking-pro.dtb`

```text
dtb_name=/dtb/meson-g12b-gtking-pro.dtb
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

Once the device-tree name has been set you can insert the SD card in the box and power on. Some box devices will detect the card automatically. Others need you to trigger recovery mode using a reset button on the device. Sometimes the reset button is obvious. Sometimes it is hidden behind a small hole in the case requiring a paper-clip or needle to press it. Sometimes it is hidden at the end of the 3.5mm audio jack requiring a toothpick or paper-clip to press it. If a reset button is required you press the button and hold it then apply power to the box, and after 5-7 seconds you release the button. Due to differences in vendor u-boot configuration the exact timing varies and you may need to experiment a few times to get it right.

## Board Images

These images are built for Single Board Computer \(SBC\) devices which boot modern u-boot via an SD card or removable eMMC module. Installation is normally simple requiring you to write the image to the SD card or eMMC module then boot the device. If the board has eMMC storage soldered \(not on a removable module\) it may be necessary to boot from the "box" image first. Once booted to a box image on SD card \(so eMMC is not in use\) you can write the correct `board` image to eMMC \(overwriting Android or other factory-installed images\).

## install2internal

Community images using the legacy Amlogic kernels often include the `install2internal` script to reconfigure the factory boot process and run LibreELEC from the internal eMMC storage. In the past when most box devices had 1GB RAM and SD cards were slow the performance difference between an SD card and "internal" storage was substantial, so the script evolved a cult following and many users belive they _must_ install to internal eMMC or their box will be unusuable. This is wrong advice. On modern boxes with faster CPUs, 2GB+ RAM and better SD card support, the performance difference is often marginal.

The main reason we do not provide or support emmc installs on "box" devices is the high level of support issues. Software and hardware quality in Android STB hardware is not great, and this complicates the process of successfully installing to the internal eMMC storage so many installs have problems resulting in a "bricked" box. Amlogic builds factory-restore mechanisms into their software that mean it is always  possible to recover the box, but this usually depends on finding an Android image for the device, and the process is challenging for less technical users. Our forum staff are all volunteers who give time to the project for fun. Helping a never-ending stream of pissed-off inexperienced users recover bricked boxes is not fun, so we actively discourage the existence and use of this script.

If you want to run LibreELEC from eMMC storage please purchase a "board" device that supports it. If you find and run an `install2internal` script and something messes up; your problem is not our problem!

## emmctool

In the `AMLGX` image there is an eMMC helper script called `emmctool` that supports a range of useful functions for backup/write/erase \(and more\) for eMMC storage. See:

```text
LibreELEC:~ # emmctool 

info: boot device is /dev/mmcblk0, U-boot version is 2021.04
info: emmc device is /dev/mmcblk1

Model: MMC 8WPD3R (sd/mmc)
Disk /dev/mmcblk1: 7818MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name          Flags
 1      17.4kB  7818MB  7818MB  ext4         EMMC_STORAGE

usage: emmctool (w)rite <filename>   : write <filename>.img/.img.gz to the eMMC module
                (b)backup <filename> : dump the emmc partition to an .img.gz file
                (d)etect             : detect an eMMC module attached after boot
                (i)nfo               : show info about the eMMC module
                (l)abels             : change eMMC disk labels to /
                (r)esize             : resize the storage partition to 100%
                (s)storage           : convert emmc for use as /storage (boot from sdcard)
                (u)boot <filename>   : write signed u-boot <filename> to the eMMC module
                (z)ero               : zero (erase/wipe) the eMMC module
                (h)elp               : displays this help message
```

The `emmctool` helper supports a limited range of SBC boards with eMMC modules. On a generic Android device it will output the \(i\) info only.

