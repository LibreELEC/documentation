# WireGuard

LibreELEC can be configured as a WireGuard VPN client allowing you to accessing media in a remote location or tunnel traffic to avoid local inspection of network activity. This guide assumes configuration of a single WireGuard tunnel that is persistent, i.e. activated on device boot so that Kodi network traffic is routed through the WireGuard VPN tunnel.

WireGuard tunnels are managed by a ConnMan VPN plugin \(connman-vpn.service\) that acts as a companion to the main network connection manager daemon \(connman.service\). The VPN plugin watches /storage/.config/wireguard/\*.config and will attempt to define ConnMan services from auto-discovered configuration files. Once a valid ConnMan WireGuard .config has been imported it can be connected; either manually using connmanctl from the SSH console or scripted from a systemd service that runs on boot. Connections can also be controlled manually from the network 'Connections' tab in the LibreELEC settings add-on which connects and disconnects WireGuard \(ConnMan\) services via d-bus.

## Sample Config

ConnMan uses its own configuration file format \(shown below\). You cannot import/use the WireGuard configuration files exported from most WireGuard server tools and third-party VPN services - the format is different. The files will contain everything you need, but you must transpose the information into the ConnMan format:

```text
[provider_wireguard]
Type = WireGuard
Name = WireGuard (Home)
Host = 185.210.30.121
Domain = my.home.vpn
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

Name = AnythingYouLike  
Host = IP of the WireGuard **server**  
Domain = must.not.be.blank  
WireGuard.Address = The internal IP of the **client** node, e.g. a /24 address  
WireGuard.ListenPort = The **client** listen port \(optional\)  
WireGuard.PrivateKey = The **client** private key  
WireGuard.PublicKey = The **server** public key  
WireGuard.PresharedKey = The **server** pre-shared key \(optional\)  
WireGuard.DNS = Nameserver to be used with the connection \(optional\)  
WireGuard.AllowedIPs = Subnets accessed via the tunnel, 0.0.0.0/0 is "route all traffic"  
WireGuard.EndpointPort = The **server** ListenPort  
WireGuard.PersistentKeepalive = Periodic keepalive in seconds \(optional\)

Domain is a quirk of how ConnMan internally names and stores services, and it must exist. It is simply a text field, not a qualified domain, so "MyVPN" and "alice.loves.bob" and "libreelec.tv" are all valid Domain entries.

## Creating Keys

If you need to create some, run `wg-keygen` from the SSH console and `/storage/.cache/wireguard` will contain new publickey, privatekey, and preshared files with keys inside. Most users will not need to generate WireGuard keys as they will be in the configuration file provided by a VPN service provider.

## Testing Connections

Once you have saved a configuration file, check it is valid:

```text
RPi4:~ # connmanctl services
* R home              vpn_185_210_30_121_my_home_vpn
*AO Wired                ethernet_dca622135939_cable
```

In the above example `vpn_185_210_30_121_my_home_vpn` was created \(vpn\_Host\_Domain\) as the ConnMan service name. Test the service will connect using:

```text
RPi4:~ # connmanctl connect vpn_185_210_30_121_my_home_vpn
```

ConnMan will create a new network interface, so `ifconfig` will show wg0 \(sometimes wg1, wg2\), e.g.

```text
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

You should be able to `ping` the remote \(server\) side of the WireGuard VPN tunnel. In our example this is 10.2.0.1:

```text
RPi4:~ # ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: seq=0 ttl=64 time=147.936 ms
64 bytes from 10.2.0.1: seq=1 ttl=64 time=147.955 ms
```

The routing table will show normal traffic routed to the wg0 interface:

```text
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

```text
RPi4:~ # connmanctl disconnect vpn_185_210_30_121_my_home_vpn
```

Check `ifconfig` again and the WireGuard interface will be gone.

## Configuring Systemd

To start the connection automatically on boot we need to create a systemd wireguard.service file. This tells ConnMan which service to start and the sequence/dependencies \(when to start it\). The sample wireguard.service file looks like:

```text
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

```text
cp /storage/.config/system.d/wireguard.service.sample /storage/.config/system.d/wireguard.service
```

Replace **vpn\_service\_name\_goes\_here** with your service name, e.g. vpn\_185\_210\_30\_121\_my\_home\_vpn using nano. Use `ctrl+o` to save changes and `ctrl+x` to exit nano:

```text
nano /storage/.config/system.d/wireguard.service
```

Now we can enable and start the service:

```text
RPi4:~ # systemctl enable /storage/.config/system.d/wireguard.service
Created symlink /storage/.config/system.d/multi-user.target.wants/wireguard.service → /storage/.config/system.d/wireguard.service.
RPi4:~ # systemctl start wireguard.service
```

Congrats! .. all is done. Check the WireGuard tunnel is active using "ifconfig" and "ping" and if all is good, reboot to test the WireGuard tunnel comes up automatically on boot.

## Thanks

Big thanks to ConnMan maintainer Daniel Wagner who has worked with Team LibreELEC staff to implement WireGuard support in ConnMan \(he wrote the code, we tested it\). Thanks wagi!

