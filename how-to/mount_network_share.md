# Mount Network Share

Kodi can natively mount SMB, NFS, SFTP, WebDAV (and more) remote filesystems (shares) to read media for playback, but many applications that write content, e.g. TVHeadend storing TV recordings, must write to "local" storage. Remote SMB and NFS shares can be "mounted" to the local filesystem using kernel mounts configured through systemd .mount files.

The following NAS configuration is used in the examples below:

* NAS IP: `192.168.1.222`
* Username: `nasuser1`
* Password: `123nas`
* Share name: `recordings`
* Full address to share: `\\192.168.1.222\recordings`

## SMB Shares

#### 1. Create the folder where the share should be mounted

Connect to your LibreELEC HTPC with SSH.

`mkdir /storage/recordings`

#### 2. Create the systemd .mount file

**IMPORTANT:** The filename uses hyphens to separate elements of the filesystem path to the share mount-point, e.g. `/storage/recordings` will be `storage-recordings.mount` and sub folders, e.g. `/storage/recordings/tv` would be `storage-recordings-tv.mount`

Create the .mount file:

`nano /storage/.config/system.d/storage-recordings.mount`

Below is an example of the mount definition file for a Samba share:

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

#### 3. Things to edit

Address of your share. Remember to always use / slashes:

`What=//192.168.1.222/recordings`

Path where the Share should be mounted:

`Where=/storage/recordings`

Options:

`Options=username=nasuser1,password=123nas,rw,vers=2.1`

`username=` Username of your network share\
`password` Password of your network share\
`rw` Read/write access\
`vers=2.1` Version of the Samba protocol, `2.1` is supported since Windows 7 several [other versions](https://wiki.samba.org/index.php/Samba3/SMB2#Introduction) are supported too

#### 4. Enable the mount

Finally we need to enable the mountpoint.

`systemctl enable storage-recordings.mount`

#### 5. Reboot

Reboot your system to check if the mount works.

#### 6. Helpful command for troubleshooting

Get status and error messages from the mount point:

`systemctl status storage-recordings.mount`

Remove mount point and disable it:

`systemctl disable storage-recordings.mount`

## NFS Shares

#### 1. Create the folder where the share should be mounted

Connect to your LibreELEC HTPC [with SSH](https://app.gitbook.com/accessing\_libreelec).

`mkdir /storage/recordings`

#### 2. Create the systemd definition file

**Important:** you need to set the filename for the definition file according to the folder where you want to mount your share . In our case: `storage-recordings.mount` represents the path -> `/storage/recordings`. If you add a subfolder: `storage-recordings-tv.mount` represents the path -> `/storage/recordings/tv`.

`nano /storage/.config/system.d/storage-recordings.mount`

Contents of the definition file for a NFS share:

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

#### 3. Things to edit

Address of your share;

`What=192.168.1.222:/usr/data2/video`

Path where the share should be mounted:

`Where=/storage/recordings`

Options: At this section you are able to define specific NFS options, such as the NFS version. In the example below we set Type for an NFSv3 share.

`Type=nfs`

To use an NFSv4 share:

`Type=nfs4`

#### 4. Start it for a test

`systemctl start storage-recordings.mount`

Note: That's only a test and the mount won't be available after a reboot. To make it available after boot you have to "enable" the service first.

#### 5. Enable the mount

If the previous test worked, enable the service via:

`systemctl enable storage-recordings.mount`

#### 6. Reboot

Reboot your system to see if the mount is available after boot.

#### 7. Helpful commands for troubleshooting

Get status and error messages from the mount point:

`systemctl status storage-recordings.mount`

Remove mount point and disable it:

`systemctl disable storage-recordings.mount`

## **Apple TimeCapsule**

TimeCapsule devices share files using an Apple dialect of SMB that is not compatible with the Samba `smbclient` Kodi uses to connect to SMB shares. To access media on a TimeCapsule you can follow the steps described above for connecting to Samba shares with a systemd storage mount, but with one difference: the `Options` configuration must force SMB v1.0 and legacy NTLM authentication or the mount will fail. See below:

```
Options=username=MyUser,password=MyPass,sec=ntlm,vers=1.0
```

SMB v1.0 is widely considered to be insecure, but TimeCapsules no longer receive software updates and there is no alternative; SMB v2/v3 are not supported.

## Auto Mounting

In some edge cases where the remote filesystem may be unavailable when LibreElec boots or the remote filesystem has a chance of disconnecting, an automount is an easy way to mount without the risk of failure and with the option to attempt to connect/reconnect with no extra effort.

It is basically a safe way to retry mounting the remote filesystem.

### How it works

Because we enable the automount service, we can disable the mount service. Which in turn allows the system to boot without errors, even if the network share is not available (it will still show the files as missing until the share is available, but this is expected). What the automount does is try to connect to the network share when the local mount path is accessed by the filesystem. So when trying to play media from the mount path, or trying to update the library (assuming the mount path is in your media library).

#### 1. Create the folder and systemd `.mount` definition file

Follow the relevant instructions above to create a systemd `.mount` definition file for your network share.

**IMPORTANT:** It is recommended to follow the instructions above completely and add the mounted path to your library in Kodi before proceeding with the instructions below.

#### 2. Disable the mount service in systemd

If you've already enabled the mount service in systemd, disable it to prevent errors on boot when the network share is unavailable.

`systemctl disable storage-recordings.mount`

#### 3. Create a systemd `.automount` definition file

Filename restrictions are the same as for the `.mount` definition file. Meaning that if your `.mount` file is named `storage-recordings.mount` your automount file should be named `storage-recordings.automount`.

`nano /storage/.config/system.d/storage-recordings.automount`

Contents of the automount definition file:

```
[Unit]
Description=test automount for recordings

[Automount]
Where=/storage/recordings
TimeoutIdleSec=0

[Install]
WantedBy=multi-user.target
```

#### 4. Things to edit

Path of the mount in question:

`Where=/storage/recordings`

Idle timeout (time before systemd should unmount the network share, 0 means disabled):

`TimeoutIdleSec=0`

#### 5. Enable the automount service

`systemctl enable storage-recordings.automount`

#### 6. Reload systemd to apply changes

`systemctl daemon-reload`

#### 7. Test the automount

If you have already added the mount path to your library in Kodi, you can test the automount by trying to access the folder inside Kodi. If all went well, the network share will mount as soon as you attempt to access it's mount path.

If the network share is available when LibreElec is starting up, it will be mounted during the boot process.

#### 8. Helpful commands for troubleshooting

Get status and error messages from the automount (great for seeing when a mount was attempted):

`systemctl status storage-recordings.automount`

**IMPORTANT:** Running the above command will NOT show mount errors, to see mount errors you will still need to run `systemctl status` on the `.mount` service (`systemctl status storage-recordings.mount`).

Disable the automount:

`systemctl disable storage-recordings.automount`
