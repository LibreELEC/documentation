# Log Files

If you experience problems, the best explanation of the issue is often in a log file. If you want to investigate yourself or ask others for support in the forum or the IRC channel, they will ask for a log file \(or files\) and configuration data to look at.

Kodi has `default` and `debug` log modes. Debug logs show more detail and are more useful for staff and forum regulars to work with. If you are asked for a log - assume the request is for a `debug` log and for the entire log file, and not just the small snippet you think is useful \(often we need to see and understand the configuration too\).

 Enable 'debug logging' at Settings → System → Logging, then paste or copy/share the logs.

## LibreELEC Settings

The settings add-on has a built-in "paste" function that grabs the current Kodi log file and other useful system configuration details and sends them to the [http://ix.io](http://ix.io) pastebin service. It is an easy way for inexperienced \(or command-line phobic\) users to share logs.

Navigate to LibreELEC → System → scroll down to `Paste system logs` and press enter to submit them. Once the upload completes the GUI will show a short URL that you can copy and use in a forum post or while chatting to developers on IRC.

## SSH/Console

 SSH into your LibreELEC machine and run the following command:

```text
pastekodi
```

 You will get a short [http://ix.io](http://ix.io) URL back. That will look like this:

```text
LibreELEC:~ # pastekodi 
http://ix.io/2f5
```

Copy and paste the URL link to a forum post or an IRC chat session, or review it yourself.

## Samba/SMB

If Samba \(SMB Sharing\) is enabled you can collect the log file by accessing the `Logfile` folder over the network. This is a special folder. When you access the folder it triggers a task to collect the current Kodi log and system configuration, and package everything into a single zip file that can be copied back to your local machine or unpacked in the share. The auto-created files are named with date and time to make it easier to find the latest file. Inside the zip are multiple files. The main one project staff will be interested in is `KODI.log`

**!! PLEASE DO NOT UPLOAD LOG/ZIP FILES TO THE FORUM !!**

Instead, open `KODI.log` in a text editor and copy/paste the content to an interenet "paste" service like [https://pastebin.com](https://pastebin.com) then share the paste URL. This makes reviewing your log a "one click" exercise so more people are likely to review it, and it saves a ton of disk space on our forum server.

## Kodi Log Uploader

The "Kodi Log Uploader" add-on can be installed from the Kodi add-on repo. It will detect that is being used on LibreELEC to collect the correct log files, and it performs the same task as the paste function in the LibreELEC settings add-on. The main difference is that Kodi Log Uploader displays a QR code on-screen to make the process of copying the pastebin URL for use easier \(for some\). It also sends the log to the Team Kodi pastebin service, which uses highlighting to make reading the logfile easier for developers and forum staff.

