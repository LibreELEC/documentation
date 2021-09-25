# Intel x86-64 \(Generic\)

The LibreELEC "Generic" image supports a \(very\) broad range of x86\_64 compatible hardware using AMD, Intel, and nVidia GPU hardware. The GPU in a device determines what media can be played using hardware decoding and modern features like HDR, and the CPU determines if any unsupported codecs can be software decoded for playback by ffmpeg.

## HDR

HDR support in the Linux kernel, in Kodi, and in LibreELEC is ongoing "work in progress" that continues to evolve. HDR depends on a combination of the media format and colourspace properties, GPU hardware, HDMI output, and TV panel capabilities. Most users wrongly view HDR support like codec support, but it is much more complicated. Few people outside the world of broadcast media truly understand how it all works, and we are fortunate to have a small number of users from that professional background who provide direct and insightful feedback as progress is made.

As GPU hardware is one of the major variables, please read the AMD, Intel, and nVidia GPU sections below for further information relevant to HDR support on that GPU type:

## Intel GPUs

&lt; need to add something about different \*lake generations and support &gt;

## AMD GPUs

&lt; need to add something about different hardware generations and support &gt;

## nVidia GPUs

LibreELEC v7.x - v10.x include two different nVidia GPU drivers; a "legacy" driver \(340.xx\) and the latest stable driver. The drivers provide `OpenGL` support via `Xorg` so Kodi can display a GUI on-screen, and Kodi supports`VDPAU` hardware decoding of H264 and some older SD era media codecs for efficient playback. Newer nVidia cards support 4K resolutions and `NVDEC` hardware decoding, but Kodi does not support `NVDEC`, and while Kodi can output 4K, nVidia drivers have no support for HEVC or VP9 \(the formats used with most 4K media\) and there is no support for HDR. It is no great surprise that project active-install stats show 80% of active nVidia installs are using the legacy nVidia driver, and the number of active installs continues to decline over time: meaning LibreELEC users are replacing nVidia devices with something that does not use an nVidia GPU. This trend is not new and influences our technical decisions:

LibreELEC v11.x will switch "Generic" from Xorg OpenGL graphics to the Mesa OpenGL and the same DRM PRIME video stack used with ARM SoC devices. It is currently still possible to build a "Generic\_Legacy" image using Xorg, but if "GBM" image development continues to progress well our intention is to drop the legacy image \(and with it, nVidia support\). In lieu of this:

_**The team does not recommend users purchase nVidia hardware for use with Kodi**_

LibreELEC v11.x "GBM" images are built with Mesa `nouveau` OpenGL support so users who upgrade will still see Kodi on-screen. However `nouveau` has no ability to "reclock" the GPU so the card runs at its lowest frequency so performance is bad \(too bad to be useful\). This issue is caused by nVidia requiring the use of signed \(closed-source\) firmware to interact with the card generations, and it is a problem with all modern nVidia cards and all Linux distributions. It means the `nouveau` driver is not a long-term solution for nVidia support in LibreELEC.

nVidia has been working to reduce the differences between their `EGL Streams` alternative to `GBM` which may lead to support in Mesa which provides the OpenGL/GLES interface to render Kodi. If this happens it removes one technical blocker to LibreELEC supporting newer nVidia devices in future releases. However, it is not the only thing required. Kodi would also need to add support for `NVDEC` hardware decoding. However, Team Kodi has a firm policy of refusing to add more proprietary hardware-decoding APIs that complicate code maintenance. So far nobody in Team Kodi \(or outside it\) has volunteered to write the `NVDEC` support code and run the gauntlet of getting it accepted and merged. Even if an attempt was successful \(which is unlikely\) it would not benefit the majority of LibreELEC users with older nVidia GPU cards. So `NVDEC` support is not a long-term solution for nVidia support in LibreELEC either.

NB: If \(when\) LibreELEC drops support for Xorg and nVidia users, it does not means Kodi has dropped support. nVidia users can continue to use their nVidia card with Xorg and any Desktop Linux distro that supports Kodi \(Arch, Debian, Ubuntu, etc.\) - although the long-term trend with Desktop distros is to move away from Xorg to Wayland \(which depends on Mesa\) so the trend there is negative too. In lieu of this, we say again:

_**The team does not recommend users purchase nVidia hardware for use with Kodi**_

## Legacy Hardware

Despite the range of devices that Generic x86\_64 images can run on, team development and testing has always been focussed on current and recent hardware packaged for use as HTPC devices. Users frequently mistake the minimalist nature of LibreELEC as perfect for recycling an older device \(laptops with a broken screen are a recurring theme\) into a medial player. You might be able to boot LibreELEC, but we are often missing hardware drivers for old hardware and the overall playback performance \(and power consumption\) of first generation Raspberry Pi boards is often way \(way\) better. As a general rule, project staff view any hardware over 7-8 years old as legacy hardware. If you do need to use legacy hardware; use an era-appropriate LibreELEC \(or OpenELEC\) image.

## i386 Support

Support for i386 hardware was dropped between OpenELEC v5.x and v6.x back in 2015 due to low numbers of users and LibreELEC staff have no interest in resurrecting support. It is still technically possible to restore i386 support to our buildsystem and self-build an i386 image since we support 32-bit ARM SoC devices and use 32-bit userspace with 64-bit ARM SoCs so most packages are still 32-bit compilable with little or no tweaking. However, there are still a moderate number of package that do need tweaks to restore i386 support, and there is no HOWTO guide that you can follow. Some users relish the intellectual challenge of figuring out how to restore support, but old i386 hardware is low-spec for modern Kodi use and our normal recommendation is to invest in an inexpensive Raspberry Pi board instead of spending tens or hundreds of hours performing buildsystem archeology. It will give a better overall result, costs less to run, and be zero hassle to maintain over time.



