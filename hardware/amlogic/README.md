# Amlogic

Current LibreELEC 10.0+ images for Amlogic using modern Linux kernels use boot processes and device-tree files that are not compatible with older LibreELEC images that use Amlogic Linux 3.14 or 4.9 kernels. The change to boot processes means you cannot update from older releases and must make a new/clean installation.

There are two images supporting Amlogic Gen10+ (64-bit) SoCs and older Gen8 (32-bit) SoCs used in a range of Linux SBC and Android STB devices:

`AMLGX` supports the following 64-bit SoCs:

* GXBB (S905)
* GXL (S805X/S905X/D/W/L)
* GXM (S912)
* G12A (S905X2/D2/Y2)
* G12B (S922X/A311D)
* SM1 (S905X3/D3)

`AMLMX` supports the following 32-bit SoCs:

* Meson 8 (S805)
* Meson 8b (S802)
* Meson 8m2 (S812)

NB: The WeTek Play(1)/OpenELEC box uses Meson 6 (8726MX) hardware. There is little support for Meson 6 hardware in the upstream kernel and low probability of support evolving to the point where modern-kernel LibreELEC images are viable.

`AMLGX` and `AMLMX` provide a "box" image for use with devices that run Amlogic (aka Vendor or Legacy) boot firmware (U-Boot 2015.01 with Amlogic and manufacturer customisations) and "board" images using modern boot firmware (mainline U-Boot) specific to a single SBC board or STB device. The image type can be identified by the filename `-suffix`:

* `LibreELEC-AMLGX.arm-10.0.0-box.img.gz` is the `AMLGX` "box" image
* `LibreELEC-AMLGX-arm-10.0.0-khadas-vim3.img.gz` is a "board" image for VIM3
* `LibreELEC-AMLMX-arm-10.0.0-box.img.gz` is a "box" image for Meson 8 devices

## Box Images

Box images support SBC and STB devices with Android or "vendor" boot firmware running on the internal eMMC storage. LibreELEC is installed  by triggering "recovery" mode boot in the Amlogic U-Boot firmware. Recovery mode searches for some standard files on SD and USB media. LibreELEC provides files tweaked to boot and run LibreELEC instead of recovering the device. Once recovery mode is activated the device will seach (and find LibreELEC) on each boot; until Android recovery completes (it never does).

As `box` images can be used on many devices you must configure the device-tree file to use first. This is done by editing `uEnv.ini` in the root folder of the SD card. Change `@@DTB_NAME@@` to the name of the .dtb file to use. Current supported device-tree files are in the `dtb` folder.

For example, here is the default `uEnv.ini` file:

```
dtb_name=/dtb/@@DTB_NAME@@
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

To boot a Beelink GT-King box change `@@DTB_NAME` to `meson-g12b-gtking.dtb`

```
dtb_name=/dtb/meson-g12b-gtking.dtb
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

Once the device-tree name is set you can insert the SD card in the box and power on. Some box devices will detect the presence of the SD card automatically. Others need you to trigger recovery mode using a reset button on the device. Common locations for the button are:

* Visible button marked "reset" or "recovery" or "power" button
* Visible pin-hole on the underside of the case
* Hidden button visible through ventilation holes in the case
* Hidden at the end of the 3.5mm audio jack

In most cases you will need a small pin, unfolded paper-clip, or wooden toothpick to press the reset button with - hence the install process is often referred to as the "toothpick method" in forum posts. Press and hold the button, then power-on the box. After 5-7 seconds release the button to interrupt boot and start the recovery process. Due to differences in box speeds and vendor u-boot customisations the exact timing for button release varies and you will need to experiment to find the timing that works for your board. It is possible to see U-Boot output and remove the guesswork by attaching a UART serial cable to the board, although most STB box devices will need connector pins soldering to the board as most manufacturers omit them to save some pennies.

## Board Images

These images are built for Single Board Computer (SBC) devices which boot modern u-boot via an SD card or removable eMMC module. Installation is normally simple requiring you to write the image to the SD card or eMMC module then boot the device. If the board has eMMC storage soldered (not on a removable module) it may be necessary to boot from the "box" image first. Once booted to a box image on SD card (so eMMC is not in use) you can write the correct `board` image to eMMC (overwriting Android or other factory-installed images).

## install2internal

Community images using the legacy Amlogic kernels often include the `install2internal` script to reconfigure the factory boot process and run LibreELEC from the internal eMMC storage. In the past when most box devices had 1GB RAM and SD cards were slow the performance difference between an SD card and "internal" storage was substantial, so the script evolved a cult following and many users belive they _must_ install to internal eMMC or their box will be unusuable. This is wrong advice. On modern boxes with faster CPUs, 2GB+ RAM and better SD card support, the performance difference is often marginal.

The main reason we do not provide or support emmc installs on "box" devices is the high level of support issues. Software and hardware quality in Android STB hardware is not great, and this complicates the process of successfully installing to the internal eMMC storage so many installs have problems resulting in a "bricked" box. Amlogic builds factory-restore mechanisms into their software that mean it is always  possible to recover the box, but this usually depends on finding an Android image for the device, and the process is challenging for less technical users. Our forum staff are all volunteers who give time to the project for fun. Helping a never-ending stream of pissed-off inexperienced users recover bricked boxes is not fun, so we actively discourage the existence and use of this script.

If you want to run LibreELEC from eMMC storage please purchase a "board" device that supports it. If you find and run an `install2internal` script and something messes up; your problem is not our problem!

## emmctool

In the `AMLGX` image there is an eMMC helper script called `emmctool` that supports a range of useful functions for backup/write/erase (and more) for eMMC storage. See:

```
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

The `emmctool` helper supports a limited range of SBC boards with eMMC modules. On a generic Android device it will output the (i) info only.
