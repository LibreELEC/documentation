# Network Boot

PXE booting with `SYSTEM` and `/storage` mounted via NFS is supported on Generic x86\_64 and current Raspberry Pi devices (Pi 4 and newer).  Older Raspberry Pi devices can also be booted over the network, but they require a different procedure. In the following writeup the server is Ubuntu 18.04 LTS.

## Server (Ubuntu)

In Ubuntu we need to install and configure DHCP, TFTP, and NFS services:

```
sudo apt install isc-dhcp-server tftpd-hpa nfs-kernel-server
```

### DHCP

Edit `/etc/dhcp/dhcpd.conf` and change the DHCP `range` if needed. Create entries in the `host` section for your client(s):

```
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

Edit `/etc/default/isc-dhcp-server` to set the interface name handling DHCP requests:

```
INTERFACES="eth0"
```

### NFS

Create the directories that will contain your `/storage` userdata and TFTP boot files, e.g.

```
sudo mkdir /mnt/media/storage
sudo mkdir -m777 /mnt/tftpboot
```

Download the LibreELEC image for your device. On x86 systems, copy the `KERNEL` and `SYSTEM` files into the `/mnt/tftpboot` directory:

```
sudo cp KERNEL /mnt/tftpboot
sudo cp SYSTEM /mnt/tftpboot
```

On a Raspberry Pi, just copy all files into the `/mnt/tftpboot` directory:

```
sudo cp * /mnt/tftpboot
```

{% hint style="info" %}
If you want to place the files in a subdirectory it is required to adjust the include directive in `config.txt`.
{% endhint %}

Then add the following lines to the `/etc/exports` file:

```
/mnt/media/storage      192.168.0.2/255.255.255.0(no_root_squash,rw,async,no_subtree_check)
/mnt/tftpboot           192.168.0.2/255.255.255.0(no_root_squash,rw,async,no_subtree_check)
```

### TFTP

Create directories for the TFTP bootfiles. In this example they are served from a different disk:

```
sudo mkdir /mnt/tftpboot/pxelinux.cfg
sudo cp -p /usr/lib/syslinux/pxelinux.0 /mnt/tftpboot
```

You may also want to copy the `KERNEL` and `SYSTEM` files to `/mnt/tftpboot`

Add the following lines to `/etc/default/tftpd-hpa` and save the file:

```
RUN_DAEMON="yes"
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/mnt/tftpboot"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure"
```

Create `/mnt/tftpboot/pxelinux.cfg/default` (or instead of default use the MAC-Address of your device in the format `90-91-92-93-94-95`) and insert the following:

```
DEFAULT LibreELEC
PROMPT 0

LABEL LibreELEC
kernel /KERNEL
append ip=dhcp boot=NFS=192.168.0.2:/mnt/tftpboot disk=NFS=192.168.0.2:/mnt/media/storage
```

Replace `192.168.0.1` with the NFS server IP address. The `overlay` parameter is not required if you only intend to boot one system. LibreELEC is now configured to boot using TFTP and NFS!

### NFSv4

Today many server use NFSv4 while the kernel still default to NFSv3. In such a case set the correct NFS dialect via the `vers=` option:

```
  APPEND ip=dhcp boot=NFS=192.168.0.1:/mnt/store/libreelec,vers=4.2 disk=NFS=192.168.0.1:/mnt/store/libreelec/storage,vers=4.2 overlay
```

Valid nfs versions are `2 3 4 4.0 4.1 4.2`.

### Firewall

If using the default Ubuntu firewall (UFW) add the following rules to allow ports for TFTP (69) and ONRPC (111):

```
sudo ufw allow proto udp from 192.168.0.0/24 to any port 69
sudo ufw allow proto tcp from 192.168.0.0/24 to any port 111
```

Clients normally connect to the NFS server on random ports, so it the firewall is active "mountd" needs to be told to use a fixed port allowed through the firewall, or boot will hang.

Edit `/etc/default/nfs-kernel-server` and replace the current `RPCMOUNTDOPTS` line. In the example below `8765` is a randomly chosen port:

```
RPCMOUNTDOPTS="-p 8765"
```

Create a matching firewall rule to allow the port:

```
sudo ufw allow proto tcp from 192.168.0.0/24 to any port 8765
```

### Services

And finally, start services:

```
sudo service isc-dhcp-server start
sudo service nfs-kernel-server start
sudo service tftpd-hpa start
```
