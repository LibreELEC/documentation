# Add content via Samba Shares

LibreELEC configures a set of default Samba (SMB) shares, which you can use to copy content to and from your system.

The default shares are listed in the `/storage/.config/samba.conf.sample` file, and if you want to modify the default shares, you can do that too!

### Accessing the Shares

On a Mac, while you're in Finder, select Go > Connect to Server..., the click 'Browse' and find the LIBREELEC server (the name of the server corresponds to the name you set in Kodi's settings). Alternatively, don't click 'Browse', just type in `smb://LIBREELEC/` and press Enter.

On Windows, in Explorer, type in `\\LIBREELEC` and press Enter, and you should see a list of all the shares.

If you are prompted for a password, you should be able to log in as a 'Guest' instead, and have full access to the files on any of the shares.

Copy files into the shares as needed, and in Kodi's UI, you can [Add Video Sources](https://kodi.wiki/view/Adding\_video\_sources) that include the media you've copied across.

### Modifying the Default Shares

You can either create a fresh Samba config file, or copy the sample file and modify it to your liking:

```
cp /storage/.config/samba.conf.sample /storage/.config/samba.conf
```

After modifying the shares, restart Samba:

```
systemctl smbd restart
```

...or just reboot, and the new settings should take effect after a restart.

The sample configuration file is recreated on every reboot, so you should not modify that file. Because that file changes over time, you can also create your own `samba.conf` file, and [`include` the sample file inline](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html#idm4527).
