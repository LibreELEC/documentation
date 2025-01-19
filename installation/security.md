# Security

This article documents LibreELEC's insecurities: the services we run by default and the security design choices and compromises the OS makes. Where options exist, it also provides guidance or warnings to help you better-secure your LibreELEC installation.

{% hint style="danger" %}
LibreELEC is designed for home networks and the out-of-box configuration is a compromise of security, supportability, and usability. It should not be used in security-sensitive networks.
{% endhint %}

## Avahi

Avahi (Zeroconf) runs in the background to annouce services like Kodi, Samba, SSH over mDNS to the local network to make connecting from mDNS-aware OS and client applications easier. The service list is updated automatically when individual services are enabled or disabled. Avahi can be disabled in the LibreELEC settings add-on.

## Bluetooth

Bluetooth is enabled by default if hardware is detected to facilitate connections from remote controls and keyboards, and the OS listens for connections and pairing requests. Devices like speakers normally pair automatically while keyboards, remote controls, and compute devices with input capabilities normally prompt for a PIN code (shown on the screen). Bluetooth services including OBEX file transfer can be disabled in the LibreELEC settings add-on.

## Credentials

In a conventional Linux distro users access the OS with a non-privileged user account and use `sudo`to elevate privileges to perform tasks as the `root`user. This provides security isolation between user and system processes, with the side benefit of preventing easy end-user access to disruptive configuration options that are easily changed or misconfigured resulting in complex support activity.

In LibreELEC there is a single`root`user and most internal services run as the root user. This reduces distro maintainance and eliminates the need for `sudo` to provide a simple console experience for users with typically low or no Linux CLI experience. Although users have `root`privileges, most of the filesystem exists in a read-only state so there is limited scope for unskilled users to misconfigure the OS from its default known-good state.

As the default `root/libreelec`credential is a well-known secret we support changing the root user password and the first-run wizard prompts for it to be changed. It can also be changed from the console using `passwd`. It is _not_ possible to add users, groups, or change the `user:group` context applications run under without creating a custom OS image with baked-in changes.&#x20;

{% hint style="danger" %}
Users are strongly advised to change default credentials. The default `root/libreelec` and `libreelec/libreelec` have long been observed in common password dictionary lists that are available to anyone.
{% endhint %}

## Firewall

The OS is intended for use inside a home network behind a NAT router/firewall device. It embeds the `iptables` firewall but this is disabled by default. It can be enabled via the LibreELEC settings add-on, and we provide simple `Home/Public` options for inexperienced users to enable for essential protection. The "Home" option allows traffic from 192.168.x.x, 172.16.x.x, 10.x.x.x IP ranges (RFC1918) while the "Public" option blocks all traffic from all networks. The firewall automatically enables when custom iptables configuration is provided by the `/storage/.config/iptables` file.

## Samba

Samba is enabled by default to facilitate end-user access to the `\Logfiles` share when installation completes but the system does not boot to the Kodi home screen. Accessing the`\Logfiles`share auto-generates a zip archive containing logs and config info that can be downloaded and shared with project staff and end-users providing support assistance in the forum.

On successful boot the first-run wizard is shown and users can disable Samba. After the wizard has completed Samba services can be enabled/disabled or reconfigured via the LibreELEC settings add-on. Here you can enable authentication and change the default `libreelec/libreelec`credential used to access the Samba server.&#x20;

## SSH

SSH services are disabled by default but can be enabled on succsseful boot in the first-run wizard. If you enable services you are prompted to change the default `root/libreelec`credential. After the wizard has completed SSH services can be enabled/disabled or reconfigured via the LibreELEC settings add-on. Here you can reset the root password. The password can also be changed from the local console or SSH console using the standard Linux `passwd`tool.&#x20;

For better security and convenience, add a public key to `/storage/.ssh/authorized_keys` and then disable password authentication in the LibreELEC settings add-on. You can replace the default 2048-bit RSA keys generated on first-boot and stored in the `/storage/.ssh` directory. New SSH keys must be secured with 600 permissions else the SSH daemon will not start.

{% hint style="info" %}
Enabling the "Disable SSH Password" feature does not disable the password login prompt. It simply ensures no password provided will be accepted.
{% endhint %}

## VPN

Connecting to any commercial or private VPN server that provides a public IP address to the VPN tunnel network interface requires the server to block inbound connections or the[#firewall](security.md#firewall "mention") to be enabled with either `Home` or `Public` profiles. Services like Samba and SSH listen on all available interfaces so the VPN connection exposes services to the public internet.

## WSDD2

The WSDD2 service broadcasts information on Samba share to Windows OS devices to support auto-discovery in Explorer. It is started and stopped alongside the Samba service.&#x20;
