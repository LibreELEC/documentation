# Network Boot

PXE booting with `SYSTEM` and `/storage` mounted via NFS is supported on Generic x86\_64 and Raspberry Pi devices. In the following writeup the server is Ubuntu 18.04 LTS.

## LibreELEC

Download the image for your device.

* Create a readonly nfs export and place `KERNEL` and `SYSTEM` files into it 
* Create a read-write nfs export for storage
* Create `pxelinux.cfg` with the MAC address of your device, e.g. `90-91-92-93-94-95`
* Edit this file and add:

```text
DEFAULT LibreELEC
PROMPT 0

LABEL LibreELEC
  KERNEL libreelec/KERNEL
  APPEND ip=dhcp boot=NFS=192.168.0.1:/mnt/store/libreelec disk=NFS=192.168.0.1:/mnt/store/libreelec/storage overlay
```

Replace `192.168.0.1` with the NFS server IP address. The `overlay` parameter is not required if you only intend to boot one system. LibreELEC is now configured to boot using TFTP and NFS!

## Ubuntu

In Ubuntu we need to install and configure DHCP, TFTP, and NFS services:

```text
sudo apt install isc-dhcp-server tftpd-hpa nfs-kernel-server
```

## DHCP

Edit `/etc/dhcp/dhcpd.conf` and change the DHCP `range` if needed. Create entries in the `host` section for your client\(s\):

```text
authoritative;
allow booting;
allow bootp;

ddns-update-style none;

default-lease-time 86400;
max-lease-time 86400;

subnet 192.168.0.0 netmask 255.255.255.0
{
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.0.255;
  option domain-name-servers 192.168.0.2, 192.168.0.1;
  option routers 192.168.0.1;

  range 192.168.0.10 192.168.0.200;
  next-server 192.168.0.2;
  filename "/pxelinux.0";

  host <name>
  {
    hardware ethernet 00:0a:0b:0c:0d:0e;
    fixed-address 192.168.0.5;
  }
}
```

Edit `/etc/default/isc-dhcp-server` and set the interface name that will handle DHCP requests:

```text
INTERFACES="eth0"
```

## NFS

Create the directories that will contain your `/storage` userdata and TFTP boot files, e.g.

```text
sudo mkdir /mnt/media/storage
sudo mkdir -m777 /mnt/tftpboot
```

Copy the `KERNEL` and `SYSTEM` files into the `/mnt/tftpboot` directory:

```text
sudo cp KERNEL /mnt/tftpboot
sudo cp SYSTEM /mnt/tftpboot
```

Then add the following lines to the `/etc/exports` file:

```text
/mnt/media/storage      192.168.0.2/255.255.255.0(no_root_squash,rw,async,no_subtree_check)
/mnt/tftpboot           192.168.0.2/255.255.255.0(no_root_squash,rw,async,no_subtree_check)
```

## TFTP

Create directories for the TFTP bootfiles. In this example they are served from a different disk:

```text
sudo mkdir /mnt/tftpboot/pxelinux.cfg
sudo cp -p /usr/lib/syslinux/pxelinux.0 /mnt/tftpboot
```

You may also want to copy the `KERNEL` and `SYSTEM` files to `/mnt/tftpboot`

Add the following lines to `/etc/default/tftpd-hpa` and save the file:

```text
RUN_DAEMON="yes"
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/mnt/tftpboot"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure"
```

Create `/mnt/tftpboot/pxelinux.cfg/default` and insert the following:

```text
DEFAULT LibreELEC
PROMPT 0

LABEL LibreELEC
kernel /KERNEL
append ip=dhcp boot=NFS=192.168.0.2:/mnt/tftpboot disk=NFS=192.168.0.2:/mnt/media/storage
```

## Firewall

If using the default Ubuntu firewall \(UFW\) add the following rules to allow ports for TFTP \(69\) and ONRPC \(111\):

```text
sudo ufw allow proto udp from 192.168.0.0/24 to any port 69
sudo ufw allow proto tcp from 192.168.0.0/24 to any port 111
```

Clients normally connect to the NFS server on random ports, so it the firewall is active "mountd" needs to be told to use a fixed port allowed through the firewall, or boot will hang.

Edit `/etc/default/nfs-kernel-server` and replace the current `RPCMOUNTDOPTS` line. In the example below `8765` is a randomly chosen port:

```text
RPCMOUNTDOPTS="-p 8765"
```

Create a matching firewall rule to allow the port:

```text
sudo ufw allow proto tcp from 192.168.0.0/24 to any port 8765
```

## Services

And finally, start services:

```text
sudo service isc-dhcp-server start
sudo service nfs-kernel-server start
sudo service tftpd-hpa start
```

