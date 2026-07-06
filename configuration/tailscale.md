# Tailscale

Tailscale is a zero-config VPN built on top of the WireGuard protocol. It creates a secure, private mesh network, a.k.a "tailnet" that connects devices directly to each other. You can connect your devices into an existing tailnet by installing the Tailscale Service add-on from the add-on repo.

### Connecting

After installing the Tailscale add-on, SSH into the device and bring tailscale up:

```
tailscale up
```

The output will display a URL that you can use to authenticate to your Tailscale network (known as a tailnet). After you authenticate, check the Machines page of the admin console to confirm the device appears in your tailnet.

### Configuration

Once the node has been added to your tailnet it can be configured. Follow Add-ons > My Add-ons > Services > Tailscale > Configure to open the configuration options.

* **Enable** allows you to manually enable (default) or disable the service.
* **Auto-generate name from OS hostname** is enabled by default and the OS hostname will be used as the node name in the tailnet. Disable this to manually specify another node name.
* **Accept routes** allows the node to accept routes published by other nodes in the tailnet.
* **Use exit node** instructs Tailscale to route all traffic through a tailnet exit node. Enabling this option will disable the option to configure the local node as an exit node.
  * **Select exit node** queries the tailnet for available exit nodes to make population of the exit node address easier.
  * **Exit node address** is the IP of the tailnet exit node to use; configured manually or (easier) by selecting an exit node
* **Advertise exit node** allows the local node to access traffic from other nodes and route that traffic to the internet. This option is hidden when 'Use exit node' is enabled.
  * **Allow LAN access** allows local LAN access when the local node is configured as an exit node. If this is disabled the exit node cannot act as a subnet router for the local subnet and will only route traffic to the internet

### Routing

Tailscale uses its own routing table (normally table `52`) with a `0.0.0.0/0` default route that points at `tailscale0` when you use an exit node, and an `ip rule` (normally `pref 5270: from all lookup 52`) that gets evaluated _before_ your main table lookup (`pref 32766`). Routing traffic through exit nodes can cause problems accessing devices in the same subnet, as _all traffic_ is routed. To have the node connect to e.g. a local NAS server with media, or an external server/service that should use the local internet connection instead of routing via the exit node, a routing exception must be made.

Check your actual rule setup first:

```
ip rule show
ip route show table 52
```

You'll likely see something like:

```
5210: from all fwmark 0x80000/0xff0000 lookup main
5230: from all fwmark 0x80000/0xff0000 lookup default
5250: from all fwmark 0x80000/0xff0000 unreachable
5270: from all lookup 52
32766: from all lookup main
```

To exempt local traffic from being routed through the tailnet you must add an ip rule _before_ table 52, e.g. to route traffic to `95.217.22.250/32` we add _ip rule_ `5269` _before_ tailscale on `5270` :

```
ip rule add to 95.217.22.250/32 lookup main priority 5269
```

Then we add an _ip route_ that sends traffic over the local `eth0` interface:

```
ip route add 95.217.22.250/32 via 172.16.20.1 dev eth0
```

Rule and route changes are not persistent over reboot. To make them persistent you will need to create and enable a simple one-shot systemd service to execute commands _after_ the tailscale service is started.

Create the service in `/storage/.config/system.d/tailscale-routes.service` and then enable the service with `systemctl enable /storage/.config/system.d/tailscale-routes.service` . Below is an example of a simple one-shot systemd service:

```
[Unit]
Description=Tailscale Routing Service
After=network-online.target time-sync.target tailscale.service
Wants=network-online.target time-sync.target tailscale.service

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStartPre=/usr/sbin/ip rule add to 95.217.22.250/32 lookup main priority 5269
ExecStart=/usr/sbin/ip route add 95.217.22.250/32 via 192.168.0.1 dev eth0

[Install]
WantedBy=multi-user.target
```
