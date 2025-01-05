---
description: How to disable the internal screen and force external HDMI/DP/VGA output
---

# Laptops

LibreELEC images are targeted at common HTPC device hardware: typically small form-factor and single-board computer devices that are static/attached to a TV or AVR device. One characteristic of common target hardware is a single active display connection even if the hardware has several output connector options. Laptops are not target hardware devices for the project, but the "Generic" x86\_64 image(s) boot and run on a wide range of x86\_64 devices and some users recycle old laptops as their HTPC. Laptops normally support multiple active display outputs: the internal LCD screen plus one of the following:

* HDMI
* DisplayPort
* VGA

Some laptops can disable the internal screen and force display to an external output using BIOS configuration. Some detect whether the laptop lid is open or closed, and when closed output is automatically directed to an active external connector. Others require run-time switching using keyboard function keys, e.g. Fn+F7 but this needs software support in the OS, and since we are intentionally minimalist LibreELEC does not have that support.

In most cases with multiple display outputs the internal display probes first and display output is directed there. In this scenario users need to manually edit kernel boot parameters to disable the internal display connector, allowing the external connector to be found and used instead.&#x20;

Enable SSH services and SSH into the laptop, then identify output devices:

```shell
LibreELEC:~ # tail /sys/class/drm/*/status
==> /sys/class/drm/card1-eDP-1/status <==
connected

==> /sys/class/drm/card1-HDMI-A-1/status <==
connected
```

The above output shows two display "connectors" are active and connected:

* eDP-1 = the internal screen
* HDMI-A-1 = the external HDMI connector

NB: Some laptops may use "LVDS-1" for the internal screen, and "VGA-1" for the external connector.

Next we make the boot partition writeable so boot parameters can be edited:

```shell
mount -o remount,rw /flash
```

Now we edit them in the nano text editor:

```shell
nano /flash/syslinux.cfg
```

Connectors are disabled by adding `video=` options, e.g. `video=eDP-1:d` will disable the eDP-1 connector allowing HDMI-A-1 to be used instead. In the example below the APPEND line has been modified to disable the eDP-1 connector:

```sh
APPEND boot=UUID=10B4-4A77 disk=UUID=93786be3-5652-e2f498d33733 quiet video=eDP-1:d
```

After adding the details for the connector, you need to disable, save (ctr+o) and exit (ctrl+x) then `reboot` to test the change. On restart, the external connector should be the only active connector and Kodi should appear on the external display device. As the output capabilities of the external connector will be different to the internal screen, you may need to adjust the Kodi desktop resolution in Kodi display settings to match e.g. 720p or 1080p output.
