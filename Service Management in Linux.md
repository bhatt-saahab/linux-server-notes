Service management in Linux refers to controlling background processes (services) that handle system functions such as networking, logging, and web hosting. Services can be managed using different init systems depending on the Linux distribution: SysVinit (legacy), chkconfig (runlevels), and systemd (modern standard).

Managing services in Linux is a fundamental part of system administration. Services are background processes that provide core functionality such as networking, web hosting, logging, and more.

## Key Points of a Linux Service

- **Background Process**: Runs in the background without user interaction.
- **System Management**: Provides core system functions (e.g., networking, logging).
- **Controlled by Init Systems**: Managed by systemd, upstart, or SysVinit.
- **Configuration & Logs**: Config files usually in `/etc`, logs in `/var/log`.

## Managing Services with SysVinit (/etc/init.d/)

Legacy method used in older Debian-based or SysVinit-enabled systems. Used in older Linux systems (Debian/Ubuntu before systemd, RHEL ≤6).

- List available service scripts:
    
    ```bash
    cd /etc/init.d/
    
    ```
    
    ```bash
    ls -lh /etc/init.d/
    
    ```
    
- View specific service script:
    
    ```bash
    cat /etc/init.d/apache2
    
    ```
    
- Check status:
    
    ```bash
    /etc/init.d/apache2 status
    
    ```
    
- Start service:
    
    ```bash
    /etc/init.d/apache2 start
    
    ```
    
- Stop service:
    
    ```bash
    /etc/init.d/apache2 stop
    
    ```
    
- Restart service:
    
    ```bash
    /etc/init.d/apache2 restart
    
    ```
    
- Reload configuration:
    
    ```bash
    /etc/init.d/apache2 force-reload
    
    ```
    

## Managing Services with service Command (Legacy)

Wrapper command for SysVinit scripts:

- Check status:
    
    ```bash
    service apache2 status
    
    ```
    
- Start service:
    
    ```bash
    service apache2 start
    
    ```
    
- Stop service:
    
    ```bash
    service apache2 stop
    
    ```
    
- Restart service:
    
    ```bash
    service apache2 restart
    
    ```
    
- Reload configuration:
    
    ```bash
    service apache2 reload
    
    ```
    

## Managing Services with systemctl (systemd)

Modern distributions like RHEL 7+, CentOS 7+, Ubuntu 16.04+, and Debian Jessie+ use systemd.

- Checking service status:
    
    ```bash
    systemctl status httpd.service
    
    ```
    
- Starting and stopping:
    
    ```bash
    systemctl start httpd.service
    
    ```
    
    ```bash
    systemctl stop httpd.service
    
    ```
    
- Restart and reload:
    
    ```bash
    systemctl restart httpd.service
    
    ```
    
    ```bash
    systemctl reload httpd.service
    
    ```
    
- Manage startup behavior:
    
    ```bash
    systemctl enable httpd.service
    
    ```
    
    ```bash
    systemctl disable httpd.service
    
    ```
    
    ```bash
    systemctl is-enabled httpd.service
    
    ```
    
- All active services:
    
    ```bash
    systemctl list-units --type=service
    
    ```
    
- Running services:
    
    ```bash
    systemctl list-units --type=service --state=running
    
    ```
    
- Failed services:
    
    ```bash
    systemctl list-units --type=service --state=failed
    
    ```
    
- Prevents running, even manually:
    
    ```bash
    systemctl mask bluetooth
    
    ```
    
- Re-enable service:
    
    ```bash
    systemctl unmask bluetooth
    
    ```
    
- Service logs:
    
    ```bash
    journalctl -u httpd.service
    
    ```
    
- Last 1 hour logs:
    
    ```bash
    journalctl -u httpd.service since "1 hour ago"
    
    ```
    
- Live logs:
    
    ```bash
    journalctl -u httpd.service -f
    
    ```
    

## chkconfig (SysVinit Runlevel Management)

Used to enable/disable services at specific runlevels in SysVinit-based systems.

- Show all services:
    
    ```bash
    chkconfig --list
    
    ```
    
- Enable/disable service:
    
    ```bash
    chkconfig httpd on
    
    ```
    
- Disable network service:
    
    ```bash
    chkconfig httpd off
    
    ```
    
- Runlevel example (3 = multi-user, text mode):
    
    ```bash
    chkconfig --list | grep 3:on
    
    ```
    
    ```bash
    chkconfig --level 35 httpd on
    
    ```
    
    ```bash
    chkconfig --level 0126 network off
    
    ```
    

## Linux Runlevels (SysVinit)

- **0**: Shutdown
- **1**: Single user mode (maintenance)
- **2**: Multi-user (no networking on some distros)
- **3**: Multi-user (text mode, networking)
- **4**: Reserved (custom use)
- **5**: Multi-user with GUI
- **6**: Reboot

## Comparison: SysVinit vs systemd

| Action | SysVinit (service) | Systemd (systemctl) |
| --- | --- | --- |
| Start service | `service apache2 start` | `systemctl start apache2` |
| Stop service | `service apache2 stop` | `systemctl stop apache2` |
| Restart service | `service apache2 restart` | `systemctl restart apache2` |
| Reload config | `service apache2 reload` | `systemctl reload apache2` |
| Check status | `service apache2 status` | `systemctl status apache2` |
| Enable service | Not applicable | `systemctl enable apache2` |
| Disable service | Not applicable | `systemctl disable apache2` |
