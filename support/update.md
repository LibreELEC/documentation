# Updating

Updating LibreELEC is \(mostly\) simple and can be done automatically, manually from inside the Kodi GUI via the LibreELEC Settings Add-On, or by copying an update file to a local Samba share, or from the SSH console.

## Auto-Update

Auto-Update is enabled by default and tracks minor version updates, e.g. LibreELEC 9.2.0 will update to 9.2.1, 9.2.2, etc. as they are released. It will not perform major version updates as these involve Kodi add-on updates and user-experience changes that we do not want to force onto users. If auto-update is on the latest update file will download in the background, then Kodi will prompt you to reboot and start the update process. If you prefer to manage updates manually, auto-update can be disabled in the settings add-on.

## Setttings Add-On

Navigate to the Update section of the settings add-on, select the update channel and then the specific version to update to. The update .tar file will start downloading, and once complete, LibreELEC will reboot to perform the update. Please see the following video:

[https://www.youtube.com/embed/CjlHY6syRHw](https://www.youtube.com/embed/CjlHY6syRHw)

## Samba Share

We publish two image file types; .img.gz files which can be used for new installations and updates, and .tar files which are only used for updates \(and are faster to update from\). Download the update file from [https://libreelec.tv/downloads\_new](https://libreelec.tv/downloads_new) and using a file browser, copy/paste it to the `\\LIBREELEC\UPDATE` Samba share on your LibreELEC device. Once the file transfer has completed, reboot LibreELEC to start the update process.

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

## Downgrade

Kodi does not support downgrading. If "updating" within the same release, e.g. LibreELEC 9.2.6 to 9.2.5, the downgrade will normally work. If downgrading e.g. LibreELEC 9.2.6 to 9.0.2, the downgrade process will complete but Kodi may not gracefully handle being restarted with configuration files and add-ons from the \(newer\) previous release. To successfully downgrade, you must take a backup before updating, then clean install and restore the backup.

## Migrate OpenELEC

In 2016 when we forked from OpenELEC the differences between projects were small and it was deliberately simple to migrate by using our update .tar file. Today there are some challenges:

* LibreELEC images for Generic x86\_64 hardware are now 240MB+ in size and most OpenELEC installs have a 230MB boot partition so LibreELEC files do not fit and the update process will fail \(gracefully\). Raspberry Pi and similar images are smaller and will still fit, but read on:
* Older Kodi add-ons are frequently incompatible with newer Kodi versions, causing crashes when the system restarts. We detect when Kodi repeatedly fails to start and will run with a "safe mode" configuration, but this normally requires SSH console fiddling to get back to a working setup.
* Kodi does not self-manage media and add-on package caches so over-time filesystem litter accumulates. Starting from a clean install removes all the file junk resulting in a faster/lighter installation with an improved Kodi user experience.

To migrate essential Kodi data from the old installation, stop Kodi and back-up the userdata folder:

```text
cd /storage/backup
systemctl stop kodi
tar -czf kodi_userdata.tar.gz /storage/.kodi/userdata
```

Backup files are normally 1-2GB in size so move them off-box by copying to USB or copying from the `\\LIBREELEC\BACKUP` Samba share. After installing LibreELEC you can restore the backup:

```text
systemctl stop kodi
rm -rf /storage/.kodi
tar -xzf /storage/kodi_userdata.tar.gz
systemctl start kodi
```

The following video shows the migration process from OpenELEC v7.0 to LibreELEC v7.0. The Kodi GUI now looks different but the process remains the same:

[https://youtu.be/Tkoxg-Q0Fe8](https://youtu.be/Tkoxg-Q0Fe8)

