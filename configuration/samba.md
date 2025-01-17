# Samba

The embedded Samba server in LibreELEC is enabled by default and provides CIFS/SMB shares that can be accessed from all modern OS to [add-content-via-samba-shares.md](../how-to/add-content-via-samba-shares.md "mention") or collect [log-files.md](../support/log-files.md "mention") for support or access the `/storage/.config` folder to make basic system changes. Samba starts with configuration from `/storage/.config/samba.conf` if it exists, else it starts with a default configuration from `/etc/samba/smb.conf`  which is inside the read-only filesystem.&#x20;

Samba services can be disabled from the LibreELEC settings addon.

{% hint style="warning" %}
Samba **server** configuration files do not configure the SMB **client** within Kodi
{% endhint %}

## Removable Media

The OS automatically creates Samba shares for removable media devices like external USB drives that are connected at boot time. Share names are based on the disk label, e.g. `/var/media/MyBook` will result in the `\\LIBREELEC\MYBOOK` share being auto-created. If your disk does not have a label and has mounted using a hardware identifier, e.g. `/var/media/sda1-ata-KINGSTON_SNV2S20` the best and correct way to change the share name is setting a label:

```
# e2label /dev/sda1 "KINGSTON"
```

This will result in the auto-created SMB share `\\LIBREELEC\KINGSTON`  which will be persistent.

{% hint style="danger" %}
Users often workaround unlabeled disks and resulting weird share names by creating custom shares. This works if you have one disk. If you have more than one disk boot time probe order is not guaranteed so `/var/media/sda1-blah` can change to `/var/media/sdb1-blah` breaking the shares. Using `e2label`is the better solution.
{% endhint %}

## Custom Shares

Rename `/storage/.config/samba.conf.sample`  to `/storage/.config/samba.conf` then edit `samba.conf` to make changes to the `[global]`section or add/remove shares, then reboot to restart the OS with the new Samba configuration. The format is simple to crib from the sample file, e.g. to add a new \[Addons] share, append this to the conf:

```
[Addons]
  path = /storage/.kodi/addons
  available = yes
  browseable = yes
  public = yes
  writeable = yes
```

To remove a share from the default list, comment it out, e.g. to remove `[Emulators]` change the conf file to look like this, then restart:

```
  public = yes
  writeable = yes

#[Emulators]
#  path = /storage/emulators
#  available = yes
#  browseable = yes
#  public = yes
#  writeable = yes

[Configfiles]
  path = /storage/.config
  available = yes
```

{% hint style="info" %}
For more information on Samba server configuration options please refer to the official reference guide here: [https://www.samba.org/samba/docs/4.9/man-html/smb.conf.5.html](https://www.samba.org/samba/docs/4.9/man-html/smb.conf.5.html)
{% endhint %}

## Time Machine&#x20;

Samba can serve SMB shares compatible with Apple's [Time Machine](https://support.apple.com/en-ae/104984) backup snapshot functionality in macOS. Time Machine capabilities are not enabled by default and require a custom `samba.conf` (as explained above) with the following options appended to the `[global]`section:

```
# samba TimeMachine support
  min protocol = SMB2
  ea support = yes
  vfs objects = catia fruit streams_xattr
  delete veto files = true
  fruit:metadata = stream
  fruit:veto_appledouble = no
  fruit:nfs_aces = no
  fruit:posix_rename = yes
  fruit:zero_file_id = yes
  fruit:wipe_intentionally_left_blank_rfork = yes
  fruit:delete_empty_adfiles = yes
```

Note that `fruit:model = Xserve` is already defined in the default configuration. Some users prefer the appearance of the `fruit:model = MacSamba`  icon for their shares.

Next add/append a `TimeMachine` share to the configuration file:

```
[TimeMachine]
  path = /storage/timemachine/
  fruit:time machine = yes
  # fruit:time machine max size = 250GB
  read only = no
  guest ok = yes
```

Time Machine provides a roll-up strategy for snapshots to reduce disk consumption, but you can force a specific space quota/restriction by uncommenting `fruit:time machine max size` and setting the size value. In the example above max size has been set to 250GB.

{% hint style="info" %}
see [https://wiki.samba.org/index.php/Configure\_Samba\_to\_Work\_Better\_with\_Mac\_OS\_X](https://wiki.samba.org/index.php/Configure_Samba_to_Work_Better_with_Mac_OS_X) for more information about macOS coexistence configuration and features.
{% endhint %}
