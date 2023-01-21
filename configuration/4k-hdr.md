# 4K / HDR

This page explains the configuration points:

* Desktop Resolution
* Modeline Whitelist
* Adjust Refresh
* Deinterlacing
* HDR Questions

### Desktop Resolution

The primary impact of the Kodi desktop resolution is GUI performance, or how Kodi feels when you navigate around menus and libraries. Kodi leverages GPU hardware to accelerate rendering of GUI elements, but fanart and thumbnails are frequently adapted from original artwork on-the-fly as you scroll around, and even when all images have been pre-processed, they must still be read from disk caches before being displayed. Higher levels of CPU activity from larger artwork and media also mean higher power consumption from the HTPC device.

The second impact of the Kodi desktop resolution is how media is scaled. If "Adjust Refresh" is not enabled, Kodi will scale all media played up or down to the desktop resolution and refresh rate, which by default is 1080p @ 60 fps. Scaling SD and 720p media to 1080p while adding extra frames to match 60fps is handled well, as Kodi simply needs to duplicate frames to reach the higher fps rate, and the simple scaling algorithms which Kodi uses to reduce artefacts in the upscaled image move smaller amounts of data in/out of memory. Hardware as low-spec as the first generation Raspberry Pi can normally manage this well.

Scaling SD and 1080p media to 4K is a literally bigger challenge. Frame sizes for 4K media are 4x the size of 1080p so the amount of data being processed in/out of memory increases and the CPU resources needed also increase. Scaling 4K to 1080p is a larger challenge again. Source 4K media has huge frame sizes and sophisticated (down)scaling algorithms are required to create a lossy image without massive visual artefacts.

Higher-spec Intel CPU devices with recent Core i7 chips and SSD storage can normally run the Kodi GUI at 4K with 60fps refresh rates and scale media with reasonable quality, but older and lower-spec Intel CPU devices and anything running from a spinning HDD or removable USB/SD media will struggle with a 4K desktop, and ARM SoC devices simply fail as they don't have the memory bandwidth or CPU speeds to process the data volumes needed.

However, all 4K resolution TV's have dedicated scaling hardware using sophisticated algorithms to upscale 1080p images to the native 4K resolution of the screen. TVs do this better and much more efficiently than Kodi can scale images itself.

<mark style="color:red;">**Recommendation: Keep the Desktop resolution at 1080p, with 60fps refresh rate. Allow Kodi to upscale SD media to HD, and let the TV handle upscaling to 4K**</mark>

### Mode Whitelist

Most 4K televisions support a broad range of resolutions and refresh rates to support different media standards. In Linux each output format is known as a "modeline" and Kodi allows you to configure a list of allowed modes to use in combination with the "Adjust Refresh" option.

Raspberry Pi 4 devices require specific configuration in config.txt to enable 4K @ 60/59.94/50 mode output, but nearly all streaming media uses 4K@30/23.976 modes so most users do not need to enable these modes. Enabling 4K60 output also increases power consumption.

4K @ 60/59.94/50 modes require hardware that supports HDMI 2.0 or higher, and an HDMI cable that supports the higher bandwidth needed. Suitable cables are normally sold as 4K@60 "certified" cables. Cheaper and lower-spec cables that support HDMI 1.4 modes (up to 4K@30) are a frequent cause of "I enabled 4K@60 but have a blank screen" issue reports in the forums.

Some hardware supports 4096x2160 modes. Kodi evaluates the whitelist using the height (2160) and refresh rate, so if you include these in your whitelist selection Kodi will switch to them instead of 3840x2160 modes which are an exact or more accurate match with 4K streaming media. If you see black bars on the sides of 4K media while playing, remove 4096 modes from the whitelist.

<mark style="color:red;">**Recommendation: Configure Kodi to allow the following modes, if available:**</mark>

* <mark style="color:red;">**3840x2160 @ 60/59.94/50/30/29.97/25/24/23.976**</mark>
* <mark style="color:red;">**1920x1080 @ 60/59.94/50/24/23.976**</mark>

### Adjust Refresh

Kodi can either upscale media to match the desktop resolution and refresh rate, or automatically switch to a resolution and rate that exactly or better matches the media. Switching to match the rate can eliminate or minimise the need for Kodi to make corrections (to keep things in-sync) by adding or dropping frames. Adding frames to the stream is generally harmless and rarely causes noticeable artefacts, but dropping frames causes skips, glitches and audio pops.

Kodi has three "Adjust Refresh" settings:

* Off (scale media to the desktop resolution/rate)
* On start/stop (change resolution/rate when playback starts and stops)
* Always (change on start/stop and during playback if the media changes resolution/rate)

<mark style="color:red;">**Recommendation: Set the Adjust Refresh setting to "On Start/Stop"**</mark>

### Deinterlacing

4K media is progressive so this is not a requirement for 4K or HDR playback, but many Kodi users have DVB setups where interlaced streams are common so we need to set the mode whitelist to support them. The challenge with interlaced media is:

* Kodi cannot output interlaced frames, it always outputs progressive frames
* Kodi cannot detect media is interlaced until you start playing it

Kodi converts interlaced media for progressive output by doubling the original refresh rate and rendering each half-frame in its own full frame, e.g. PAL 1080i @ 25fps media becomes 1080p @ 50fps, and NTSC 1080i @ 29.97 becomes 1080p @ 59.94fps. For this rate doubling to work, we need to ensure the whitelisted 1080p modes do not contain 1080p @ 25/29.97/30Hz rates, else Kodi will see an exact match against resolution and refresh rate and you will see high CPU loads and 50% of frames being dropped. If the modes are removed, Kodi will use 50Hz/59.94Hz rates, and if the media is interlaced, Kodi can still render 100% of the frames.

<mark style="color:red;">**Recommendation: Remove 1080 @ 30, 29.97 and 25 fps rates from the Modelist**</mark>

### HDR

Broadcast and Internet media follow a multitude of formal standards for resolution, refresh-rate, bit-depth, video format and colour-space. HDR formats add static or dynamic metadata to the stream or even individual video frames to enhance how colours are displayed. Kodi on Linux can support static HDR formats like HLG and HDR10, but not dynamic (proprietary) formats like HDR10+ and Dolby Vision which do not have open-source implementations. Users should understand HDR is not a simple "My TV can do 4K" type standard. HDR is a collection of competing technical standards (format wars are in progress) and the number of variables that must be determined and processed to render something nice looking on screen is huge.

Kodi aims to hide the mind-bending complexity of choosing how HDR media will be displayed on screen by detecting what your HTPC device and TV support and what the media to be played requires, allowing the display pipeline to automatically configure itself for the best result; whether that is an exact match or the best compromise. Kodi and LibreELEC support for HDR formats is still in early stages.

<mark style="color:red;">**Recommendation: There is nothing specific to configure for HDR itself (only 4K things) so sit back and enjoy that Kodi and LibreELEC developers are working to bring HDR support to your living room, for free. Please be patient, because it's a long and complicated task!**</mark>

Here are some answers to common HDR questions:

* Kodi does not (yet) support HDR to SDR conversion, known as tonemapping. If you play HDR media on a TV that does not support HDR, it will often play but colours will be muted and washed out. In the future Kodi may support conversion, but it is not currently implemented.
* Kodi has no way to present OSD menus in SDR when playing HDR media, so menus will have bright saturated colours. In the future, plane based tonemapping may be able to correct this, but it is not currently implemented and not all hardware supports multiple planes.
* Kodi will attempt to output in the highest bit-depth supported by the display pipeline, e.g. on a Raspberry Pi 4 it will attempt 12-bit before falling back to 10-bit, then 8-bit. Not all 4K HDR capable hardware supports higher 12-bit and 10-bit depths.
* Kodi supports Dolby Vision under Android (if the device is licensed for it) but not Linux. Dolby requires manufacturers to license their Intellectual Property and use integration libraries to decode the HDR metadata. Until FFMpeg comes up with a "clean room" reverse engineered open-source implementation, Kodi will not support it.
