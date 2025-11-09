This guide walks through the process of setting up a basic FTP server using vsftpd on a Linux system. The goal is to allow local users (u1, u2, etc.) to log in and access their own home directories securely.

## Create Local Users

- Create two local user accounts (u1 and u2). These users will be used to test FTP access:

```
useradd u1

```

```
useradd u2

```

- Set passwords for the new users:

```
passwd u1

```

```
passwd u2

```

**Note:** It's important that each user has a valid password to authenticate via FTP.

## Configure vsftpd

- Edit the vsftpd configuration file:

```
vim /etc/vsftpd/vsftpd.conf

```

**Example content for /etc/vsftpd/vsftpd.conf:**

```
# Disallow anonymous logins
anonymous_enable=NO

# Enable local user login
local_enable=YES

# Allow FTP write commands (upload, delete, etc.)
write_enable=YES

# Set default permissions for uploaded files
local_umask=022

# Display directory-specific messages if any
dirmessage_enable=YES

# Enable logging of uploads/downloads
xferlog_enable=YES

# Ensure data connections use port 20
connect_from_port_20=YES

# Use standard FTP xferlog format
xferlog_std_format=YES

# Chroot local users into their home directories
chroot_local_user=YES

# Allow writeable chroot (otherwise vsftpd may reject writeable home dirs)
allow_writeable_chroot=YES

# Enable passive mode with defined port range
pasv_enable=YES
pasv_min_port=55000
pasv_max_port=55999

# PAM authentication service
pam_service_name=vsftpd

# Enable user list
userlist_enable=YES

# Enable IPv6 listener (disable 'listen=YES' if this is enabled)
listen=NO
listen_ipv6=YES

```

**Note:** Save and exit the file after editing.

## Check or Configure SELinux (If Applicable)

- View the current SELinux mode:

```
cat /etc/selinux/config

```

**Example output:**

```
# SELinux status
SELINUX=disabled
SELINUXTYPE=targeted

```

- If SELinux is enforcing, you may need to enable the `ftp_home_dir` boolean:

```
setsebool -P ftp_home_dir 1

```

## Restart vsftpd Service

- Apply configuration changes by restarting the vsftpd service:

```
systemctl restart vsftpd.service

```

- Check if the service is active:

```
systemctl status vsftpd.service

```

- Enable it at boot:

```
systemctl enable vsftpd.service

```

## Setup Proper Home Directory Permissions

- Ensure that user directories exist and have correct permissions:

```
mkdir -p /home/u1 /home/u2

```

```
chown u1:u1 /home/u1

```

```
chown u2:u2 /home/u2

```

```
chmod 755 /home/u1 /home/u2

```

**Note:** You can use `chmod 700` if you want their home directories to be private.

## Configure the Firewall (Optional)

- This opens port 21, used by FTP control connections:

```
firewall-cmd --permanent --add-service=ftp

```

- If you're running a firewall (like firewalld), allow the passive port range:

```
firewall-cmd --permanent --add-port=55000-55999/tcp

```

```
firewall-cmd --reload

```

**Note:** This ensures that FTP passive mode will work properly, especially behind NAT.

## Test FTP Login

- Test the FTP server using a client like the command-line ftp tool:

```
ftp 192.168.1.50

```

**Example login:**

```
Name (192.168.1.50): u1
Password: 123

```

**Note:** If the connection is successful, you will be placed in `/home/u1` and have access to read/write files (based on permissions).

## Troubleshooting Tips

### FTP Login Fails

- Check authentication logs:

```
journalctl -xe | grep vsftpd

```

- Ensure the user is not listed in `/etc/vsftpd/ftpusers` (users in this file are denied FTP access).

### Directory Listing Fails

- Passive ports might be blocked by a firewall.
• Ensure proper permissions on home directories.
• Ensure SELinux (if enabled) is not preventing access.

### Optional: Add Banner Message

- You can add a custom banner to display to users on login:

```
ftpd_banner=Welcome to the Armour FTP server.

```

**Note:** Add this line to your `/etc/vsftpd/vsftpd.conf`.

## Useful Resources

- vsftpd man page
• Logs: `/var/log/vsftpd.log`, `/var/log/xferlog`, `/var/log/secure`

# Allow Root Login on vsftpd (FTP Server)

By default, vsftpd denies FTP access to privileged users like root for security reasons. However, in certain controlled environments (e.g., labs, VMs), you might want to enable root FTP login.

**Warning:** Allowing root to log in via FTP is a major security risk and should never be done on a production or internet-facing server. Proceed only in isolated, secure environments.

## Edit the /etc/vsftpd/ftpusers File

This file lists users who are always denied FTP access, even if they have a valid shell and password. You must remove or comment out the root entry.

- Edit the file:

```
vim /etc/vsftpd/ftpusers

```

**Original example (root is blocked):**

```
# Users that are not allowed to login via ftp
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
uucp
operator
games
nobody

```

**Updated version (root is allowed):**

```
# Users that are not allowed to login via ftp
# root
bin
daemon
adm
lp
sync
shutdown
halt
mail
news
uucp
operator
games
nobody

```

## Edit the /etc/vsftpd/user_list File

This file also lists users who are denied access by default (unless configured otherwise via `userlist_deny` setting). You must comment out root here as well.

- Edit the file:

```
vim /etc/vsftpd/user_list

```

**Original:**

```
# vsftpd userlist
# If userlist_deny=NO, only allow users in this file
# If userlist_deny=YES (default), never allow users in this file, and
# do not even prompt for a password.
# Note that the default vsftpd pam config also checks /etc/vsftpd/ftpusers

root
bin
daemon
adm
lp
sync
shutdown
halt
mail
news
uucp
operator
games
nobody

```

**Updated version:**

```
# vsftpd userlist
# If userlist_deny=NO, only allow users in this file
# If userlist_deny=YES (default), never allow users in this file, and
# do not even prompt for a password.
# Note that the default vsftpd pam config also checks /etc/vsftpd/ftpusers
# root
bin
daemon
adm
lp
sync
shutdown
halt
mail
news
uucp
operator
games
nobody

```

## Restart the vsftpd Service

- After making changes, restart the vsftpd service to apply them:

```
systemctl restart vsftpd.service

```

- Check its status:

```
systemctl status vsftpd.service

```

## Optional: Verify root Shell and Password

- Make sure that the root account has a valid login shell and password:

```
grep root /etc/passwd

```

**Expected output:**

```
root:x:0:0:root:/root:/bin/bash

```

- To set (or reset) the root password:

```
passwd root

```

## Test Root FTP Login

- You can now attempt to log in via FTP using root:

```
ftp 192.168.1.50

```

**Credentials:**

```
Username: root
Password: [your root password]

```

## Additional Notes

- vsftpd.conf settings related to user access do not need modification if `local_enable=YES` is already set.
• For secure setups, it's recommended to:
• Use SSH/SFTP instead of FTP.
• Use virtual users with limited access if FTP is required.

# Login with Selected Users on vsftpd

You can restrict FTP login access to a specific list of users using the `userlist_file` directive in vsftpd.

## Create the Allowed Users File

- Edit or create the following file:

```
vim /etc/vsftpd/allow_users

```

- Add the usernames you want to allow:

```
infosec
armour
u1

```

**Note:** Only these users will be allowed to log in via FTP.

## Edit the main configuration file

- Edit the configuration file:

```
vim /etc/vsftpd/vsftpd.conf

```

**Ensure the following lines are present or updated correctly:**

```
# Enable local users to log in
local_enable=YES

# Enable write commands like STOR
write_enable=YES

# Allow passive FTP range
pasv_enable=YES
pasv_min_port=55000
pasv_max_port=55999

# Custom FTP login banner
ftpd_banner=Welcome to ARMOUR FTP service.

# Restrict access to only specified users
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/allow_users

# Allow chroot with write access if needed
allow_writeable_chroot=YES

# PAM authentication service
pam_service_name=vsftpd

# Use TCP Wrappers for host-based access control
tcp_wrappers=YES

# Enable IPv6 (vsftpd runs in standalone mode if listen=YES)
listen=NO
listen_ipv6=YES

```

**The key lines for restricting access to selected users are:**
• `userlist_enable=YES`
• `userlist_deny=NO`
• `userlist_file=/etc/vsftpd/allow_users`

**Note:** `userlist_deny=NO` means only users in the list are allowed. If `userlist_deny=YES` (default), the users in the list are blocked instead.

## Restart vsftpd Service

- Apply the changes:

```
systemctl restart vsftpd.service

```

## Example Test

- Login using FTP client:

```
ftp <your-server-ip>

```

- Try logging in as one of the allowed users (e.g., u1) and verify access. Any other users should receive a login denial.

## Notes

- Make sure `ftpusers` and `user_list` files do not block your allowed users.
• If a user appears in `/etc/vsftpd/ftpusers` or `/etc/vsftpd/user_list`, they will be blocked even if they are listed in `allow_users`.
• To avoid this, comment out or remove those users from those two files.

# FTP Path Configuration in vsftpd

This section walks through how to set a custom FTP root directory (e.g., `/backup`) for all FTP users using vsftpd.

## Create and Set Permissions on the FTP Directory

- Create a shared directory for FTP users:

```
mkdir /backup/

```

- Set permissive permissions to allow access:

```
chmod -R 1777 /backup/

```

**Note:** 1777 ensures all users can write but only delete their own files, similar to `/tmp`. Adjust permissions based on your security policy.

## Configure vsftpd to Use the Custom Directory

- Edit the configuration file:

```
vim /etc/vsftpd/vsftpd.conf

```

**Make sure the following options are present or updated:**

```
# Disallow anonymous logins
anonymous_enable=NO

# Enable local users to log in
local_enable=YES

# Allow FTP write commands (upload, delete, rename, etc.)
write_enable=YES

# Set default permissions for uploaded files
local_umask=022

# Show directory welcome messages
dirmessage_enable=YES

# Log uploads/downloads
xferlog_enable=YES
xferlog_std_format=YES

# Use port 20 for data connections
connect_from_port_20=YES

# Customize FTP banner
ftpd_banner=Welcome to ARMOUR FTP service.

# Chroot users to their home directory (or specified path)
chroot_local_user=YES

# Allow writable chroot directories
allow_writeable_chroot=YES

# Set the custom root directory for all users
local_root=/backup

# Passive mode settings (adjust as needed)
pasv_enable=YES
pasv_min_port=55000
pasv_max_port=55999

# Use PAM for authentication
pam_service_name=vsftpd

# Use host-based access control (via /etc/hosts.allow and /etc/hosts.deny)
tcp_wrappers=YES

# Listen on IPv6 (set "listen=YES" instead for IPv4)
listen=NO
listen_ipv6=YES

# Optional: Restrict access to a list of allowed users
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/allow_users

```

## Restart the FTP Service

- Apply the new settings:

```
systemctl restart vsftpd.service

```

## Testing

- Create a local user (if not already existing):

```
adduser ftpuser

```

- Add the user to the allowed list (if using userlist enable):

```
echo "ftpuser" >> /etc/vsftpd/allow_users

```

- Try connecting via FTP:

```
ftp <your-server-ip>

```

## Additional Reference

- DigitalOcean guide for vsftpd user directory setup: https://www.digitalocean.com/community/tutorials/how-to-set-up-vsftpd-for-a-user-s-directory-on-ubuntu-16-04

## Notes

- If using `chroot_local_user=YES`, make sure users do not have write permission to their chroot directory unless `allow_writeable_chroot=YES` is set.
• `local_root=/backup` makes all FTP users land in `/backup`, not their own `/home/username` directories. Use per-user configuration or scripts if you need individual directories.
