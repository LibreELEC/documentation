# Hyperion

<mark style="color:red;">WARNING: Hyperion does not support the GBM/V4L2 video pipeline used in LibreELEC 10.x and 11.x: including all Raspberry Pi and Generic x86\_64 (GBM) releases. Users with x86\_64 hardware can update to the Generic\_Legacy (X11) image. Users with Raspberry Pi hardware must use an external grabber hardware device or remain on older releases. Changes to add GBM support to Hyperion are tracked here:</mark> https://github.com/hyperion-project/hyperion.ng/pull/1422

[Hyperion](https://github.com/hyperion-project/hyperion) is an open source project that provides an ambient lighting system, compatible with Kodi. Hyperion requires hardware and software configuration. This page describes the process to add Hyperion to Generic x86\_64 and Raspberry Pi hardware. For software configuration please head over to [Hypercon](hypercon.md).

## Adalight

If you want hardware that will work on any LibreELEC device, an "Adalight" setup is the best choice. It works by sending serial data to an Arduino micro controller which controls the LEDs. You need:

* Arduino (Uno, nano, etc)
* WS2801 LED strand
* 5V power supply
* Wire/Jumpers
* Breadboard/Protoboard (optional)
* Soldering iron (optional)

![Adalight wiring](<../.gitbook/assets/hyperion-adalight (1).png>)

Follow the tutorial here [https://learn.adafruit.com/adalight-diy-ambient-tv-lighting/overview](https://learn.adafruit.com/adalight-diy-ambient-tv-lighting/overview).

In [Hypercon](hypercon.md) you will need to enable:

![Hypercon configuration Adalight](../.gitbook/assets/hyperion-config-adalight.png)

## Raspberry Pi

Pi devices can drive an SPI output directly, and hyperion can output to an spidev device. You need:

* Raspberry Pi
* WS2801 LED strand
* 5V power supply
* Wire/Jumpers

![Raspberry Pi wiring](../.gitbook/assets/hyperion-rpi.png)

You can find the pinout here [https://pinout.xyz](https://pinout.xyz). You will need to connect the following pins:

```
19 MOSI -> Data
23 SCLK -> Clock
25 GND  -> Ground
```

You need to enable SPI in the config.txt file:

```
mount -o remount,rw /flash
nano /flash/config.txt
```

Add the following line to the end of the file:

```
dtparam=spi=on
```

Then reboot. In [Hypercon](hypercon.md) you will need to enable:

![Hypercon configuration Raspberry Pi](<../.gitbook/assets/hyperion-config-rpi (1).png>)
