# Mounting network shares

Examples for mounting an network share to LibreELEC. This might be
useful if you run a Tvheadend Server at LE and like to record to your
NAS and similar tasks where you need a connection to an network storage
because the software can't handle it.

### Sample NAS setup

  - NAS IP: `192.168.1.222`
  - Share user/pass: `nasuser1` / `123nas`
  - Share name: `recordings`
  - Full address to share: `\\192.168.1.222\recordings`

# Samba Share
## Mounting a Samba share

### 1. Create the folder where the share should be mounted

Connect to your LibreELEC HTPC with SSH.

`mkdir /storage/recordings`

  
### 2. Create the systemd definition file

**Important:** you need to use the filename for the definition file
according to the folder where you want to mount your share .  
In our case `storage-recordings.mount` represent path -\> `/storage/recordings`.  
If you like an subfolder `storage-recordings-tv.mount` represent path -\> `/storage/recordings/tv`.  
  
`nano /storage/.config/system.d/storage-recordings.mount`

Content of the definition file for a Samba share.

```
[Unit]
Description=cifs mount script
Requires=network-online.service
After=network-online.service
Before=kodi.service

[Mount]
What=//192.168.1.222/recordings
Where=/storage/recordings
Options=username=nasuser1,password=123nas,rw,vers=2.1
Type=cifs

[Install]
WantedBy=multi-user.target
```

### 3. Things to edit

Address of your share, remember to use / slashes:

`What=//192.168.1.222/recordings`

Path where the Share should be mounted:

`Where=/storage/recordings`

Options:

`Options=username=nasuser1,password=123nas,rw,vers=2.1`

`username=` Username of your network share  
`password` Password of your network share  
`rw` Read/write access  
`vers=2.1` Version of the Samba protocol, `2.1` is supported since
Windows 7 several [other versions](https://wiki.samba.org/index.php/Samba3/SMB2#Introduction) are supported too

### 4. Enable the mount

Finally we need to enable the mountpoint.

`systemctl enable storage-recordings.mount`

### 5. Reboot

Reboot your system to check if the mount works.

### 6. Helpful command for troubleshooting

Get status and error messages from the mount point.

`systemctl status storage-recordings.mount`

Remove mount point and disabling it.

`systemctl disable storage-recordings.mount`

# NFS Share

## Mounting a NFS share

### 1. Create the folder where the share should be mounted

Connect to your LibreELEC HTPC [with SSH](/accessing_libreelec).

`mkdir /storage/recordings`

  
### 2. Create the systemd definition file

**Important:** you need to use the filename for the definition file
according to the folder where you want to mount your share .  
In our case `storage-recordings.mount` represent path -\> `/storage/recordings`.  
If you like an subfolder `storage-recordings-tv.mount` represent path -\> `/storage/recordings/tv`.  
  
`nano /storage/.config/system.d/storage-recordings.mount`

Content of the definition file for a NFS share.

```
[Unit]
Description=test nfs mount script
Requires=network-online.service
After=network-online.service
Before=kodi.service

[Mount]
What=192.168.1.222:/usr/data2/video
Where=/storage/recordings
Options=
Type=nfs

[Install]
WantedBy=multi-user.target
```
  
### 3. Things to edit

Address of your share;

`What=192.168.1.222:/usr/data2/video`

Path where the share should be mounted:

`Where=/storage/recordings`

Options:
At this section you are able to define specific NFS options, such as NFS
version for example. In our example here, we don't need it and we are
assuming you are using a NFSv3 share.

Type:
`Type=nfs`

  
### 4. Start it for a test:

`systemctl start storage-recordings.mount`

Note: That's only a test and the mount won't be available after a
reboot. To make it available after boot you have to "enable" the service
first.

  
### 5. Enable the mount

If the previous test worked, then please enable the service via:

`systemctl enable storage-recordings.mount`

  
### 6. Reboot

Reboot your system to see if the mount is available after boot.

  
### 7. Helpful command for troubleshooting

Get status and error messages from the mount point.

`systemctl status storage-recordings.mount`

Remove mount point and disabling it.

`systemctl disable storage-recordings.mount`
