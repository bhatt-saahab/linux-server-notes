Common Internet File System (CIFS) is a dialect of the Server Message Block (SMB) protocol. It is widely used for shared access to files, printers, and serial ports between systems on a local network.

**SMB (Server Message Block):**

A network protocol for sharing files, printers, and more between nodes.

**CIFS:**

A particular implementation/dialect of SMB, historically used in Windows systems.

**Samba:**

An open-source project that implements SMB/CIFS on Unix-like systems, enabling file and print sharing with Windows clients.

## Use Cases

- Seamless file sharing between Linux, Windows, and macOS.
• Printer sharing across mixed operating systems.
• Integration into Windows domains via Active Directory.
• Hosting cross-platform network shares for users and applications.

## Common Paths And Configs

### Main Samba Configuration File

All configuration is managed through:

```
 /etc/samba/smb.conf

```

### Basic Share Example

This configuration defines a shared directory with guest access:

```
[shared]
path = /srv/samba/shared
browseable = yes
read only = no
guest ok = yes

```

**Notes:**

• `path`: Filesystem path to be shared.

• `browseable`: Whether the share is visible during network browsing.

• `read only`: If set to `no`, allows write access.

• `guest ok`: Enables guest access without login.

## Common Commands

### Install Samba (Debian/Ubuntu)

Install Samba and its dependencies:

```
apt install samba

```

### Check Samba Version

Verify the installed Samba version:

```
smbd --version

```

### Add A Samba User

Add a user to Samba (must exist as a system user first):

```
sudo smbpasswd -a <username>

```

### Restart Samba Services

Apply changes by restarting the SMB and NetBIOS services:

```
sudo systemctl restart smbd nmbd

```

### Test Samba Configuration

Check your `smb.conf` for syntax errors:

```
testparm

```

## Security And Permissions

- Ensure proper Linux file permissions (`chmod`, `chown`) are set on shared folders.
• Use these common parameters for access control in `smb.conf`:

```
valid users = username
read only = yes
write list = user1, user2

```

- Enable password encryption:

```
encrypt passwords = yes

```

**Notes:**

• Always prefer SMBv2 or SMBv3 to avoid the vulnerabilities of SMB1.

• Use firewall rules to restrict access to SMB ports (137-139, 445).

## Mounting SMB Shares (Client-Side)

### Temporary Mount (CLI)

To mount a remote SMB share to `/mnt/shared` using credentials:

```
mount -t cifs //192.168.1.100/shared /mnt/shared -o username=user,password=pass,iocharset=utf8,vers=3.0

```

**Options:**

• `username/password`: Auth credentials.

• `vers=3.0`: Use SMBv3.

• `iocharset=utf8`: UTF-8 encoding for file names.

### Persistent Mount With Fstab

To persist across reboots, add the following to `/etc/fstab`:

```
//192.168.1.100/shared /mnt/shared cifs username=user,password=pass,iocharset=utf8,vers=3.0 0 0

```

**Optional Security Tip:** Use a credentials file instead of embedding credentials in fstab.

**Example `/etc/samba/cred-user`:**

```
username=user
password=pass

```

Mount with:

```
mount -t cifs //192.168.1.100/shared /mnt/shared -o credentials=/etc/samba/cred-user,vers=3.0

```

Ensure that the credentials file is owned by root and has restricted permissions:

```
chown root:root /etc/samba/cred-user

```

```
chmod 600 /etc/samba/cred-user

```

# Smb Client Tools

This guide covers usage and examples for accessing and managing SMB/CIFS shares on Linux systems (especially RHEL-based distributions). Tools covered include `smbclient`, `smbtree`, `smbget`, and `smbtar`.

## Package Management

### Check Installed Samba Packages

Use the following command to list all Samba-related packages currently installed:

```
rpm -qa | grep samba

```

### Install Samba Client

Install the Samba client tools using the package manager:

```
yum install samba-client

```

```
apt install samba-client

```

### Verify Samba Client Installation

Check if the Samba client is installed:

```
rpm -qa | grep samba-client

```

View detailed package information:

```
rpm -qi samba-client

```

Display documentation files:

```
rpm -qd samba-client

```

List all installed files for the package:

```
rpm -ql samba-client

```

## Browsing Shares With Smbtree

`smbtree` scans the network and displays SMB shares in a tree format, similar to Windows' network neighborhood.

### Note On Smbtree Limitations

- This utility doesn't work if NetBIOS name resolution is not configured.
• If you are using SMB2 or SMB3, network browsing uses WSD/LLMNR, which is not yet supported by Samba.
• SMB1 is disabled by default on the latest Windows versions for security reasons.

### Discover Network Shares

Scan and print the SMB workgroup/domain tree:

```
smbtree

```

List share names only (without computer/user names):

```
smbtree -S

```

Browse shares using a specific username:

```
smbtree -U win7

```

Show shares in a specified workgroup:

```
smbtree -D workgroup

```

List all available workgroups on the network (requires NetBIOS):

```
smbtree -S | grep -E '^\\\\\\\\'

```

### Access Shares Directly

List shares using UNC format and a username:

```
smbclient -L //hostname -U username

```

Mount a share using CIFS (NetBIOS not required):

```
mount -t cifs -o username=user,password=pass //192.168.1.x/share /mnt

```

## Accessing Shares With Smbclient

`smbclient` is a command-line utility with an FTP-style interface for accessing SMB shares.

### Launch smbclient without connecting

```
smbclient

```

Display help menu:

```
smbclient --help

```

List shares on a remote host (anonymous):

```
smbclient -L 192.168.1.61

```

List shares using UNC format:

```
smbclient -L //192.168.1.61

```

List shares as a specific user:

```
smbclient -L //192.168.1.61 -U win11

```

### Connect To A Share

Anonymous access to a share:

```
smbclient //192.168.1.61/data

```

Authenticated access:

```
smbclient //192.168.1.61/data -U Armour

```

### Example Smbclient Session

Download a single file:

```
get runasroot.sh

```

Download multiple files:

```
mget templated-roadtrip.zip VBoxDarwinAdditions.pkg

```

Upload a single file:

```
put mysql80.community-release-el7.3.noarch.rpm

```

Upload multiple files:

```
mput html5up-paradigm-shift.zip latest.zip

```

## Backup And Restore With Smbtar

`smbtar` lets you archive or extract entire SMB shares in tar format.

Display help information:

```
smbtar --help

```

Backup a share:

```
smbtar -s 192.168.1.61 -x data -u Administrator -p @rmour123 -t data.tar -v

```

**Options:**

• `-s`: SMB server address

• `-x`: Share name

• `-u/-p`: Username and password

• `-t`: Output archive file

• `-v`: Verbose output

## Mounting SMB Shares With Mount.cifs

Use the cifs kernel module to mount SMB shares as part of the Linux file system.

Mount to `/mnt` using credentials:

```
mount -t cifs -o username=Administrator,password=@rmour123 //192.168.1.61/data /mnt

```

Mount to a specific directory:

```
mount -t cifs -o username=Administrator,password=@rmour123 //192.168.1.61/data /mnt/d1

```

Unmount the share:

```
umount /mnt

```

## Notes

- Install `cifs-utils` for full CIFS support:

```
yum install cifs-utils

```

- Mounts can be made persistent via `/etc/fstab`:

```
vim /etc/fstab

```

```
//192.168.1.61/data /mnt cifs username=Administrator,password=@rmour123 0 0

```

```
mount -a

```

- For better security, use a credentials file:

```
vim /etc/smb-credentials

```

```
username=Administrator
password=@rmour123

```

Mount using the credentials file:

```
mount -t cifs -o credentials=/etc/smb-credentials //192.168.1.61/data /mnt

```

- Consider `autofs` for on-demand mounting.

• Avoid SMB1; prefer SMB2 or SMB3 for performance and security.

• Use `vers` or `sec` parameters if needed:

```
mount -t cifs -o username=Administrator,password=@rmour123,vers=3.0 //192.168.1.61/data /mnt

```
