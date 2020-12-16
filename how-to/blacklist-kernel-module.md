# Blacklist Kernel Module

In most Linux distros modules are blacklisted by creating `/etc/modprobe.d/blacklist.conf` which contains the name of the module. In LibreELEC `/etc` is inside the read-only `SYSTEM` file so we provide an alternate location `/storage/.config/modprobe.d` which is overlaid onto the normal `/etc` location during boot.

### Example 

To blacklist the `rc-pinnacle-pctv-hd` module. Login to your LibreELEC box using SSH and confirm the module is loaded:

```text
lsmod
```

If the module is loaded, append the module name to `blacklist.conf`:

```text
echo "blacklist rc-pinnacle-pctv-hd" >> /storage/.config/modprobe.d/blacklist.conf
```

Now reboot to effect the change.

### Module vs Driver

This approach to blacklisting only works with modules that are compiled with `=m` to create an independent module; meaning they are loaded via modprobe. If the module has been compiled directly into the kernel with `=y` modprobe is not used and this method has no effect. Drivers that are compiled-in are normally controlled via kernel boot params.

