# Raspberry Pi (config.txt)

The `config.txt` file is used to configure boot and hardware configuration on Raspberry Pi hardware, similar to how the "BIOS" is used on an Intel PC.

## Edit via SSH

The `/flash` boot partition is read-only by default, so we need to remount it in read-write mode:

```
mount -o remount,rw /flash
```

Use the `nano` text editor to modify the file. Save changes with `ctrl+o` and exit using `ctrl+x`:

```
nano /flash/config.txt
```

After editing set the `/flash` partition back to read-only mode:

```
mount -o remount,ro /flash
```

And reboot for the changes in `config.txt` to be applied:

```
reboot
```

## Edit via Card Reader

Turn off the Raspberry Pi, remove the SD card, and use a card reader connected to your Windows, Linux, or macOS computer. If using Windows, use a text editor capable of saving text files in Unix format, e.g. [Notepad++](https://notepad-plus-plus.org/downloads) or Wordpad.exe (not Notepad.exe).

## MPEG2 and VC-1 Licenses

* Raspberry Pi 0/1/2 cannot hardware-decode MPEG2/VC-1 media files without a license key that can be purchased from the [Raspberry Pi Foundation](http://www.raspberrypi.com/license-keys). The hardware is too slow to software decode these formats without the keys.

* Raspberry Pi 3 hardware has higher spec CPUs and is capable of software-decoding without the licenses, although purchasing them allows hardware-decoding and your device will run cooler. 

* Raspberry Pi 4 is capable of software decoding these formats and license keys are not available.

To purchase license keys you need the serial number of your Raspberry Pi. This can be read with the following command:

```
cat /proc/cpuinfo
```

Edit `config.txt` as described above, uncommenting the license key lines by removing the `#` marks, and replacing `00000000` with the keys you received after purchasing:

```
decode_MPG2=0x00000000
decode_WVC1=0x00000000
```

Reboot and confirm the license keys are installed correctly by running these commands:

```
vcgencmd codec_enabled MPG2
vcgencmd codec_enabled WVC1
```

If the licenses are enabled you should see this:

```
MPG2=enabled
WVC1=enabled
```

If any key that you purchased shows `disabled` check that:

* You entered the correct serial number
* You added the licence key to `config.txt` correctly
* You uncommented the lines in `config.txt` by removing the `\#` mark (and space)
* You rebooted before testing

## Overlays

Raspberry Pi kernels use a board-specific device-tree file to describe the board's hardware and how things are connected. As the Raspberry Pi is designed to be extended with extra hardware via HATs and peripheral connectors the kernel also supports device-tree "overlay" files. When configured, these overlay "fragments" of device-tree content that describe the extra hardware on the board device-tree, and the the kernel reads them as a single combined file.

Overlays can be found in `/flash/overlays` and they are configured in `config.txt` in the root folder of the SD card that you boot the Raspberry Pi from. The following are examples of common overlay changes.

Audio Interfaces:

```
dtoverlay=hifiberry-dac
dtoverlay=hifiberry-dacplus
dtoverlay=hifiberry-digi
dtoverlay=hifiberry-amp
dtoverlay=iqaudio-dac
dtoverlay=iqaudio-dacplus
```

Infra-Red Receivers:
```
dtoverlay=gpio-ir
```

Override defaults for the gpio-ir module:
```
dtoverlay=gpio-ir,gpio_pin=25,gpio_pull=up,rc-map-name=rc-technisat-usb2
```

Optional hardware interfaces:
```
dtparam=i2c1=on  # for later RPi's
dtparam=i2c0=on  # for early RPi's
dtparam=spi=on
```

## Overclocking

Please see [the Raspberry Pi Foundation Wiki](http://elinux.org/RPiconfig#Overclocking) for more details on overclocking.
