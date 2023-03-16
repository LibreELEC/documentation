---
description: Installing LibreELEC to internal eMMC storage
---

# WeTek Hub/Play2

Create a bootable SD card using the `AMLGX` "box" image, then edit the `uEnv.ini` file in the root folder of the SD card and change `@@DTB_NAME@@` to `meson-gxbb-wetek-hub.dtb` or `meson-gxbb-wetek-play2.dtb` then save/eject and boot the box using the SD card. If you have been booting from SD card before it should automatically detect the card and boot into LibreELEC. If you have been booting from eMMC you may need to select "boot from SD Card" during boot.

Once the box has booted into the "box" image you can download the latest WeTek "board" image and install LibreELEC to internal eMMC storage using `emmctool`. <mark style="color:red;">**This permanently overwrites the Android vendor OS with LibreELEC.**</mark>

Find the URL for the relevant board image on the [download page](https://libreelec.tv/downloads/amlogic/). For example, the LibreELEC v11 beta 1 image for the WeTek Play2 will be:

```
https://releases.libreelec.tv/LibreELEC-AMLGX.arm-11.0.0-wetek-play2.img.gz
```

Enable SSH and login, then run the following commands to download the board image to /storage and run `emmctool` to write (`w`) the `LibreELEC-AMLGX.arm-11.0.0-wetek-play2.img.gz` image to eMMC:

```
cd /storage
wget https://releases.libreelec.tv/LibreELEC-AMLGX.arm-11.0.0-wetek-play2.img.gz
emmctool w LibreELEC-AMLGX.arm-11.0.0-wetek-play2.img.gz
```

`The emmctool` utility will write the board image to eMMC, expand the /storage partition to 100% size and change the partition labels to `BOOT` and `DISK` to avoid clashing with the default labels used in the SD card image (so you can boot from SD card if needed).

Once `emmctool` has finished, run `shutdown` and remove the SD card. Then cycle power to boot into LibreELEC running from the internal storage.

