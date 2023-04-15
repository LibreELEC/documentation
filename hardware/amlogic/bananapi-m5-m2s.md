# BananaPi M5 / M2S

## Enabling WiFi and Bluetooth

BananaPi M5 and M2S have an optional WiFi mezzanine board with an RTL8822CS chip providing 802.11 b/g/n/ac on 2.4GHz/5GHz channels and Bluetooth 5.0 connectivity. The board is not active unless enabled through a device tree modification.

On BPI-M5 create `/storage/.config/autostart.sh` with the following content:

```shell
mount -o remount,rw /flash
fdtput -t s /flash/meson-sm1-bananapi-m5.dtb /soc/sd@ffe03000 status okay
fdtput -t s /flash/meson-sm1-bananapi-m5.dtb /soc/bus@ffd00000/serial@24000 status okay
mount -o remount,ro /flash
```

On BPI-M2S create `/storage/.config/autostart.sh` with the following content:

```sh
mount -o remount,rw /flash
fdtput -t s /flash/meson-sm1-bananapi-m2s.dtb /soc/sd@ffe03000 status okay
fdtput -t s /flash/meson-sm1-bananapi-m2s.dtb /soc/bus@ffd00000/serial@24000 status okay
mount -o remount,ro /flash
```

After rebooting the board, the WiFi and BT devices should be detected:

```bash
LibreELEC:/ # dmesg | grep rtw
[ 8.547372] rtw_8822cs mmc2:0001:1: WOW Firmware version 9.9.4, H2C version 15
[ 8.548447] rtw_8822cs mmc2:0001:1: Firmware version 9.9.14, H2C version 15

LibreELEC:/ # dmesg | grep -i Blue
[    8.099139] Bluetooth: Core ver 2.22
[    8.099306] NET: Registered PF_BLUETOOTH protocol family
[    8.099317] Bluetooth: HCI device and connection manager initialized
[    8.099538] Bluetooth: HCI socket layer initialized
[    8.099557] Bluetooth: L2CAP socket layer initialized
[    8.099797] Bluetooth: SCO socket layer initialized
[    8.272210] Bluetooth: HCI UART driver ver 2.3
[    8.272242] Bluetooth: HCI UART protocol H4 registered
[    8.272930] Bluetooth: HCI UART protocol Three-wire (H5) registered
[    8.307537] Bluetooth: HCI UART protocol Broadcom registered
[    8.316217] Bluetooth: HCI UART protocol QCA registered
[    9.038966] Bluetooth: hci0: RTL: examining hci_ver=0a hci_rev=000c lmp_ver=0a lmp_subver=8822
[    9.042736] Bluetooth: hci0: RTL: rom_version status=0 version=3
[    9.042767] Bluetooth: hci0: RTL: loading rtl_bt/rtl8822cs_fw.bin
[    9.053947] Bluetooth: hci0: RTL: loading rtl_bt/rtl8822cs_config.bin
[    9.094258] Bluetooth: hci0: RTL: cfg_sz 33, total sz 36529
[    9.518223] Bluetooth: hci0: RTL: fw version 0xffb8abd6
[    9.574916] Bluetooth: MGMT ver 1.22
```
