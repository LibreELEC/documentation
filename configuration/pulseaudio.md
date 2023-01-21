# Pulseaudio

Pulseaudio is used for routing audio to a Bluetooth device. With additional configuration it can also be used to receive audio and stream to multi-device (multi-room) configurations.

The tutorials on this page are basically using four commands:

* `pactl load-module <name>` to load a pulseaudio module (not all are loaded by default)
* `pactl set-default-sink` to set the pulseaudio output device
* `pactl set-default-source` to set the pulseaudio input device
* `pactl info` to check the current sink/source configuration

Sample `pactl info` output:

```
LibreELEC:~ # pactl info
Server String: /var/run/pulse/native
Library Protocol Version: 31
Server Protocol Version: 31
Is Local: yes
Client Index: 41
Tile Size: 65472
User Name: root
Host Name: LibreELEC
Server Name: pulseaudio
Default Sample Specification: s16le 2ch 44100Hz
Default Channel Map: front-left,front-right
Default Sink: alsa_output.platform-aml_m8_snd.45.analog-stereo
Default Source: alsa_input.platform-aml_m8_snd.45.analog-stereo
```

## Bluetooth Sending

This is the default configuration. Simply pair a Bluetooth audio device in the LibreELEC settings add-on and change the Kodi output device (in Kodi settings) to Bluetooth Audio.

## Bluetooth Receiving

This allows you to stream Bluetooth audio from a smartphone or other Bluetooth device. Before you start, pair the device in the LibreELEC settings add-on.

Stop Kodi (else it holds onto the audio device):

```
systemctl stop kodi
```

Load the pulseaudio udev module:

```
pactl load-module module-udev-detect
```

List the names of pulseaudio output devices using the `pactl` utility:

```
pactl list short sinks
```

For example:

```
1    alsa_output.platform-aml_m8_snd.45.analog-stereo    module-alsa-card.c    s32le 2ch 44100Hz    SUSPENDED
```

Configure the pulseaudio output device:

```
pactl set-default-sink alsa_output.platform-aml_m8_snd.45.analog-stereo
```

Use `pactl` to find your Bluetooth audio device:

```
pactl list short sources
```

For example:

```
12    bluez_source.B8_53_AC_01_8F_E7    module-bluez5-device.c    s16le 2ch 44100Hz    SUSPENDED
```

Configure the pulseaudio source device:

```
pactl set-default-source bluez_source.B8_53_AC_01_8F_E7
```

Restart Kodi:

```
systemctl start kodi
```

Select the audio output device in Kodi if you want audio from your device, and from playing files in Kodi.

## Network Sending

This requires another computer or device running pulseaudio, e.g. an MPD music player or another LibreELEC device.

On the pulseaudio target device, ensure the the `native-protocol-tcp` and `zeroconf-publish` modules are loaded:

```
pactl load-module module-native-protocol-tcp auth-anonymous=1
pactl load-module module-zeroconf-publish
```

On the pulseaudio source (LibreELEC) device, stop Kodi and find the output device:

```
systemctl stop kodi
pactl list short sinks
```

For example:

```
39    module-tunnel-sink    server=[[192.168.1.70]]:4713 sink=alsa_output.pci-0000_00_08.0.analog-stereo format=s16le channels=2 rate=44100 sink_name=tunnel.lukas-macbook-pro.local.alsa_output.pci-0000_00_08.0.analog-stereo channel_map=front-left,front-right
```

On the pulseaudio source, configure the pulseaudio output device:

```
pactl set-default-sink tunnel.lukas-macbook-pro.local.alsa_output.pci-0000_00_08.0.analog-stereo
```

Restart Kodi:

```
systemctl start kodi
```

You can now select the default pulseaudio output device in Kodi settings. Audio will be broadcast on the network and other pulseaudio devices can select it as their audio source.

## Network Receiving

This requires another pulseaudio device in the network, e.g. an MPD player or another LibreELEC device.

Stop Kodi else it will hold onto the audio output device:

```
systemctl stop kodi
```

Load the pulseaudio udev module:

```
pactl load-module module-udev-detect
```

Use `pactl` to list pulseaudio output devices:

```
pactl list short sinks
```

For example:

```
1    alsa_output.platform-aml_m8_snd.45.analog-stereo    module-alsa-card.c    s32le 2ch 44100Hz    SUSPENDED
```

Configure the pulseaudio output device:

```
pactl set-default-sink alsa_output.platform-aml_m8_snd.45.analog-stereo
```

On the source pulseaudio device, load the `native-protocol-tcp` and `zeroconf-discover` modules:

```
pactl load-module module-native-protocol-tcp auth-anonymous=1
pactl load-module module-zeroconf-discover
```

On the target device, use `pactl` to list available input devices:

```
pactl list short sources
```

For example:

```
38    module-tunnel-source    server=[[192.168.1.70]]:4713 source=alsa_input.pci-0000_00_08.0.analog-stereo format=s16le channels=2 rate=44100 source_name=tunnel.lukas-macbook-pro.local.alsa_input.pci-0000_00_08.0.analog-stereo channel_map=front-left,front-right
```

Configure the pulseaudio input device:

```
pactl set-default-source tunnel.lukas-macbook-pro.local.alsa_input.pci-0000_00_08.0.analog-stereo
```

Restart Kodi:

```
systemctl start kodi
```

## Configuration

Custom pulseaudio configuration is stored in `/storage/.config/pulse-daemon.conf.d` as `/etc/pulse` is inside the read-only filesystem. Files must have a `.conf` extension. The file `daemon.conf` is processed first, followed by other files in alphabetical order so if the same option is set in multiple files, the last one to be read will be used. If files have the same name as a default (embedded) file they override (replace) the embedded file.

To make custom configurations persistent, we must override `/etc/pulse/default.pa` which is read on startup. Create `/storage/.config/pulse-daemon.conf.d/custom.conf` and set the following to override the startup script file location:

```
default-script-file = /storage/.config/pulse-daemon.conf.d/custom.pa
```

Now create `/storage/.config/pulse-daemon.conf.d/custom.pa` to extend the default startup script:

```
#!/usr/bin/pulseaudio -nF

.fail
.include /etc/pulse/default.pa

# extend default startup script here e.g.
load-module module-rtp-recv latency_msec=250 sap_address=0.0.0.0
```

## Sample Rates and Resampling

Create `/storage/.config/pulse-daemon.conf.d/custom.conf` with your custom options, e.g.

```
resample-method = soxr-vhq
default-sample-format = s16le
default-sample-rate = 44100
```

To see the available resample methods:

```
pulseaudio --dump-resample-methods
```

For example:

```
trivial
ffmpeg
auto
copy
peaks
soxr-mq
soxr-hq
soxr-vhq
```
