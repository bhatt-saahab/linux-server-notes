## Clam Antivirus (ClamAV)

ClamAV is an open-source antivirus engine designed to detect trojans, viruses, malware, and other malicious threats on Linux systems. This guide covers installation, configuration, updating, and scanning using ClamAV.

1. Download Test Files
EICAR provides standard anti-malware test files to safely verify ClamAV functionality:
Download from EICARS
- Before installing ClamAV, ensure the required repositories are enabled:
    
    ```bash
    yue repolist all
    
    ```
    
- T Install ClamAV and its related packages:
    
    ```bash
    yum install clamav
    
    ```
    
- Check installed ClamAV package details:
    
    ```bash
    rpm -qi clamav
    
    ```
    
- List files installed by ClamAV:
    
    ```bash
    rpm -ql clamav
    
    ```
    
- FreshClam keeps ClamAV's virus database updated:
    
    ```bash
    yum install clamav-freshclan
    
    ```
    
- Run FreshClam manually to fetch the latest virus definitions:
    
    ```bash
    freshclam
    
    ```
    

(Consider enabling the FreshClam service for automatic updates.)

- Basic scan:
    
    ```bash
    clamscan
    
    ```
    
- Scan a specific directory:
    
    ```bash
    clamscan /root/
    
    ```
    
- Recursive scan:
    
    ```bash
    clamscan -r /root/
    
    ```
    
- Scan directory containing suspected threats:
    
    ```bash
    clamscan -r /root/webshells/
    
    ```
    
- To validate ClamAV, download harmless test malware files:
    
    ```bash
    wget --no-check-certificate <https://secure.eicar.org/eicar.com>
    
    ```
    
    ```bash
    wget --no-check-certificate <https://secure.eicar.org/eicar.com.txt>
    
    ```
    
    ```bash
    wget --no-check-certificate <https://secure.eicar.org/eicar_com.zip>
    
    ```
    
- Recursive scan of /root:
    
    ```bash
    clamscan -r /root/
    
    ```
    
- Show only infected files:
    
    ```bash
    clamscan -i -r /root/
    
    ```
    
- Log infected files to a log file:
    
    ```bash
    clamscan -i -r /root/ -l /root/clamav.log
    
    ```
    
- Scan and remove infected files (use with caution):
    
    ```bash
    clamscan --remove -i -r /root/
    
    ```
    
- Remove infections from a specific directory:
    
    ```bash
    clamscan --remove -r /root/webshells/
    
    ```
    

# Cron-Jobs-in-Linux

## Cron Jobs in Linux

A cron job is a scheduled task in Linux or Unix-like systems that automates processes to run at specified intervals or times. It is managed by the cron daemon ( crond), which continuously runs in the background and checks for tasks defined in the crotitab (cron table)

## Checking if Cron is Installed

- Before using cron, ensure it is installed:
    
    ```bash
    rpm -qa | grep cron
    
    ```
    
    ```bash
    rpm -qa | grep crontabs
    
    ```
    
- If not installed:
    
    ```bash
    yum install crontabs
    
    ```
    
- Verify installation:
    
    ```bash
    rpm -ql crontabs
    
    ```
    
    ```bash
    rpm -qi crontabs
    
    ```
    

## Managing Cron Service

- Enable and start the cron service:
    
    ```bash
    systemctl enable crond
    
    ```
    
    ```bash
    systemctl start crond
    
    ```
    
    ```bash
    systemctl status crond
    
    ```
    

## Cron Directories

Cron jobs are managed in specific directories:

- `ls -lh /etc/ | grep cron`
- `ls -1 /etc/cron*`

You'll typically see:

1 /etc/cron.deny

2 /etc/crontab

3 /etc/cron.d/

4 /etc/cron.hourly/

5 /etc/cron.daily/

6 /etc/cron.weekly/

7 /etc/cron.monthly/

## System-Wide Cron Directories

These are controlled by run-parts, which executes all scripts inside a directory.

| Directory | Frequency |
| --- | --- |
| /etc/cron.hourly | Runs every hour |
| /etc/cron.daily | Runs once per day |
| /etc/cron.weekly | Runs once per week |
| /etc/cron.monthly | Runs once per month |

## Example: Hourly Sync Logs

- `vim /etc/cron.hourly/log_sync`
    
    #!/bin/bash
    
    rsync -av /var/log/ /backup/logs/
    
- Make it executable:
    
    ```bash
    chmod +x /etc/cron.hourly/log_sync
    
    ```
    

(Similar daily, weekly, and monthly examples can be created for cleanup, backups, reports, etc.)

## Manually Running Cron Scripts

- `run-parts /etc/cron.hourly`
- `run-parts /etc/cron.daily`
- `run-parts /etc/cron.weekly`
- `run-parts /etc/cron.monthly`

## Crontab Basics

Crontab entries follow this format:

1 # minute (0-59)

2 # hour (023)

3 # day of month (131)

4 # month (1- 12 or jan-dec)

5 # day of week (0 6 or sun-sat)

6 #

7 # ***** user command-to-execute

## Common Symbols

| Symbol | Meaning |
| --- | --- |
| * | Any value |
| , | List separator (e.g., 1,5,10) |
| - | Range (e.g., 1-5) |
| / | Step values (e.g., */5 = every 5) |
