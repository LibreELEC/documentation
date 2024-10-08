# Create Media

The “LibreELEC USB-SD Creator” app helps you select and download the latest LibreELEC image for your HTPC device and create bootable USB or SD Card media. The tool is available for current 64-bit Windows 10/11 versions, and macOS v14.x and newer.

Download Links:

* [LibreELEC.USB-SD.Creator.x64.exe](https://releases.libreelec.tv/LibreELEC.USB-SD.Creator.x64.exe) ([sha256](https://releases.libreelec.tv/LibreELEC.USB-SD.Creator.x64.exe?mirrorlist))
* [LibreELEC.USB-SD.Creator.macOS.dmg](https://releases.libreelec.tv/LibreELEC.USB-SD.Creator.macOS.dmg) ([sha256](https://releases.libreelec.tv/LibreELEC.USB-SD.Creator.macOS.dmg?mirrorlist))

Note: The Linux version of the app for Ubuntu and Debian derivatives is not currently available. Please see the note on [#alternatives](create-media.md#alternatives "mention") below.&#x20;

## Step 1

Choose the LibreELEC image and the app will show the latest stable release, e.g.

* Generic x86\_64 - LibreELEC-Generic.x86\_64-12.0.1.img.gz
* RaspberryPi 2/3 - LibreELEC-RPi2.arm-12.0.1.img.gz
* RaspberryPi 4 - LibreELEC-RPi4.arm-12.0.1.img.gz
* RaspberryPi 5 - LibreELEC-RPi5.arm-12.0.1.img.gz

If you select the 'Show all' check-box the drop list includes older stable releases too.

## Step 2

After selecting the LibreELEC image and version click the `Download` button. This will prompt you to select a folder to download to. You can also select a previously downloaded image file on your machine for installation. Hit the `Select file` button and browse your computer for the .img.gz file, or drag/drop the file on the app GUI.

## Step 3

Select the USB stick or SD card to write the image onto. Click the refresh button if the removable device is not listed. Note: **ALL DATA ON THE TARGET DEVICE WILL BE OVERWRITTEN** so please ensure there is no important data on it!

## Step 4

After selecting an image and target device the `Write` button is available. Click it to write the image. Once the progress bar reaches 100% and shows `Writing done!` you can exit the app, eject the USB or SD media, and boot your device to install LibreELEC.

## Support

If you have problems with the app, please post in the [USB-SD Creator Support forum](https://forum.libreelec.tv/forum-41.html).

### Alternatives

Here are some alernative tools that can be used for creating LibreELEC boot media:

* [Raspberry Pi Imager](https://www.raspberrypi.com/software/) can create Raspberry Pi images using files from our servers
* [Balena Etcher](https://etcher.balena.io/) is popular but huge (almost the same size as our distro images!)
* [Win32DiskImager](https://win32diskimager.org/) is a reliable tool for Windows OS devices
* [dd](https://linux.die.net/man/1/dd) is available from most Linux OS command lines&#x20;
