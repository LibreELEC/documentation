# LaFrite

LibreComputer LaFrite (AML-S805X-AC) is an S805X (GXL) board with 512MB or 1GB of RAM and 16MB SPI boot flash. The board runs upstream U-Boot from SPI, and has no SD card slot, so you can boot the AMLGX `box` or `lafrite` images written to a USB stick or eMMC module. Default boot order is:

* SPI (built-in)
* EMMC (optional module)
* USB (socket furthest from the GPIO header)

### Run LibreELEC from eMMC

LibreComputer preinstalls their eMMC modules with Android, so if you power on the board with the module attached it will always boot into Android. To erase Android or overwrite with LibreELEC the board must first boot from USB without the module attached. Once in LibreELEC booted from USB, the module can be attached, detected, and erased or imaged with the `lafrite` image:

1. Power on the Board, and boot the AMLGX `box` image from USB
2. Attach the eMMC module, and from the SSH console:
3. Run `emmctool d` to detect the module
4. Run `emmctool z` to erase (zero) the module
5. Copy the `lafrite` image to `/storage/` on the USB stick
6. Run `emmctool w /storage/LibreELEC-AMLGX.arm-11.0.0-lafrite.img.gz` to write the `lafrite` image to the eMMC module.
7. Power off the board, remove the USB stick
8. Power on the board, and LibreELEC will boot to eMMC and resize /storage

### Updating U-Boot in SPI

LibreComputer provides [U-Boot firmware](https://boot.libre.computer/release/aml-s805x-ac/) files for updating the SPI flash. LibreELEC does not need a minimum version to boot, but newer U-Boot firmware fixes some graphical on-screen glitches seen in the early boot stages. To update:

1. Download the latest `aml-s805x-ac-spiflash.img` and flash it to a USB stick
2. Connect the USB drive to the socket furthest from the GPIO header
3. Power on the board
4. Current U-Boot checks if the new image matches existing firmware, and when it does not (as it is newer) the firmware will be updated. When complete:
5. Power off the board and remove the USB stick

### UART

The debugging UART is accessible on the 40pin GPIO header:

* TX (Green) on Pin 3
* RX (Grey) on Pin 5
* GND (Black) on Pin 6
