# Hidden WiFi

WiFi networks with a hidden SSID are not visible to the connection manager (ConnMan) so cannot be selected in the LibreELEC settings add-on, and even if you manually configure a new ConnMan network profile via SSH, ConnMan cannot see the network to use the stored profile to connect.&#x20;

LE10 and older use ConnMan to manage connections and `wpa_supplicant` to create the actual connection. LE11 also use ConnMan to manage connections, but switched from `wpa_supplicant` to `iwd` allowing a simple `systemd` service to be used for connecting to hidden networks.

Create `/storage/.config/system.d/hidden-ssid.service` with the following content:

```sh
[Unit]
Description=Hidden SSID Connect Service
After=network-online.target connman.service iwd.service
Wants=network-online.target connman.service iwd.service
Before=kodi.target

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/bin/iwctl --passphrase <PWD> station wlan0 connect-hidden <SSID>
ExecStop=/usr/bin/iwctl station wlan0 disconnect

[Install]
WantedBy=multi-user.target
```

Replace `<PWD>` with the WiFi passphrase and `<SSID>` with the name of the network. On most LE devices there will only be a single `wlan0` wireless network interface, but if you have more than one card and `wlan1` or `wlan2` etc. are visible, set the correct NIC/card to use.

Next enable and start the `systemd` service:

```sh
systemctl enable /storage/.config/system.d/hidden-ssid.service
systemctl start hidden-ssid.service
```

Starting the service will initiate the connection and `ifconfig` should show an active NIC with an IP address. Ensure testing is done with Ethernet cables unplugged else the default traffic `route` will be on the `eth0` Ethernet connection.

{% hint style="info" %}
**Users often hide the network SSID to improve privacy and network security, but the SSID is disclosed in client connection requests making a hidden network easily discoverable to anyone sniffing WiFi packets and looking for it. Hiding the SSID provides zero protection.**
{% endhint %}

