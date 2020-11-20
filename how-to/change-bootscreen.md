# Change Bootscreen

* Upload the custom image to the network share \(yoursplash.png to the download folder\).
* Connect with a SSH client to your LibreELEC box.
* mount boot partition

```text
mount -o remount,rw /flash
```

* copy your oemsplash.png

```text
cp /storage/downloads/yoursplash.png /flash/oemsplash.png
```

* unmount boot partition

```text
mount -o remount,ro /flash
```

At the next boot you should see the new bootscreen.

