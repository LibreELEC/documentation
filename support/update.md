# Updating

Updating LibreELEC is \(mostly\) simple and can be done automatically, manually from inside the Kodi GUI via the LibreELEC Settings add-on, by [downloading an update file from our website](https://libreelec.tv/downloads_new) and copying the file to a local Samba share, or by running a command from the SSH console. We publish two image file types:

* LibreELEC-Project.arch-9.6.7**.tar** \(update\) files which are used for updates only.
* LibreELEC-Project.arch-9.6.7**.img.gz** \(image\) files which are used for creating new USB/SD installation media but can also be used for updates.

We can update from both **.tar** and **.img.gz** files, but .tar files are faster to process.

## Auto-Update

Auto-Update is enabled by default and tracks minor version updates, e.g. LibreELEC 9.2.0 will update to 9.2.1, 9.2.2, etc. as they are released. It will not perform major version updates as these involve Kodi add-on updates \(broken add-ons are the main source of update issues\) and user-experience changes we do not want to force onto users. If auto-update is enabled the latest update file will download in the background, then Kodi will prompt you to reboot and start the update process. If you prefer to manage system updates manually, auto-update can be disabled in the settings add-on.

## Setttings Add-On

Navigate to the Update section of the settings add-on, select the update channel \(LibreELEC major version\) and then the specific minor version to update to. The update .tar file will start downloading, and once complete the system will reboot to perform the update.

Please see the following video

{% embed url="https://www.youtube.com/watch?v=CjlHY6syRHw" caption="" %}

## Samba Share

Using a file browser \(explorer.exe, not a web browser\) copy/paste an update file to the `\\LIBREELEC\UPDATE` Samba share on your LibreELEC device. Once the file transfer has completed, reboot LibreELEC to start the update process.

## SSH Console

SSH into your LibreELEC device and navigate to the \(hidden\) update folder

```text
cd /storage/.update
```

Check to make sure there are no files in the folder

```text
ls -la
```

Download the update file \(change the URL for the current release, etc.\)

```text
wget http://releases.libreelec.tv/LibreELEC-RPi4.arm-9.2.6.img.gz
```

Once the download has completed, reboot to start the update process

```text
reboot
```

## USB Drive

Download the LibreELEC image or update file and copy it to a USB drive. Connect the USB drive to your HTPC and navigate to the `/storage/.update/` update folder using Kodi File Manager \(you will need to enable viewing of hidden files and folders in Kodi settings\). Copy the update file from the USB drive to `/storage/.update` then reboot to start the update process.

## Downgrade

Kodi does not support downgrading. If "updating" within the same LibreELEC/Kodi major release, e.g. LibreELEC 9.2.6 to 9.2.5, the downgrade will normally work. If downgrading to an older LibreELEC/Kodi major version e.g. LibreELEC 9.2.6 to 9.0.2, the downgrade process will complete but Kodi may not gracefully handle being restarted with configuration files and add-ons from the \(newer\) previous release. Always make a backup before upgrading so that you can clean install then restore the backup to effect a downgrade without problems.

## Migrate OpenELEC

In 2016 when we forked from OpenELEC the differences between projects were small and it was simple to migrate by using our update .tar file. Today there are some challenges:

* LibreELEC images for Generic x86\_64 hardware have grown to 240MB+ in size and most older OpenELEC installs have a small 230MB boot partition so LibreELEC files will not fit and the manaul .tar update process will fail \(gracefully\). Raspberry Pi and similar images are smaller and will still fit, but read on:
* Older Kodi add-ons are frequently incompatible with newer Kodi versions, causing crashes when the system restarts, and the bigger the version jump the more likely add-ons will cause problems. LibreELEC will detect when Kodi repeatedly fails to start and defaults to a "safe mode" configuration, but this normally requires you to access the SSH console to get back to a working setup.
* Kodi does not self-manage media and add-on package caches so over-time filesystem litter accumulates. Starting over from a clean install removes all the not-needed junk resulting in a faster/lighter installation with an improved Kodi user experience.

To migrate essential Kodi data from the old installation before migrating \(which is likely to fail\) or making a clean installation, enable the SSH service in the Settings add-on, then log-in over SSH to stop Kodi and back-up the userdata folder:

```text
cd /storage/backup
systemctl stop kodi
tar -czf kodi_userdata.tar.gz /storage/.kodi/userdata
```

Backup files are normally 1-2GB in size so move them off-box by copying to USB or moving them to `/storage/backup` which is accessible over the network via the `\\LIBREELEC\BACKUP` Samba share. After clean installing LibreELEC you can restore the backup:

```text
systemctl stop kodi
rm -rf /storage/.kodi
tar -xzf /storage/kodi_userdata.tar.gz
systemctl start kodi
```

The following video shows the migration process from OpenELEC v7.0 to LibreELEC v7.0 as it looked in 2016. The Kodi GUI now looks different \(the skin changed\) but the process remains the same: [https://youtu.be/Tkoxg-Q0Fe8](https://youtu.be/Tkoxg-Q0Fe8)

