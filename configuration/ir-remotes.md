# Infra-Red Remotes

Like mst current Linux distros LibreELEC uses `ir-keytable` to configure Infra-Red Remotes. Each IR receiver kernel driver installs a default `keytable` which specifies the IR protocol to use, e.g. RC5, RC6, NEC, and the scancode to Linux keycode mappings.

Most universal receivers work with the rc-rc6-mce table so RC6 MCE remotes can be used without further configuration. Drivers for DVB devices sold with a remote usually install their own keytable, e.g. the Hauppauge remote that came with a Hauppauge DVB stick.

After the kernel driver is loaded `ir-keytable -a` is automatically run to change the kernel default configuration. First `/etc/rc_maps.cfg` determines which keytable to load. Then the corresponding keytable file from `/usr/lib/udev/rc_keymaps` is used to configure the remote control driver.

As most of the LibreELEC filesystem is read-only, we use a system of boot-time file overrides to read user-created configuration from the persistent `/storage` area:

* `/storage/.config/rc_maps.cfg` overrides `/etc/rc_maps.cfg`.
* `/storage/.config/rc_maps.cfg.sample` has simple examples.
* `/storage/.config/rc_keymaps overridess`/usr/lib/udev/rc\_keymaps\`.
* `/etc/rc_maps.cfg` uses the `libreelec_multi` keytable instead of rc6\_mce.

The default `libreelec_multi` keytable allows us to support Xbox 360/One remotes "out of box" in addition to MCE/RC6 remotes. This is configured via the following change:

```text
# use combined multi-table on MCE receivers
#*      rc-rc6-mce               rc6_mce
*       rc-rc6-mce               libreelec_multi
```

## Can I use?

While most IR receivers can be used with a large variety of remotes the answer to “Can I use remote X with IR receiver Y?” depends on many factors:

* Some IR receivers cannot be configured and you can only use the remote they came with.
* Some receivers only support a subset of protocols, e.g. only RC5, not RC6.
* If a remote uses a protocol not supported by the IR receiver, or a protocol not supported by the Linux kernel, the last option is the RAW \(lirc\) protocol which allows userspace LIRC to configure it via a custom `lirc.conf` file.

## Configuration \(Easy\)

LibreELEC includes 100+ remote keytable files from the Linux kernel so there is a good chance your remote has a known configuration, or a partially working keytable can provide a starting point for adding the missing buttons \(see the "Hard" section\).

1. Look at the keytable files in `/usr/lib/udev/rc_keymaps/`.
2. If one of the filenames suggests it could match your remote, try using it.
3. If it doesn't work, keep trying..

To test a different keytable file run `ir-keytable -c -w /path/to/keytable-file` e.g.

```text
LibreELEC:~ # ir-keytable -c -w /usr/lib/udev/rc_keymaps/samsung
Read samsung table
Old keytable cleared
Wrote 30 keycode(s) to driver
Protocols changed to nec
```

If the output ends with these lines the protocol is not supported by the IR receiver:

```text
Invalid protocols selected
Couldn't change the IR protocols
```

If the keytable loaded without errors press the up, down, left, right and OK buttons to see if navigation in Kodi works?

If you find a working keytable file the config can be made persistent by creating `/storage/.config/rc_maps.cfg` with the name of the keytable, e.g. if the `samsung` keytable works the file with the following content:

```text
* * samsung
```

Running `ir-keytable -a /storage/.config/rc_maps.cfg` and the output should look like:

```text
LibreELEC:~ # ir-keytable -a /storage/.config/rc_maps.cfg
Old keytable cleared
Wrote 30 keycode(s) to driver
Protocols changed to nec
```

Test the buttons work again, and if all is okay, reboot.

## Configuration \(Hard\)

If you cannot find a working keymap or the keymap has misssing buttons you can create your own. The keytable file is plain text with a simple format. The first line contains a header with a descriptive name \(free text, but avoid special characters and spaces\) and the remote protocol \(important!\). This is followed by lines that map remote scancode to Linux keycode. A typical keytable file looks like this:

```text
# table justboom, type: RC5
0x101a KEY_UP
0x101b KEY_DOWN
0x1013 KEY_LEFT
0x1014 KEY_RIGHT
0x1015 KEY_OK
```

To capture the keycodes you must stop Kodi and eventlircd first, or these services capture IR input and you will see no output from `ir-keytable`:

```text
systemctl stop kodi
systemctl stop eventlircd
```

Next we have to identify the IR protocol. If you found a partially working keytable file \(with the protocol listed in the header\) you can skip this step. First we run `ir-keytable` to find out which protocols tthe receiver driver supports. Look at the `Supported protocols:` line, e.g.

```text
LibreELEC:~ # ir-keytable
Found /sys/class/rc/rc0/ (/dev/input/event1) with:
        Driver gpio-rc-recv, table rc-rc6-mce
        Supported protocols: lirc rc-5 rc-5-sz jvc sony nec sanyo mce_kbd rc-6 sharp xmp
        Enabled protocols: lirc nec rc-6
        Name: gpio_ir_recv
        bus: 25, vendor/product: 0001:0001, version: 0x0100
        Repeat delay = 500 ms, repeat period = 125 ms
```

Run `ir-keytable -p PROTOCOL -t` and press buttons on the remote. If you discover the correct protocol you will see `EV_MSC` events and the scancode of the button pressed, e.g.

```text
LibreELEC:~ # ir-keytable -p rc-5 -t
Protocols changed to rc-5
Testing events. Please, press CTRL-C to abort.
1503592437.660155: event type EV_MSC(0x04): scancode = 0x101a
1503592437.660155: event type EV_SYN(0x00).
1503592437.774129: event type EV_MSC(0x04): scancode = 0x101a
1503592437.774129: event type EV_SYN(0x00).
1503592437.921009: event type EV_MSC(0x04): scancode = 0x101a
```

If you see no events stop ir-keytable with `CTRL-C` and try another protocol from the list. You can ignore `lirc` as this is not a real protocol.

Once you find the correct IR protocol create `/storage/.config/rc_keymaps/custom_remote` and set the header file. In the example above the protocol is `rc-5` so we set:

```text
# table custom_remote, type: rc-5
```

If you found a partially working keytable, clone it and then edit the header to say `custom_remote`:

```text
cp /usr/lib/udev/rc_keymaps/samsung /storage/.config/rc_keymaps/custom_remote
```

Next we capture the scancodes and document the keycode mapping. Some users find it easiest to open two SSH connections; one to see ir-keytable scancode output in, and one so they can copy/paste scancodes directly into the `custom_remote` keytable file, e.g. in one open the `nano` editor:

```text
nano /storage/.config/rc_keymaps/custom_remote
```

And in the second run `ir-keytable -t` to find out the scancode of each button. For each button do the following:

* Press a button and note the scancode \(the 0x… value after scancode:\)
* Add a new line with the scancode \(including 0x\).
* Add the Linux keycode, separated with a blank.

You can get a list of all supported Linux keycodes via `irrecord -l | grep ^KEY` but it is easiest to use keycodes listed in the `<remote device=“devinput”>` section of `/usr/share/kodi/system/Lircmap.xml` else you must also create a Kodi `lircmap.xml` with Linux keycode to action mappings.

The table below has a selection of common keycodes:

| Keycode | Keycode | Keycode | Keycode |
| :--- | :--- | :--- | :--- |
| KEY\_LEFT | KEY\_STOPCD | KEY\_INFO | KEY\_TEXT |
| KEY\_RIGHT | KEY\_FASTFORWARD | KEY\_PROPS | KEY\_1 |
| KEY\_UP | KEY\_FORWARD | KEY\_ZOOM | KEY\_2 |
| KEY\_DOWN | KEY\_REWIND | KEY\_ANGLE | KEY\_3 |
| KEY\_OK | KEY\_VOLUMEUP | KEY\_MUTE | KEY\_4 |
| KEY\_ENTER | KEY\_VOLUMEDOWN | KEY\_POWER | KEY\_5 |
| KEY\_SELECT | KEY\_CHANNELUP | KEY\_SLEEP | KEY\_6 |
| KEY\_DELETE | KEY\_CHANNELDOWN | KEY\_WAKEUP | KEY\_7 |
| KEY\_ESC | KEY\_PAGEUP | KEY\_EJECTCD | KEY\_8 |
| KEY\_MEDIA | KEY\_PAGEDOWN | KEY\_EJECTCLOSECD | KEY\_9 |
| KEY\_HOME | KEY\_NEXT | KEY\_DVD | KEY\_0 |
| KEY\_EXIT | KEY\_NEXTSONG | KEY\_MENU | KEY\_NUMERIC\_STAR |
| KEY\_BACK | KEY\_PREVIOUS | KEY\_VIDEO | KEY\_NUMERIC\_POUND |
| KEY\_BACKSPACE | KEY\_PREVIOUSSONG | KEY\_AUDIO | KEY\_RED |
| KEY\_ESC | KEY\_EPG | KEY\_MP3 | KEY\_GREEN |
| KEY\_RECORD | KEY\_TITLE | KEY\_CAMERA | KEY\_BLUE |
| KEY\_PLAY | KEY\_TV2 | KEY\_IMAGES | KEY PVR |
| KEY\_PLAYPAUSE | KEY\_CONTEXT\_MENU | KEY\_TUNER | KEY\_RADIO |
| KEY\_PAUSE | KEY\_SUBTITLE | KEY\_TV |  |
| KEY\_STOP | KEY\_LANGUAGE | KEY\_PVR |  |

Once you are finished with the keytable, save the file, stop ir-keytable -t with `+` and then restart it with the keytable file:

```text
LibreELEC:~ # ir-keytable -c -w /storage/.config/rc_keymaps/custom_remote
Read justboom table
Old keytable cleared
Wrote 12 keycode(s) to driver
Protocols changed to rc-5
```

Now run `ir-keytable -t` and press buttons. In addition to EV\_MSC scancode events you should now see EV\_KEY events, e.g.

```text
LibreELEC:~ # ir-keytable -t
Testing events. Please, press CTRL-C to abort.
1503599395.150849: event type EV_MSC(0x04): scancode = 0x1019
1503599395.150849: event type EV_KEY(0x01) key_down: KEY_VOLUMEUP(0x0073)
1503599395.150849: event type EV_SYN(0x00).
1503599395.264827: event type EV_MSC(0x04): scancode = 0x1019
1503599395.264827: event type EV_SYN(0x00).
1503599395.413668: event type EV_MSC(0x04): scancode = 0x1019
1503599395.413668: event type EV_SYN(0x00).
1503599395.673626: event type EV_KEY(0x01) key_up: KEY_VOLUMEUP(0x0073)
1503599395.673626: event type EV_SYN(0x00).
```

If the test is successful, make the keytable persistent by creating `/storage/.config/rc_maps.cfg` with the following content:

```text
* * custom_remote
```

Reboot and you should have a working remote in Kodi.

## Troubleshooting

Check the obvious. Batteries run down and either stop the remote from working or reduce the working range. Try using the remote in-front of the receiver. You can also try pointing the IR transmitter at the sensor of a digital camera or smartphone in a dark room. If the IR transmitter works you should see it light up the camera/smartphone screen.

If the remote is sending signals, we need trace the problem and

There are several steps until a button press on your remote finally triggers an action in Kodi. Use this guide to check if each of the steps works. Check if the remote is transmitting signals

To check if the IR receiver driver is loaded run `ir-keytable`. If you see the error message `/sys/class/rc/: No such file or directory` no driver is loaded.

To check if the IR receiver is receiving any signals, see if `ir-keytable` shows `lirc` as a supported protocol. If yes, run `ir-ctl -r` to show raw, undecoded signals from the receiver. If IR reception works you will see lots of pulse and space lines when pressing a button. Note: On kernels before 4.3 lirc may be listed in Supported protocols but not in Enabled protocols. In this case run `ir-keytable -p lirc` to enable it first \(remembering this will disable other decoders\).

To check if `lircd` is running run `ir-keytable`. If only `lirc` shows up in `Enabled protocols:` lircd is running. When it starts it disables all other protocols\). You can also run `ps | grep /usr/sbin/lircd` to check for `/usr/sbin/lircd` and `/usr/sbin/lircd-uinput` processes. If you see them LIRC enabled. If LIRC is enabled, disable it in LibreELEC Settings &gt; Services and reboot.

To check if the correct keytable is loaded run `ir-keytable -r` to show the current keytable and protocol. Compare this to the header in `/storage/.config/rc_keymaps/custom_remote` and check `/storage/.config/rc_maps.cfg` references the correct keytable name.

To check if IR decoding works correctly stop Kodi and eventlircd:

```text
systemctl stop kodi
systemctl stop eventlircd
```

Then run `ir-keytable -t` and press buttons on the remote. You should see EV\_MSC events with the scancode and EV\_KEY events with the Linux keycode, e.g.

```text
LibreELEC:~ # ir-keytable -t
Testing events. Please, press CTRL-C to abort.
1503599395.150849: event type EV_MSC(0x04): scancode = 0x1019
1503599395.150849: event type EV_KEY(0x01) key_down: KEY_VOLUMEUP(0x0073)
```

If you only get EV\_MSC but no EV\_KEY events, verify if the correct keytable is configured.

After this test, restart eventlircd and Kodi \(or reboot\):

```text
systemctl start eventlircd
systemctl start kodi
```

To check if eventlircd translates the input events to LIRC events run `irw` to show the translated LIRC events Kodi sees. When pressing a button you should see messages like this:

```text
LibreELEC:~ # irw
72 0 KEY_VOLUMEDOWN devinput
72 1 KEY_VOLUMEDOWN devinput
72 2 KEY_VOLUMEDOWN devinput
```

To check if Kodi receives the translated LIRC events enable debug logging in System Settings &gt; Logging then watch the logfile:

```text
tail -f /storage/.kodi/temp/kodi.log
```

When you press a button you should see log entries with `LIRC: Update` like this:

```text
21:55:33.891 T:1945866240   DEBUG: LIRC: Update - NEW at 60659:6c 0 KEY_DOWN devinput (KEY_DOWN)
21:55:33.891 T:1945866240   DEBUG: OnKey: 167 (0xa7, obc88) pressed, action is Down
```

The LIRC line indicates Kodi received the LIRC event. The `OnKey:` line shows the action generated after applying `Lircmap.xml` and `remote.xml`. If you see LIRC lines but incorrect actions, check your Kodi `Lircmap.xml` and `remote.xml` config files are correct.

## Known Issues

USB data transfers can interfere with GPIO IR recievers on Raspberry Pi 0/1/2/3 hardware, and since Ethernet is internally connected via USB playing large movies from a NAS can exhibit the problem. This is a known issue: [see this forum post for details](https://github.com/raspberrypi/linux/issues/906) and there is nothing we can do. Common workarounds are to use a USB IR receiver like [Flirc](https://flirc.tv) or CEC.

## LIRC Support

Most remotes are now supported by the Linux kernel but "LIRC" \(the userspace lircd daemon and tools\) is still useful for handling unusual remotes with odd protocols and no kernel drivers. Since LibreELEC 8.2.0 LIRC is disabled by default, but can be enabled in LibreELEC Settings &gt; Services &gt; Lirc. Configuration is handled in \(almost\) the same way as most desktop Linux distros:

* The `/etc/lirc/lirc_options.conf` file configures the default LIRC driver and /dev/lirc0.
* The `/etc/lirc/lircd.conf` file includes remote configs for MCE, Xbox 360, Xbox One, and a few others.

The embedded files can be overriden at boot time by creating `/storage/.config/lircd.conf` and `/storage/.config/lirc_options.conf` with needed changes.

In LibreELEC `lircd-uinput` reads decoded LIRC events from `/run/lirc/lircd.socket` and translates these to Linux input events via the Linux uinput driver. The Linux input events from lircd-uinput are then picked up by `eventlircd` and fed to Kodi as LIRC events via the `/run/lirc/lircd` socket.

It might seem odd to translate between LIRC, Linux input and \(again\) LIRC events, but Kodi can only receive LIRC events on a single LIRC socket, and this allows eventlircd to collect all remote events and feed them to Kodi so we can support LIRC-decoded and kernel-decoded remotes without needing user input or complex scripts to change the configuration.

To use LIRC with an IR receiver that supports in-kernel decoding it's best to disable ir-keytable auto configuration with an empty `rc_maps.cfg` file:

```text
touch /storage/.config/rc_maps.cfg
```

Although lircd disables all remote protocols \(and thus in-kernel decoding\) on startup ir-keytable auto-configuration runs in parallel, and if it happens to run after lircd starts it can re-enable in-kernel decoding. This causes duplicate decoding as both lircd and kernel will receive and proocess the IR signals. This issue is often intermittent since it depends on the timing of lircd start. Sometimes lircd will run after ir-keytable and in-kernel decoding is disabled as expected.

Note: If you stop or disable LIRC in LibreELEC Settings you will need too reboot or set ir-keytable manually from the SSH console. Disabling LIRC does not automatically re-enable in-kernel decoding.

