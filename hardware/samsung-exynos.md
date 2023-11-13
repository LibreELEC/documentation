# Samsung (Exynos)

Odroid XU4 and XU3 boards can run the `Exynos` image which uses a current upstream Linux kernel with a mesa/panfrost open-source graphics pipeline and the latest Kodi version.&#x20;

H264, H263, VP8 and other older codecs are supported. XU4/XU3 do not have HEVC or VP9 support and cannot handle software decoding those formats.

Note: The Samsung S5-MFP decoding drivers regressed after Linux 5.4 when a cleanup was done on the driver that removed functionality. The original plan was to restore function in later changes, but this never happened. Due to the age of the boards now, is unlikely to happen. You may find the Linux 5.4 kernel images in the HardKernel forums are more functional.

`Exynos` images are designed to boot and run from SD card, but can also boot from an eMMC module if upstream u-boot is installed to the `/dev/mmcblk1boot0` or `/dev/mmcblk1boot1` partitions. The boot process requires `extlinux` support so will not work with the older vendor u-boot versions that sometime ship pre-installed on XU4 eMMC modules.
