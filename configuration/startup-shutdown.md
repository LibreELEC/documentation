# Startup & Shutdown

## autostart.sh

The `autostart.sh` script runs at the start of userspace boot. It can be used to run commands before Kodi starts. It does not exist by default, but you can create it using nano.

```text
nano /storage/.config/autostart.sh
```

You can place any commands in the script, but note they will block the boot process until they complete, and the network stack will not be up when the script runs, so most uses of `autostart.sh` require you to "background" and delay the commands, e.g. the following script sleeps for 20 seconds and then runs a `kodi-send` command to update the video library.

```text
(
 sleep 20
 kodi-send --host=127.0.0.1 -a "UpdateLibrary(video)"
)&
```

## shutdown.sh

The `shutdown.sh` script run during the shutdown process. It does not exist by default, but you can create it using nano.

```text
nano /storage/.config/shutdown.sh
```

Unlike the `autostart.sh` script, the `shutdown.sh` script should follow the following template to ensure commands are executed during the correct event, i.e. you can run different commands for a `reboot` event to `halt` event, or use the `*` as a catch-all.

```text
case "$1" in
  halt)
    # your commands here
    ;;
  poweroff)
    # your commands here
    ;;
  reboot)
    # your commands here
    ;;
  *)
    # your commands here
    ;;
esac
```

