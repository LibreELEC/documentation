# Cron

cron can be used to schedule timed event execution of binaries and scripts, e.g. download of EPG guides on a repeating time interval (hourly, every 12 hours, etc.) or running a nightly backup script in the background at 2am.&#x20;

Running `crontab -e` will open the root user crontab in `nano` allowing you to paste content your cron commands. Use `ctrl+o` to save the file and `ctrl-x` to exit nano.&#x20;

For more info on cron commands, see: [https://help.ubuntu.com/community/CronHowto](https://help.ubuntu.com/community/CronHowto)
