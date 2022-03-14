# Safe Mode

## Safe Mode

This feature gives users the option to not end up in a Kodi restart loop if something went wrong on the Kodi side and the possibility to fix an existing installation.

### How does it work

If for whatever reason Kodi crashes and therefore created a crashlog more than 5 times in 900 seconds (15 minutes), then we will enter the safe mode. This will avoid that you won't be able to get a running Kodi in any way.

### Behind the scenes

What has happened once you entered the save mode? Pretty simple. Once you end up in safe mode, we did a backup of your existing Kodi installation in a different folder. From a technical point of view we simply renamed the existing (and failing) `/storage/.kodi` folder to `/storage/.kodi.FAILED` and triggered a reboot after that. That will mean your next Kodi start will end up with a vanilla Kodi but with a special red background and a notification screen, that you now have entered the save mode. It will look like this:

![](../.gitbook/assets/kodi-safe\_1.png)

### What to do now?

First you should read the infos given from from the notification screen above and also probably make notes if you might think you can't remember them later. Once that is done, you should follow this section which is similar to the "first run wizard" and gives you specific options to either enter a hostname, enable Samba and/or SSH or setting up your network connection like shown in the screenshots below:

![Set up the hostname](../.gitbook/assets/kodi-safe\_2.png)

![Set up your network](../.gitbook/assets/kodi-safe\_3.png)

![Enable Samba or SSH](../.gitbook/assets/kodi-safe\_4.png)

![Exit the wizard](../.gitbook/assets/kodi-safe\_5.png)

We recommend to enable both SSH and Samba as this might be necessary to debug why your Kodi ended up in the safe mode at all.

After you ended the wizard all your settings, add-ons, library and what not from the previous (failing) installation are stored inside the `/storage/.kodi.FAILED` folder. This folder can either be accessed by SSH or Samba as we also share this folder by default if it exists. Please read the following if you don't know how to access LibreELEC: Accessing LibreELEC

From now on, if you want to debug why Kodi entered the safe mode, we will completely focus to the `/storage/.kodi.FAILED` folder which contains all the necessary information we might need to see what happened.

So for providing the logfile via SSH use the following command:

`pastebinit /storage/.kodi.FAILED/temp/kodi.log`

or search via Samba for the ''Kodi-Failed'' folder and enter the ''temp''-subfolder to get the ''kodi.log'' logfile.

For the very best option, we recommend to use the log uploader option we built-in in LibreELEC.

### After debugging or fixing

After we might have found out what the problem might have caused and we also might have fixed it, you don't need to do anything else beside rebooting your machine. We will automatically restore the old (and probably fixed) installation and at the next boot you will directly boot into Kodi like you are used to.

Be aware, that if we haven't fixed anything, that you will again enter the safe mode to be able to fix your installation if Kodi crashes multiple times again.

### How to disable safe mode

It can be completely disabled by creating `/storage/.config/safemode.disable` .

Login via SSH and type touch `/storage/.config/safemode.disable`

### How to fix a broken add-on

As Kodi don't provide any help you have to manually remove the addon.

Login via SSH and type `rm -rf /storage/.kodi.FAILED/addons/addon.name`
