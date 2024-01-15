# WireGuard

LibreELEC can be configured as a WireGuard VPN client allowing you to accessing media in a remote location or tunnel traffic to avoid local inspection of network activity. This guide assumes configuration of a single WireGuard tunnel that is persistent, i.e. activated on device boot so that Kodi network traffic is routed through the WireGuard VPN tunnel.

WireGuard tunnels are managed by a ConnMan VPN plugin (connman-vpn.service) that acts as a companion to the network connection manager daemon (connman.service). The VPN plugin watches `/storage/.config/wireguard/*.config` and defines ConnMan services from auto-discovered configuration files. Once a valid WireGuard .config has been imported it can be connected manually using `connmanctl` from the SSH console or scripted from a systemd service that runs on boot. Connections can also be managed using the network 'Connections' tab in the LibreELEC settings add-on which controls ConnMan via d-bus.

## Sample Config

ConnMan uses its own configuration file format (see below) so you cannot import/use the files exported from WireGuard server tools and third-party VPN services - the format is different. Those files will contain everything you need, but you must manually transpose the information into the ConnMan format:

```
[provider_wireguard]
Type = WireGuard
Name = WireGuard (Home)
Host = 185.210.30.121
WireGuard.Address = 10.2.0.2/24
WireGuard.ListenPort = 51820
WireGuard.PrivateKey = qKIj010hDdWSjQQyVCnEgthLXusBgm3I6HWrJUaJymc=
WireGuard.PublicKey = zzqUfWGIil6QxrAGz77HE5BGUEdD2PgHYnCg3CDKagE=
WireGuard.PresharedKey = DfEYeVs04HS9XhKGM4/ZXHG3Qc4MFK2AJd8XouYDbRQ=
WireGuard.DNS = 8.8.8.8, 1.1.1.1
WireGuard.AllowedIPs = 0.0.0.0/0
WireGuard.EndpointPort = 51820
WireGuard.PersistentKeepalive = 25
```

Name = AnythingYouLike\
Host = IP of the WireGuard **server**\
WireGuard.Address = The internal IP of the **client** node, e.g. a /24 address\
WireGuard.ListenPort = The **client** listen port (optional)\
WireGuard.PrivateKey = The **client** private key\
WireGuard.PublicKey = The **server** public key\
WireGuard.PresharedKey = The **server** pre-shared key (optional)\
WireGuard.DNS = Nameserver to be used with the connection (optional)\
WireGuard.AllowedIPs = Subnets accessed via the tunnel, 0.0.0.0/0 is "route all traffic"\
WireGuard.EndpointPort = The **server** ListenPort\
WireGuard.PersistentKeepalive = Periodic keepalive in seconds (optional)

Note: Using WireGuard.PresharedKey is optional, but if your WireGuard configuration omits this you must remove the line from the config. If you leave it blank, e.g. `WireGuard.PresharedKey =` it will be actively with a null value, causing connections to fail.

## Creating Keys

If you need to create some, run `wg-keygen` from the SSH console and `/storage/.cache/wireguard` will contain new publickey, privatekey, and preshared files with keys inside. Most users will not need to generate WireGuard keys as they will be in the configuration file provided by a VPN service provider.

## Testing Connections

Once you have saved a configuration file, check it is valid:

```
RPi4:~ # connmanctl services
* R home              vpn_185_210_30_121
*AO Wired             ethernet_dca622135939_cable
```

In the above example `vpn_185_210_30_121` was created (vpn\_host) as the ConnMan service name. Test the service will connect using:

```
RPi4:~ # connmanctl connect vpn_185_210_30_121
```

ConnMan will create a new network interface, so `ifconfig` will show `wg0` or sometimes a higher number like `wg1` or `wg2`:

```
RPi4:~ # ifconfig
eth0      Link encap:Ethernet  HWaddr DC:A6:32:13:26:3b  
          inet addr:192.168.10.25  Bcast:192.168.10.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:3136938 errors:0 dropped:40816 overruns:0 frame:0
          TX packets:242506 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:310409413 (296.0 MiB)  TX bytes:22433323 (21.3 MiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:6109 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6109 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:415013 (405.2 KiB)  TX bytes:415013 (405.2 KiB)

wg0       Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
          inet addr:10.2.0.2  P-t-P:10.2.0.2  Mask:255.255.255.0
          UP POINTOPOINT RUNNING NOARP  MTU:1420  Metric:1
          RX packets:13744 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11080 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:13775220 (13.1 MiB)  TX bytes:1232552 (1.1 MiB)
```

You should be able to `ping` the remote (server) side of the WireGuard VPN tunnel. In our example this is 10.2.0.1:

```
RPi4:~ # ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: seq=0 ttl=64 time=147.936 ms
64 bytes from 10.2.0.1: seq=1 ttl=64 time=147.955 ms
```

The routing table will show normal traffic routed to the wg0 interface:

```
RPi4:~ # route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         *               0.0.0.0         U     0      0        0 wg0
1.1.1.1         *               255.255.255.255 UH    0      0        0 wg0
8.8.8.8         *               255.255.255.255 UH    0      0        0 wg0
10.2.0.0        *               255.255.255.0   U     0      0        0 wg0
192.168.10.0    *               255.255.255.0   U     0      0        0 eth0
192.168.10.1    *               255.255.255.255 UH    0      0        0 eth0
185.210.30.121  192.168.10.1    255.255.255.255 UGH   0      0        0 eth0
```

To disconnect the ConnMan service:

```
RPi4:~ # connmanctl disconnect vpn_185_210_30_121
```

Check `ifconfig` again and the WireGuard interface will be gone.

## Configuring Systemd

Create a systemd wireguard.service file to start the connection automatically on boot, after the network starts, and before Kodi is launched. The sample wireguard.service file looks like:

```
[Unit]
Description=WireGuard VPN Service
After=network-online.target nss-lookup.target wait-time-sync.service connman-vpn.service
Before=kodi.service

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/bin/connmanctl connect vpn_service_name_goes_here
ExecStop=/usr/bin/connmanctl disconnect vpn_service_name_goes_here

[Install]
WantedBy=multi-user.target
```

Copy the sample wireguard.service file to `/storage/.config/system.d/wireguard.service`

```
cp /storage/.config/system.d/wireguard.service.sample /storage/.config/system.d/wireguard.service
```

Replace **vpn\_service\_name\_goes\_here** with your service name, e.g. `vpn_185_210_30_121` using nano. Use `ctrl+o` to save changes and `ctrl+x` to exit nano:

```
nano /storage/.config/system.d/wireguard.service
```

Now we can enable and start the service:

```
RPi4:~ # systemctl enable /storage/.config/system.d/wireguard.service
Created symlink /storage/.config/system.d/multi-user.target.wants/wireguard.service → /storage/.config/system.d/wireguard.service.
RPi4:~ # systemctl start wireguard.service
```

Check the WireGuard tunnel is active using "ifconfig" and "ping" and if all is good, reboot to test the WireGuard tunnel comes up automatically on boot.

## Known Issues

ConnMan makes wg0 route all traffic over the WireGuard tunnel by default, no matter what `WireGuard.AllowedIPs` configuration you set. To route only specific networks via the tunnel the ConnMan service order (which influences routing order) must be changed.

Note the`sleep` and `connmanctl move-after` and `route add` commands used in the following tweaked systemd service file:

```
[Unit]
Description=WireGuard VPN Service
After=network-online.target nss-lookup.target connman.service connman-vpn.service bluetooth.service
Wants=network-online.target nss-lookup.target connman.service connman-vpn.service bluetooth.service

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/bin/sleep 5
ExecStart=/usr/bin/connmanctl connect vpn_X_klaus
ExecStart=/usr/bin/connmanctl move-after vpn_X_klaus ethernet_b827eb10c45a_cable
ExecStart=/usr/bin/connmanctl move-after vpn_X_klaus ethernet_b827eb10c45a_cable
ExecStart=/usr/sbin/route add -net 192.168.2.0 netmask 255.255.255.0 gw 10.0.0.2
ExecStop=/usr/bin/connmanctl disconnect vpn_X_klaus

[Install]
WantedBy=multi-user.target
```

The following forum thread has tips and examples: [https://forum.libreelec.tv/thread/21906-wireguard-changes-the-default-route-although-not-configured/](https://forum.libreelec.tv/thread/21906-wireguard-changes-the-default-route-although-not-configured/)

## Thanks

Big thanks! to ConnMan maintainer Daniel Wagner (wagi) who worked with LibreELEC staff to implement WireGuard support in ConnMan (he wrote the code, we ~~abused~~ tested it).
