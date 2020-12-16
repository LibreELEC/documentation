# Change Bootsplash

LibreELEC displays the project logo as a background "splash" screen during boot. This can be changed to your own image by placing `oemsplash.png` in the boot partition.

Copy your splash image to the Downloads folder, then SSH into your LibreELEC box. Next we must remount the boot partition in read-write mode so we can make changes:

```text
mount -o remount,rw /flash
```

Copy your splash image to the boot partition, renaming it to `oemsplash.png`

```text
cp /storage/downloads/yoursplash.png /flash/oemsplash.png
```

Remount the boot partition in read-only mode:

```text
mount -o remount,ro /flash
```

On reboot you should see the new bootsplash.

### Splash Image Requirements

Splash images must be a 32-bit png files with 1920 x 1080, 1280 x 720, or 1024 x 768 pixel dimensions. Images will be upscaled to the active display resolution, so it is best to use the largest resolution \(but not 4k, as most boot firmware does not support 4k output\). Most OS image editors do not support 32-bit png export, so we recommend the excellent and open-source [GIMP](https://www.gimp.org) editor for making boot splashes.

