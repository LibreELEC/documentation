# Blu-ray Playback

No official Blu-ray player software exists for Linux, but open-source libraries allow Kodi (on LibreELEC) to play most Blu-ray discs once `/storage/.config/aacs/KEYDB.cfg` has been populated with a flat-file database of known encryption keys and certificates. To avoid legal issues with DMCA laws in the USA we cannot pre-configure a key database, so you must install this yourself.

Connect to your LibreELEC device via SSH, then run the following commands:

```
mkdir -p /storage/.config/aacs
curl -k -L http://fvonline-db.bplaced.net/fv_download.php?reduced -o /storage/.config/aacs/keydb.zip
unzip /storage/.config/aacs/keydb.zip -d /storage/.config/aacs/
mv /storage/.config/aacs/keydb.cfg /storage/.config/aacs/KEYDB.cfg
rm /storage/.config/aacs/keydb.zip
```

The current file contains PK, HC, VUK and UK data required for attempting disc decryption with more than 170,000 titles. Periodically running the commands will refresh the `KEYDB.cfg` file and add support for new Blu-ray discs.

{% hint style="info" %}
For a more detailed description on how Blu-ray DRM works on Linux, please refer to this article on the Arch Linux wiki: [https://wiki.archlinux.org/index.php/Blu-ray](https://wiki.archlinux.org/index.php/Blu-ray)
{% endhint %}
