# Raspberry Pi

## HEVC on LibreELEC 9.x/10.x/11.x

The following table summarises LibreELEC support for HEVC media on different Raspberry Pi hardware generations:

| Device               | 9.2.8                       | 10.x / 11.x               |
| -------------------- | --------------------------- | ------------------------- |
| Raspberry Pi 0/1     | Not supported               | Not supported             |
| Raspberry Pi 2/3     | Up to 1080p in Software     | Up to SD in Software      |
| Raspberry Pi 4 / 400 | Up to 4K, basic HDR (8-bit) | Up to 4K, 8/10/12-bit HDR |

Raspberry Pi 4 and 400 are the ONLY devices that have native hardware decoding of HEVC content, and in LE10/11 releases we support 8/10/12-bit output for great results with 4K HDR material. Older LE 9.2.x releases only support 8-bit output, for basic playback.

Older Raspberry Pi devices support only software decoded HEVC. In practice Raspberry Pi 3 devices running LE 9.2.x with mild overclocking can play lower-bitrate 1080p content without issues, but higher bitrates and larger filesize media will give poor results. Raspberry Pi 2/1/0 models can normally manage 720p media but will struggle with 1080p.

LibreELEC 9.2.x runs best on older Raspberry Pi hardware because the video pipeline in Kodi v18 supports vendor proprietary hardware decoding methods (MMAL and OMXplayer) and custom optimisations for software-based HEVC decoding on Pi hardware. The optimisations for HEVC are very clever, but also require a large and invasive patch (more than 50,000 lines of additional code for FFMpeg). The complexity and Raspberry Pi specific nature of the optimisations has prevented them from being accepted upstream into FFMpeg, and few Linux distributions have allowed such a large patch into their codebase.

Kodi v19 (LibreELEC 10.x) removed support for vendor proprietary hardware decoding methods (on Raspberry Pi, the MMAL and OMX decoders) to focus on a standards-based approach (V4L2, named for the Linux kernel 'Video for Linux v2' API used). LibreELEC 10.x also dropped support for Raspberry Pi Zero and other 512MB RAM devices as 1GB is now considered as the minimum RAM needed for a good Kodi experience. The Raspberry Pi Foundation have rewritten their video drivers and FFMpeg support to follow the common V4L2 frameworks. As their goal is to upstream all driver and player-app changes, allowing them to be included and used with all Linux distros, the rewrites do not include the custom HEVC optimisations that would never be accepted. This means HEVC support on Raspberry Pi 4 and 400 hardware is excellent as these devices support native HEVC hardware decoding, and the newer drivers support 8/10/12-bit output for better 4K HDR, but playback on older Pi 01/2/3 devices will be challenging as FFMpeg software decoding is no longer optimised for Pi hardware, and users can expect SD media support only, not HD (720p and 1080p) support as seen with older LibreELEC releases.

In summary:

<mark style="color:red;">**If you need HEVC support on older Raspberry Pi 01/2/3 hardware use LibreELEC 9.2.x and do not update to LibreELEC 10.x or 11.x.**</mark>

**If you have Raspberry Pi 4 or 400 hardware, use LibreELEC 10.x or newer to benefit from better HEVC and 4K HDR support.**



