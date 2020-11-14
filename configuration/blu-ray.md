# Blu-Ray Playback

No official Blu-Ray player software exists for Linux, but open-source libraries we bundle into the OS allow Kodi \(on LibreELEC\) to play most Blu-Ray discs once `/storage/.config/aacs/KEYDB.cfg` has been populated with a flat-file database of known keys and certificates. To avoid legal issues we cannot pre-populate the file with a key database, so you must install this yourself.

Connect to your LibreELEC device via SSH, then run the following command:

```text
mkdir -p /storage/.config/aacs
curl -k https://vlc-bluray.whoknowsmy.name/files/KEYDB.cfg -o /storage/.config/aacs/KEYDB.cfg
```

The file contains PK, HC and VUK data required for attempting disc decryption process for more than 24,000 titles. Re-running the `curl` command periodically will refresh `KEYDB.cfg` and add support for new Blu-Ray discs.

For a detailed description on how Blu-Ray DRM works on Linux, please refer to this article on the Arch Linux wiki: [https://wiki.archlinux.org/index.php/Blu-ray](https://wiki.archlinux.org/index.php/Blu-ray)

