# Building (Windows WSL)

## Requirements

* Windows 10 or Windows 11 with installed WSL2 and Ubuntu WSL

## Workarounds to make it work

#### WSL adds Windows paths to the Linux path variable

```
wsl@PC:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/mnt/c/Windows/system32:/mnt/c/Windows:/mnt/c/Windows/System32/Wbem:/mnt/c/Windows/System32/WindowsPowerShell/v1.0/:/mnt/c/Windows/System32/OpenSSH/:/snap/bin
```

This breaks the build and leads to unpredictable problems.

To disable that behavior you need to create a file at your Ubuntu WSL `/etc/wsl.conf` and add these options to disable. Afterwards you need to reboot WSL or your system.

```
 [interop]
  enabled=false # enable launch of Windows binaries
  appendWindowsPath=false # append Windows path to $PATH variable
```

#### Building at the native NTFS formatted storage (C:/; D:/ ...) is dead slow

To interoperate with your Windows Desktop you want the git tree accessible from Windows.\
So you clone the Git Tree to a location at windows and try to use it at WSL.

For this example the Git Tree is cloned at `D:\WSL\LE` - `/mnt/d/WSL/LE`. \
If you start building the build folder would be located at `D:\WSL\LE\build.YOURPROJECT` .

To change the path for the build files you need to add some options to the LibreELEC options file `/home/YOURUSER/.libreelec/options` .

```
BUILD_DIR=/home/YOURUSER/LEbuild
```

After adding this line to the options file every build is now located at this folder.

The /home folder is located at the WSL ext4.vhdx and not at the native NTFS storage.

#### Optional: Move your WSL file to another location

There are several ways in moving your WSL file, the easiest solution is the dedicated tool [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline#install) . The version 3.5.0 is not supporting the current Windows Version so you need to use a more recent version or a development build.

`lxrunoffline move -n Ubuntu-20.04 -d d:\wsl`

###### AlpineWSL
Or you can use https://github.com/yuk7/AlpineWSL which creates ext4.vhdx on your wanted disk and automatically mounts it to WSL2 itself on startup.
Advanced instructions: https://gist.github.com/onomatopellan/90024008a0d8c8a2ed6fa57e8b64df54 
