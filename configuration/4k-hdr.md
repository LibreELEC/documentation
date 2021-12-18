# 4K / HDR

This page explains the configuration points:

* Desktop Resolution
* Modeline Whitelist
* Adjust Refresh
* Deinterlacing
* HDR Questions

### Desktop Resolution

The primary impact of the Kodi desktop resolution is GUI performance, or how Kodi feels when you navigate around menus and libraries. Kodi leverages GPU hardware to accelerate rendering of GUI elements, but artwork and thumbnails (visible in most GUI views) are frequently adapted from original artwork on-the-fly, and already processed art must still be read from disk caches before being displayed.

Higher-spec Intel CPU devices with Core i7 chips and SSD storage can normally run the Kodi GUI at 4K with 60fps refresh rates, but older and lower-spec Intel CPU devices, anything with a HDD (not SSD) and almost all ARM SoC devices struggle with a 4K desktop. Higher CPU activity from larger art and media also means higher power consumption from the HTPC device.

The second impact of the Kodi desktop resolution is how media is scaled. If "Adjust Refresh" is not enabled, scaling SD and 720p media to 1080p while adding extra frames to match 60fps is handled well, as Kodi simply needs to duplicate frames, and the frames being duplicated and scaled are small in size so the amount of data being processed remains low. Hardware as low-spec as the first generation Raspberry Pi can manage this. Scaling 1080p media to 4K is a much bigger challenge. 4K is 4x the resolution of 1080p, so devices must process 4x the amount of source data. The scaling algorithms Kodi uses to create a higher resolution image from lower resolution source media also need a lot of CPU. Most devices struggle to use the best methods, e.g. Yadif, with 1080p media, let alone 4K content. Practically all hardware is insufficient for CPU upscaling to 4K resolutions. However, 4K resolution TV's have dedicated scaling hardware and handle the upscaling of a 1080p image from Kodi to the native 4K resolution of the screen much better than Kodi can scale the image itself.

<mark style="color:red;">**Recommendation: Keep the Desktop resolution at 1080p, with 60fps refresh rate. Let TVs handle upscaling of SD and HD media to 4K without loading the HTPC.**</mark>

### Whitelist

Most 4K televisions support a broad range of resolutions and refresh rates to support different media standards. In Linux each output format is known as a "modeline" and Kodi allows you to configure a list of modes to use in combination with the "Adjust Refresh" option.

<mark style="color:red;">**Recommendation: Configure Kodi for the following modes, if available:**</mark>

* <mark style="color:red;">**3840x2160 @ 60/59.94/50/30/29.97/25/24/23.976**</mark>
* <mark style="color:red;">**1920x1080 @ 60/59.94/50/24/23.976**</mark>

NB: You may not see 4K @ 60/59.94/50 if the hardware does not support HDMI 2.0 modes or the HDMI cable being used does not have adequate bandwidth.

### Adjust Refresh

Kodi can either upscale media to match the desktop resolution and refresh rate, or automatically switch to a resolution and rate that exactly or better matches the media. Switching to match the rate can eliminate or minimse the need for Kodi to make corrections (to keep things in-sync) by adding or dropping frames. Adding frames to the stream is generally harmless and rarely causes noticable artefacts, but dropping frames causes skips, glitches and audio pops.

Kodi has three "Adjust Refresh" settings:

* Off (scale media to the dekstop resolution/rate)
* On start/stop (change resolution/rate when playback starts and stops)
* Allways (change on start/stop and during playback if the media changes resolution/rate)

<mark style="color:red;">**Recommendation: Set the Adjust Refresh setting to "On Start/Stop"**</mark>

### Deinterlacing

The challenge with interlaced media is:

* Kodi cannot output interlaced frames, it always outputs progressive frames
* Kodi cannot detect media is interlaced until you start playing it

Kodi converts interlaced media to progressive by doubling the original refresh rate and rendering each half-frame in its own full frame, e.g. PAL 1080i @ 25fps media becomes 1080p @ 50fps, and NTSC 1080i @ 29.97 becomes 1080p @ 59.94fps. For this rate doubling to work, we need to ensure the whitelisted 1080p modes do not contain 1080p @ 25/29.97/30Hz rates, else Kodi will see an exact match against resolution and refresh rate and you will see high CPU loads and 50% of frames being dropped. If the modes are removed, Kodi will use 50Hz/59.94Hz rates, and if the media is interlaced, Kodi can still render 100% of the frames.

<mark style="color:red;">**Recommendation: Remove 1080 @ 30, 29.97 and 25 fps rates from the Modelist**</mark>

NB: All 4K media is progressive, so there is no need to do the same with 4K modes.

### HDR

Broadcast and Internet media follow a multitude of formal standards for resolution, refresh-rate, bit-depth, and video format. HDR adds the further complication of colour-space, using static or dynamic metadata that accompanies either the stream or individual video frames. Kodi supports static metadata UHD formats like  HDR, HDR10, HDR10+ but not proprietary dynamic metadata standards like Dolby Vision which do not have open-source implementations.

Kodi aims to hide the mind-bending complexity of choosing how HDR media will be displayed on screen by detecting what your HTPC device and TV support and what the media to be played requires, allowing the display pipeline to automatically configure itself for the best result; whether that is an exact match or the best compromise.

Here are some answers to common HDR questions:

* Kodi does not (yet) support HDR to SDR conversion, known as tonemapping. If you play HDR media on a TV that does not support HDR, it will play but colours will be muted and washed out. In the future Kodi may support conversion, but it is not currently implemented.
* Kodi has no way to present OSD menus in SDR when playing HDR media, so menus will have bright saturated colours. In the future, plane based tonemapping may be able to correct this, but it is not currently implemented and not all hardware supports multiple planes.
* Kodi will attempt to output in the highest bit-depth supported by the display pipeline, e.g. on a Raspberry Pi 4 it will attempt 12-bit before falling back to 10-bit, then 8-bit. Not all 4K HDR capable hardware supports higher 12-bit and 10-bit depths.
* Kodi supports Dolby Vision under Android (if the device is licensed for it) but not Linux. Dolby requires implementations to license their Intellectual Property and use integration libraries to decode the HDR metadata. Unless FFMpeg comes up with a "clean room" reverse engineered open-source implementation, it is unlikely that Kodi will ever support it.&#x20;
