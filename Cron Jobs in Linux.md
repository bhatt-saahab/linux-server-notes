A cron job is a scheduled task in Linux or Unix-like systems that automates processes to run at specified intervals or times. It is managed by the cron daemon (crond), which continuously runs in the background and checks for tasks defined in the crontab (cron table).

## Checking if Cron is Installed

Before using cron, ensure it is installed:

```
rpm -qa | grep cron

```

```
rpm -qa | grep crontabs

```

If not installed:

```
yum install crontabs

```

Verify installation:

```
rpm -qi crontabs

```

```
rpm -ql crontabs

```

```
rpm -qc crontabs

```

```
rpm -qd crontabs

```

## Managing Cron Service

Enable and start the cron service:

```
systemctl enable crond

```

```
systemctl start crond

```

```
systemctl status crond

```

## Cron Directories

Cron jobs are managed in specific directories:

```
ls -th /etc/ | grep cron

```

```
ls -l /etc/cron.*

```

You'll typically see:

- /etc/cron.deny
- /etc/crontab
- /etc/cron.d/
- /etc/cron.hourly/
- /etc/cron.daily/
- /etc/cron.weekly/
- /etc/cron.monthly/

## System-Wide Cron Directories

Linux systems provide predefined directories for scheduling jobs at specific intervals without manually editing the crontab file. These directories are managed by run-parts, a command that executes all scripts inside a directory.

These are controlled by run-parts, which executes all scripts inside a directory.

| Directory | Frequency |
| --- | --- |
| /etc/cron.hourly | Runs every hour |
| /etc/cron.daily | Runs once per day |
| /etc/cron.weekly | Runs once per week |
| /etc/cron.monthly | Runs once per month |

### 1. /etc/cron.hourly

This directory contains scripts that run once every hour.
The execution time is controlled by /etc/crontab or system cron settings.
Any executable script placed in this directory runs automatically each hour.

Example: A script to sync logs every hour

```
mkdir -p /backup/logs/

```

```
vim /etc/cron.hourly/log_sync

```

```
#!/bin/bash
rsync -av /var/log/ /backup/logs/

```

Save this script as /etc/cron.hourly/log_sync and make it executable:

```
chmod +x /etc/cron.hourly/log_sync

```

### 2. /etc/cron.daily

This directory contains scripts that run once per day.
The default execution time is usually 3:00 AM, but it can vary by system.
Common tasks include system updates, log rotation, and database backups.

Example: A script to clean temporary files daily

```
vim /etc/cron.daily/clean_tmp

```

```
#!/bin/bash
find /tmp -type f -mtime +7 -delete

```

Save this script as /etc/cron.daily/clean_tmp and make it executable:

```
chmod +x /etc/cron.daily/clean_tmp

```

### 3. /etc/cron.weekly

Scripts in this directory run once per week.
The default execution time is usually Sunday at 4:00 AM, but this depends on system settings.
Weekly tasks may include full system backups, package updates, or maintenance scripts.

Example: A script to create a weekly system backup

```
vim /etc/cron.weekly/system_backup

```

```
#!/bin/bash
tar -czf /backup/system_backup_$(date +\\%F).tar.gz /home /etc /var

```

Save this script as /etc/cron.weekly/system_backup and make it executable:

```
chmod +x /etc/cron.weekly/system_backup

```

### 4. /etc/cron.monthly

Scripts in this directory run once per month.
The default execution time is usually the first day of the month at 5:00 AM.
Suitable for long-term maintenance tasks such as database cleanup, report generation, or software updates.

Example: A script to generate a monthly user activity report

```
vim /etc/cron.monthly/user_report

```

```
#!/bin/bash
cat /var/log/secure | grep password > /backup/user_activity_$(date +\\%Y-%m).log

```

Save this script as /etc/cron.monthly/user_report and make it executable:

```
chmod +x /etc/cron.monthly/user_report

```

```
ls -th /etc/cron

```

## Manually Running Cron Scripts

```
run-parts /etc/cron.hourly

```

```
run-parts /etc/cron.daily

```

```
run-parts /etc/cron.weekly

```

```
run-parts /etc/cron.monthly

```

## Viewing and Editing Crontab

To list the configuration files for crontabs:

```
rpm -qc crontabs

```

To edit the system crontab file:

```
vim /etc/crontab

```

For manual reference:

```
man crontab

```

Online crontab scheduling reference:

- Crontab Guru

## Checking Cron Logs

To debug or check cron execution logs, use:

```
cat /var/log/cron

```

## Cron Environment

Absolute paths are required (/usr/bin/php not just php).
Set environment variables in crontab:

```
SHELL=/bin/bash

```

```
PATH=/sbin:/bin:/usr/sbin:/usr/bin

```

```
MAILTO=root

```

## Cron Logs & Debugging

View logs:

```
cat /var/log/cron

```

Monitor in real-time:

```
tail -f /var/log/cron

```

## Cron vs. Anacron

Cron: Skips jobs if the system is off.
Anacron: Ensures jobs (daily/weekly/monthly) still run after the system restarts.

## Security Considerations

- cron.allow: users allowed to use cron.
- cron.deny: users denied from using cron.
Located in /etc/

## Working with Date and Time in Cron Jobs

To fetch the current date and time:

```
date

```

Formatting date output:

```
date "+%F"

```

```
date "+%F-%H-%M"

```

```
date "+%d-%m-%Y-%H-%M"

```

```
date "+%H-%M-%d-%m-%Y"

```

Creating files with timestamped names:

```
touch `date "+%d-%m-%Y-%H-%M"`*

```

```
touch `date "+%d-%m-%Y-%H-%M-%S"`

```

Archiving files with timestamps:

```
tar -cvf /tmp/`date "+%d-%m-%Y-%H-%M"`.tar /home/armour

```

```
tar -cvf /tmp/`date "+\\%d-\\%m-\\%Y-\\%H-\\%M"`.tar /home/armour/

```

## Cron Job Scheduling Examples

| Schedule | Cron Expression | Example Command |
| --- | --- | --- |
| Every minute | * * * * * | echo "Runs every minute" >> /tmp/cron.log |
| First 10 min of hour | 1-10 * * * * | echo "Runs 1-10 min of every hour" |
| Every 2 hours at :05 | 5 */2 * * * | echo "Every 2 hours at :05" |
| Specific minutes | 1,10,15,35 * * * * | echo "Runs at 1,10,15,35 min" |
| Daily 2:30 AM | 30 2 * * * | /path/to/backup.sh |
| Sundays 3:00 AM | 0 3 * * 0 | rm -rf /tmp/* |
| Every 10 min | */10 * * * * | df -h > /var/log/disk_usage.log |
| 1st of month midnight | 0 0 1 * * | php /path/to/script.php |
| Weekdays 9 AM | 0 9 * * 1-5 | /path/to/weekday_job.sh |
