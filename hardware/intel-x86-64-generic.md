# Intel x86-64 (Generic)

The LibreELEC "Generic" image supports a (very) broad range of x86\_64 compatible hardware using AMD, Intel, and nVidia GPU hardware. The GPU in a device determines what media can be played using hardware decoding and modern features like HDR, and the CPU determines if any unsupported codecs can be software decoded for playback by ffmpeg.

## HDR

HDR support in the Linux kernel, in Kodi, and in LibreELEC is ongoing "work in progress" that continues to evolve. HDR depends on a combination of the media format and colourspace properties, GPU hardware, HDMI output, and TV panel capabilities. Most users wrongly view HDR support like codec support, but it is significantly more complicated. Few people outside the broadcast media industry truly understand how it works (in theory and in practice) but we are fortunate to have users with that professional background providing testing and insightful feedback to the team as progress is made.

As GPU hardware is one of the major variables, please read the AMD, Intel, and nVidia GPU sections below for further information relevant to HDR support on that GPU type:

## Intel GPUs

Intel NUC and similar-sized Intel GPU devices are popular for building an HTPC device around since they combine well supported upstream GPU drivers with entry level Celeron and Core i3 chips to intermediate Core i5 and high-end Core i7 CPUs.

Modern Intel GPUs have been shipping since "Nehalem" in 2010, and over time the number of different CPU and GPU generations and codenames have multiplied, making it confusing to figure out what media features a device can support. The following table is based on public sources like wikipedia, and it indicates hardware features, which do not always translate into what Linux and Kodi currently support in software:

| CPU   | Codename                                     | Driver | H264 | HEVC | AV1 | 4K  | HDR  |
| ----- | -------------------------------------------- | ------ | ---- | ---- | --- | --- | ---- |
| Gen1  | Nehalem                                      | i965   | Yes  | No   | No  | No  | No   |
| Gen2  | Sandy-bridge                                 | i965   | Yes  | No   | No  | No  | No   |
| Gen3  | Ivy-Bridge                                   | i965   | Yes  | No   | No  | No  | No   |
| Gen4  | Haswell                                      | i965   | Yes  | No   | No  | No  | No   |
| Gen5  | Broadwell, Braswell                          | i915   | Yes  | No   | No  | No  | No   |
| Gen6  | Skylake                                      | i915   | Yes  | Yes  | No  | DP  | No   |
| Gen7  | Kaby-lake, Apollo-lake                       | i915   | Yes  | Yes  | No  | Yes | HDMI |
| Gen8  | Coffee-lake, Kaby-lake refresh, Whiskey-lake | i915   | Yes  | Yes  | No  | Yes | HDMI |
| Gen9  | Coffee-lake refresh                          | i915   | Yes  | Yes  | No  | Yes | HDMI |
| Gen10 | Comet-lake, Ice-lake, Amber-lake             | i915   | Yes  | Yes  | No  | Yes | Yes  |
| Gen11 | Rocket-lake, Tiger-lake                      | i915   | Yes  | Yes  | Yes | Yes | Yes  |

* Gen6 CPUs (Skylake) have HDMI 1.4b and DisplayPort 1.2 connectors. HDMI can run at max 1920x1200@60 resolution, while DP can run at max 3840x2160@60 resolution.
* Gen7 and Gen8 CPUs use an LSPCON chip for HDMI 2.0 output. HDR is supported only when the HDMI output is connected to an HDMI 2.0 port on the TV. Users with Intel NUC devices are recommended to run the latest Intel firmware for their device. Lots of bugs in original/factory LSPCON firmwares have been fixed.
* Linux supports LSPCON chips for HDR from Linux 5.12 onwards
* Linux has full support for Tiger-Lake CPUs from Linux 5.12 onwards
* Intel GPUs are known as _Integrated graphics processors (IGPs)_ and are embedded within the CPU core. Each CPU generation uses a single GPU generation but the generation ID's do not match, e.g. Gen10 CPUs have Gen11 GPUs.

**LibreELEC 10.0 shipped with Linux 5.10 kernel support and Xorg graphics, so it does not have support for HDR or Gen11 hardware**. Community created LibreELEC 10.0 images with newer Linux kernels supporting Gen11 hardware and `GBM` graphics with experimental HDR patches can be downloaded from the LibreELEC forums. LibreELEC 11.0 nightly development images from 21/9/21 onwards also use GBM graphics, include early (pre-Alpha) Kodi 20.0 support for HDR, and an updated kernel supporting Gen11 hardware.

## AMD GPUs

AMD GPUs are a popular option for users building custom HTPCs from motherboards without onboard Intel graphics. LibreELEC embeds two AMD drivers: `radeon` is used with older cards, and `amdgpu`  is used with newer cards (starting from the 'Southern Islands' family).

Determining media features (H264, HEVC, AV1, 4K, 4K60, HDR, etc.) is complicated due to the sheer number of different AMD cards available. Each new GPU generation ships with low-end cards limited in capabilities, and high-end cards with multiple connectors. Media features like 4K and HDR also have a dependency on connectors (HDMI or Displayport) and the standards the connectors support. For example, some cards support HDMI 1.4b and a max resolution of up to 3840x2160@30 with 8-bit HDR output, while cards with DisplayPort 1.4 or HDMI 2.0 may support up to 3840x2160@60 with 10-bit output. 

The most accurate source of information is [https://www.x.org/wiki/RadeonFeature](https://www.x.org/wiki/RadeonFeature/)

## nVidia GPUs

LibreELEC v7.x - v10.x include two different nVidia GPU drivers; a "legacy" driver (340.xx) and the latest stable driver. The drivers provide `OpenGL` support via `Xorg` so Kodi can display a GUI on-screen, and Kodi supports`VDPAU` hardware decoding of H264 and some older SD era media codecs for efficient playback. Newer nVidia cards support 4K resolutions and `NVDEC` hardware decoding, but Kodi does not support `NVDEC`, and while Kodi can output 4K, nVidia VDPAU drivers have no support for HEVC or VP9 (the formats used with most 4K media) and there is no support for HDR. It is no surprise that project active-install stats show most nVidia installs are using the legacy nVidia driver, and the number of installs continues to decline over time: which means LibreELEC users are replacing older nVidia devices with something that does not use an nVidia GPU. This trend is not new and influences our technical decisions:

LibreELEC v11.x will switch "Generic" from Xorg OpenGL graphics to the Mesa OpenGL and the same DRM PRIME video stack used with ARM SoC devices. It is currently still possible to build a "Generic_Legacy" image using Xorg, but if "GBM" image development continues to progress well our intention is to drop the legacy image (and with it, nVidia support). In lieu of this:

_**The team does not recommend users purchase nVidia hardware for use with Kodi**_

LibreELEC v11.x "GBM" images are built with Mesa `nouveau` OpenGL support so nVidia users who upgrade will see Kodi on-screen. However `nouveau` has no ability to "reclock" modern nVidia GPUs so the card runs at its lowest clock frequency and performance is bad (too bad to be useful). This issue is caused by nVidia requiring the use of signed (closed-source) firmware to interact with newer card generations, and it is a problem with all modern nVidia cards and all Linux distributions. It means the `nouveau` driver only works with very old nVidia cards, and is not a long-term solution for nVidia support in LibreELEC.

Update (Oct 2021): [nVidia has announced the latest drivers support GBM and mesa](https://www.phoronix.com/scan.php?page=news_item\&px=NVIDIA-495.29.05-Linux). This will allow Kodi to render the GUI via mesa OpenGL. It removes one technical blocker to LibreELEC supporting newer nVidia devices in future releases. However, it is not the only blocker. The latest driver uses `NVDEC` for hardware decoding so either Kodi would need to add support for `NVDEC` or nVidia would need to support `VAAPI` which is used by Intel and AMD. In theory `NVDEC` is not a big technical challenge to implement, but Team Kodi has an established policy of refusing to include more/new proprietary hardware-decoding APIs that complicate code maintenance. So far nobody on the current Kodi staff has volunteered to implement `NVDEC` support and run the gauntlet of getting it accepted and merged. Even if an attempt was successful (which is unlikely) it would not benefit the majority of LibreELEC users with older nVidia cards which still depend upon the legacy driver and `VDPAU` decoding. So the lastest drivers and `NVDEC` support are not the long-term solution for nVidia support in LibreELEC either.

NB: If (when) LibreELEC drops support for Xorg and nVidia users, it does not means Kodi has dropped support. nVidia users can continue to use their nVidia card with Xorg and any Desktop Linux distro that supports Kodi (Arch, Debian, Ubuntu, etc.) - although the long-term trend with Desktop distros is to move away from Xorg to Wayland (which depends on Mesa) so the trend there is against nVidia too. In lieu of this, we say again:

_**The team does not recommend users purchase nVidia hardware for use with Kodi**_

## Legacy Hardware

Despite the range of devices that Generic x86\_64 images can run on, team development and testing has always been focussed on current and recent hardware packaged for use as HTPC devices. Users frequently mistake the minimalist nature of LibreELEC as perfect for recycling an older device (laptops with a broken screen are a recurring theme) into a medial player. You might be able to boot LibreELEC, but we are often missing hardware drivers for old hardware and the overall playback performance (and power consumption) of first generation Raspberry Pi boards is often way (way) better. As a general rule, project staff view any hardware over 7-8 years old as legacy hardware. If you do need to use legacy hardware; use an era-appropriate LibreELEC (or OpenELEC) image.

## i386 Support

Support for i386 hardware was dropped between OpenELEC v5.x and v6.x in 2015 due to low numbers of users, and LibreELEC has no interest in resurrecting support. It is still technically possible to hack/restore i386 support into our buildsystem and self-build an i386 image since we support 32-bit ARM SoC devices and use 32-bit userspace with 64-bit ARM SoCs so most packages in are still 32-bit compilable with little or no tweaking. However, there are still a few packages that do need tweaking to restore i386 support, and there is no HOWTO guide that you can follow. Some users enjoy the intellectual challenge of figuring out how to restore i386 support, but i386 hardware is super low-spec for modern Kodi use and our recommendation is to invest in a Raspberry Pi board instead of spending tens or hundreds of hours on codebase archeology. An inexpensive Pi board will give a better overall result, costs less to run, and be zero hassle to maintain over time.
