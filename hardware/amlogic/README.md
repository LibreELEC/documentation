# Amlogic

Current LibreELEC 10.0+ images for Amlogic using modern Linux kernels use boot processes and device-tree files that are not compatible with older LibreELEC images that use Amlogic Linux 3.14 or 4.9 kernels. The change to boot processes means you cannot update from older releases and must make a new/clean installation.

There are two images supporting Amlogic Gen10+ (64-bit) SoCs and older Gen8 (32-bit) SoCs used in a range of Linux SBC and Android STB devices:

`AMLGX` supports the following 64-bit SoCs:

* GXBB (S905)
* GXL (S805X/S905X/D/W/L)
* GXM (S912)
* G12A (S905X2/D2/Y2) - experimental
* G12B (S922X/A311D) - experimental
* SM1 (S905X3/D3) - experimental

`AMLMX` (not currently available) supports the following 32-bit SoCs:

* Meson 8 (S805)
* Meson 8b (S802)
* Meson 8m2 (S812)

NB: The WeTek Play(1)/OpenELEC box uses Meson 6 (8726MX) hardware. There is little support for Meson 6 hardware in the upstream kernel and low probability of support evolving to the point where modern-kernel LibreELEC images are viable.

`AMLGX` and `AMLMX` provide a "box" image for use with devices that run Amlogic (aka Vendor or Legacy) boot firmware (U-Boot 2015.01 with Amlogic and manufacturer customisations) and "board" images using modern boot firmware (mainline U-Boot) specific to a single SBC board or STB device. The image type can be identified by the filename `-suffix`:

* `LibreELEC-AMLGX.arm-10.0.0-box.img.gz` is the `AMLGX` "box" image
* `LibreELEC-AMLGX-arm-10.0.0-khadas-vim3.img.gz` is a "board" image for VIM3
* `LibreELEC-AMLMX-arm-10.0.0-box.img.gz` is a "box" image for Meson 8 devices

## Box Images

Box images support SBC and STB devices with Android or "vendor" boot firmware running on the internal eMMC storage. LibreELEC is installed by triggering "recovery" mode boot in the Amlogic U-Boot firmware. Recovery mode searches for some standard files on SD and USB media. LibreELEC provides files tweaked to boot and run LibreELEC instead of recovering the device. Once recovery mode is activated the device will search and find LibreELEC on each boot; until Android recovery completes (which never happens).

As `box` images can be used on many devices you must configure the device-tree file to use first. This is done by editing `uEnv.ini` in the root folder of the SD card. Change `@@DTB_NAME@@` to the name of the .dtb file to use. Current supported device-tree files are in the `dtb` folder.

For example, here is the default `uEnv.ini` file:

```
dtb_name=/dtb/@@DTB_NAME@@
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

To boot a Beelink GT-King box change `@@DTB_NAME@@` to `meson-g12b-gtking.dtb`

```
dtb_name=/dtb/meson-g12b-gtking.dtb
bootargs=boot=UUID=2306-0801 disk=UUID=8268da37-3a8d-4f6d-aba0-08918faded56 quiet systemd.debug_shell=ttyAML0 console=ttyAML0,115200n8 console=tty0
```

Once the device-tree name is set you can insert the SD card in the box and power on. Some box devices detect the presence of the SD card automatically. Others may need recovery mode to be triggered using a reset button on the device. Common locations for the button are:

* Visible button marked "reset" or "recovery" or "power" button
* Visible pin-hole on the underside of the case
* Hidden button visible through ventilation holes in the case
* Hidden at the end of the 3.5mm audio jack

In most cases you will need a small pin, unfolded paper-clip, or toothpick to press the reset button with - hence the install process is often referred to as the "toothpick method" in forum posts. Press and hold the button, then power-on the box. After 5-7 seconds release the button to interrupt boot and start the recovery process. Due to differences in box speeds and vendor U-Boot the exact timing for button release varies and you will need to experiment to find the timing that works for your box. It is possible to see U-Boot output and remove the guesswork by attaching a UART serial cable to the board, although most set-top box devices will need connector pins soldering to the board as manufacturers omit them to save manufacturing costs.

## Board Images

These images are built for Single Board Computer (SBC) devices which boot modern U-Boot via an SD card or removable eMMC module. Installation is normally simple requiring you to write the image to the SD card or eMMC module and boot the device. If the board has eMMC storage soldered (not on a removable module) it may be necessary to boot from the "box" image first. Once booted to a box image on SD card (so eMMC is not in use) you can write the correct `board` image to eMMC (overwriting Android or other factory-installed images) using `dd` to write the image. You can use the `emmctool` command to help that process (see below).

## Internal Installs (Android Boxes)

Images using legacy Amlogic 3.14 or 4.9 Linux kernels often included the `install2internal` script to reconfigure and run LibreELEC from internal eMMC storage. <mark style="color:red;">**The boot process and files used in**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**`AMLGX`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**images are not compatible with the script**</mark>. If you are lucky the script will simply fail to run. If you are unlucky it will corrupt boot files and "brick" the box. If this happens you will need to use Amlogic Burning Tool and the factory Android ROM image to recover your box.

We do not support the script, or Amlogic Burning Tool, and we do not have a secret collection of Android ROM images to help with when things go wrong (and they do go wrong). <mark style="color:red;">**If you attempt internal installation and brick your Android box it will be your problem to solve. Do not expect support or sympathy from the forum staff.**</mark>

Note: Amlogic builds factory-restore mechanisms into their software and hardware that mean it is almost always possible to recover "bricked" boxes, but the process is often challenging for less technical users (and experienced ones). Our forum staff are all volunteers who give their time to the project for fun. Helping a never-ending stream of irate and pissed-off users recover "bricked" Android boxes is not fun, so we do not support internal installs.

## Internal Installs (Boards)

The `AMLGX` image contains a helper script called `emmctool` that supports a range of useful functions for backup, writing, erasing (and more) with eMMC storage. See:

```
LibreELEC:~ # emmctool 

info: boot device is /dev/mmcblk0, U-Boot version is 2021.04
info: emmc device is /dev/mmcblk1

Model: MMC 8WPD3R (sd/mmc)
Disk /dev/mmcblk1: 7818MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name          Flags
 1      17.4kB  7818MB  7818MB  ext4         STORAGE

usage: emmctool (w)rite <filename>   : write <filename>.img/.img.gz to the eMMC module
                (b)backup <filename> : dump the emmc partition to an .img.gz file
                (d)etect             : detect an eMMC module attached after boot
                (i)nfo               : show info about the eMMC module
                (l)abels             : change eMMC disk labels to /
                (r)esize             : resize the storage partition to 100%
                (s)storage           : convert emmc for use as /storage (boot from sdcard)
                (z)ero               : zero (erase/wipe) the eMMC module
                (h)elp               : displays this help message
```

The `emmctool` helper supports a specific and limited range of SBC boards with eMMC modules and a small number of Android TV boxes that have [sources](https://github.com/LibreELEC/amlogic-boot-fip) to create the signed u-boot firmware needed to boot Amlogic boards.

If used on a generic Android box devices `emmctool` will output the (i) info only.

<mark style="color:red;">**Do not attempt to write board images to Android boxes.**</mark> The signed u-boot firmware in the image will not work in 99/100 cases and recovering the box from a "bricked" state is complicated. <mark style="color:red;">**Do not expect support or sympathy from the forum staff if you do this.**</mark>
