# Network Boot \(Raspberry Pi\)

PXE booting with `SYSTEM` and `/storage` mounted via NFS is supported on Generic x86\_64 and Raspberry Pi devices. In the following writeup the server is Debian stable.

## Server \(Debian\)

In my case, the netboot server is reachable at 192.168.0.8, and the Raspberry Pi 2 (v1.1) shall get a fixed IP address 192.168.0.30.

On the netboot server, we need to install and configure DHCP, TFTP, and NFS services:

```text
sudo apt install isc-dhcp-server tftpd-hpa nfs-kernel-server
```

### DHCP

Edit `/etc/dhcp/dhcpd.conf` and change the DHCP `range` if needed. Create entries in the `host` section for your Raspberry Pi client\(s\):

```text
authoritative;

default-lease-time 86400;
max-lease-time 86400;

ddns-update-style none;

subnet 192.168.0.0 netmask 255.255.255.0 {
  option routers 192.168.0.1;
}

host kodipi {
  hardware ethernet b8:27:eb:XX:XX:XX;  # put your Raspberry Pi's MAC address here
  fixed-address 192.168.0.30;
  next-server 192.168.0.8;
  option vendor-encapsulated-options "Raspberry Pi Boot";  # Important!
}
```

Edit `/etc/default/isc-dhcp-server` to set the interface name handling DHCP requests:

```text
INTERFACES="eth0"
```

### NFS

Create the directories that will contain your `/storage` userdata and TFTP boot files, e.g.

```text
sudo mkdir /srv/nfs/kodipi
sudo mkdir -m777 /srv/tftp
```

Then add the following lines to the `/etc/exports` file:

```text
/srv/nfs/kodipi  192.168.0.30/255.255.255.0(no_root_squash,rw,async,no_subtree_check)
/srv/tftp        192.168.0.30/255.255.255.0(no_root_squash,rw,async,no_subtree_check)
```

### TFTP

You can leave `/etc/default/tftpd-hpa` at the default:

```text
# /etc/default/tftpd-hpa

TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/srv/tftp"
TFTP_ADDRESS=":69"
TFTP_OPTIONS="--secure"
```

### Firewall

I have not installed a firewall on my netboot server, but if you do, you might need to do several things:

Add rules to allow ports for TFTP \(69\) and ONRPC \(111\).

Clients normally connect to the NFS server on random ports, so it the firewall is active "mountd" needs to be told to use a fixed port allowed through the firewall, or boot will hang.

Edit `/etc/default/nfs-kernel-server` and replace the current `RPCMOUNTDOPTS` line. In the example below `8765` is a randomly chosen port:

```text
RPCMOUNTDOPTS="-p 8765"
```

Create a matching firewall rule to allow access to that port.

### Services

And finally, start services:

```text
sudo service isc-dhcp-server start
sudo service nfs-kernel-server start
sudo service tftpd-hpa start
```

## Client \(LibreELEC\)

Download the tar image for your Raspberry Pi and unpack it.

- Copy (or move) all the files and directories from the `3rdparty/bootloader` directory of the tar image to `/srv/tftp`.
- Copy (or move) the `SYSTEM` and `KERNEL` files from the `target` directory to `/srv/tftp` too.
- Rename the `KERNEL` file in `/srv/tftp` to `kernel.img`.
- Create a `cmdline.txt` file in `/srv/tftp` and add:

```text
boot=NFS=192.168.0.8:/srv/tftp disk=NFS=192.168.0.8:/srv/nfs/kodipi ip=192.168.0.30:192.168.0.8:192.168.0.1:255.255.255.0:kodipi:eth0:off:192.168.0.1 overlay quiet toram
```

Replace `192.168.0.8` with the NFS server IP address.
Replace the first `192.168.0.1` in the `ip=` parameter with the default router's IP address.
Replace the second `192.168.0.1` in the `ip=` parameter with a DNS server's IP address, likely the same as the default router's IP address.

Details on the `ip=` parameter can be found at https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/admin-guide/nfs/nfsroot.rst#n88

The `overlay` parameter is not required if you only intend to boot one system, on the other hand, this guide only handles environments with one statically configured Raspberry Pi.

The `toram` parameter speeds operation up quite a bit by copying the `SYSTEM` file to the Raspberry Pi's RAM. This eats about 130 MBytes of RAM, so don't do that on a model with less than one gigabyte of RAM.

You can add an `ssh` parameter to enable the SSH server. This enables SSH connections with the default password!

- Acquire a \(micro\)SD card that fits in your Raspberry Pi, partition it to have a FAT32 partition as the first partition. It only needs to fit an about 64 kilobyte file.
- Format that partition (as FAT32 of course).
- Copy the `/srv/tftp/bootcode.bin` file to that partition.
- Insert the \(micro\)SD card into your Raspberry Pi.

LibreELEC is now configured to boot using TFTP and NFS!

This works fine with my Raspberry Pi 2 B, Version 1.1, which is not netboot capable without an SD card.
