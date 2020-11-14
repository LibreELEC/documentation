# Pulseaudio

Pulseaudio is used for routing audio to a Bluetooth device. With additional configuration it can also be used to receive audio and stream to multi-device \(multi-room\) configurations.

## Commands

The tutorials on this page are basically using four commands:

* `pactl load-module <name>` to load a pulseaudio module \(not all are loaded by default\)
* `pactl set-default-sink` to set the pulseaudio output device
* `pactl set-default-source` to set the pulseaudio input device
* `pactl info` to check the currentt sink/source configuration

Sample `pactl info` output:

```text
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

This is the default configuration. Simply pair a Bluetooth audio device in the LibreELEC settings add-on and change the Kodi output device \(in Kodi settings\) to Bluetooth Audio.

## Bluetooth Receiving

This allows you to stream Bluetooth audio from a smartphone or other Bluetooth device. Before you start, pair the device in the LibreELEC settings add-on.

Stop Kodi \(else it holds onto the audio device\):

```text
systemctl stop kodi
```

Load the pulseaudio udev module:

```text
pactl load-module module-udev-detect
```

List the names of pulseaudio output devices using the `pactl` utility:

```text
pactl list short sinks
```

For example:

```text
1    alsa_output.platform-aml_m8_snd.45.analog-stereo    module-alsa-card.c    s32le 2ch 44100Hz    SUSPENDED
```

Configure the pulseaudio output device:

```text
pactl set-default-sink alsa_output.platform-aml_m8_snd.45.analog-stereo
```

Use `pactl` to find your Bluetooth audio device:

```text
pactl list short sources
```

For example:

```text
12    bluez_source.B8_53_AC_01_8F_E7    module-bluez5-device.c    s16le 2ch 44100Hz    SUSPENDED
```

Configure the pulseaudio source device:

```text
pactl set-default-source bluez_source.B8_53_AC_01_8F_E7
```

Restart Kodi:

```text
systemctl start kodi
```

Select the audio output device in Kodi if you want audio from your device, and from playing files in Kodi.

## Network Sending

This requires another computer or device running pulseaudio, e.g. an MPD music player or another LibreELEC device.

On the pulseaudio target device, ensure the the `native-protocol-tcp` and `zeroconf-publish` modules are loaded:

```text
pactl load-module module-native-protocol-tcp auth-anonymous=1
pactl load-module module-zeroconf-publish
```

On the pulseaudio source \(LibreELEC\) device, stop Kodi and find the output device:

```text
systemctl stop kodi
pactl list short sinks
```

For example:

```text
39    module-tunnel-sink    server=[[192.168.1.70]]:4713 sink=alsa_output.pci-0000_00_08.0.analog-stereo format=s16le channels=2 rate=44100 sink_name=tunnel.lukas-macbook-pro.local.alsa_output.pci-0000_00_08.0.analog-stereo channel_map=front-left,front-right
```

On the pulseaudio source, configure the pulseaudio output device:

```text
pactl set-default-sink tunnel.lukas-macbook-pro.local.alsa_output.pci-0000_00_08.0.analog-stereo
```

Restart Kodi:

```text
systemctl start kodi
```

You can now select the default pulseaudio output device in Kodi settings. Audio will be broadcast on the network and other pulseaudio devices can select it as their audio source.

## Network Receiving

This requires another pulseaudio device in the network, e.g. an MPD player or another LibreELEC device.

Stop Kodi else it will hold onto the audio output device:

```text
systemctl stop kodi
```

Load the pulseaudio udev module:

```text
pactl load-module module-udev-detect
```

Use `pactl` to list pulseaudio output devices:

```text
pactl list short sinks
```

For example:

```text
1    alsa_output.platform-aml_m8_snd.45.analog-stereo    module-alsa-card.c    s32le 2ch 44100Hz    SUSPENDED
```

Configre the pulseaudio output device:

```text
pactl set-default-sink alsa_output.platform-aml_m8_snd.45.analog-stereo
```

On the source pulseaudio device, load the `native-protocol-tcp` and `zeroconf-discover` modules:

```text
pactl load-module module-native-protocol-tcp auth-anonymous=1
pactl load-module module-zeroconf-discover
```

On the target device, use `pactl` tto list available input devices:

```text
pactl list short sources
```

For example:

```text
38    module-tunnel-source    server=[[192.168.1.70]]:4713 source=alsa_input.pci-0000_00_08.0.analog-stereo format=s16le channels=2 rate=44100 source_name=tunnel.lukas-macbook-pro.local.alsa_input.pci-0000_00_08.0.analog-stereo channel_map=front-left,front-right
```

Configure the pulseuadio input device:

```text
pactl set-default-sink tunnel.lukas-macbook-pro.local.alsa_input.pci-0000_00_08.0.analog-stereo
```

Restart Kodi:

```text
systemctl start kodi
```

## Configuration

Custom pulseaudio configuration must be stored in `/storage/.config/pulse-daemon.conf.d` as `/etc/pulse` is in the read-only filesystem. Files must have a `.conf` extension. The main `daemon.conf` is processed first, followed by other files in alphabetical order so if the same option is set in multiple files, the last one to be read will be used. If files have the same name as a default \(embedded\) file they override \(replace\) the embedded file.

## Sample Rates and Resampling

Create `/storage/.config/pulse-daemon.conf.d/custom.conf` with your custom options, e.g.

```text
resample-method = soxr-vhq
default-sample-format = s16le
default-sample-rate = 44100
```

To see the available resample methods:

```text
pulseaudio --dump-resample-methods
```

For example:

```text
trivial
ffmpeg
auto
copy
peaks
soxr-mq
soxr-hq
soxr-vhq
```

