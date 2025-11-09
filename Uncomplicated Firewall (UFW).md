Uncomplicated Firewall (UFW) is a user-friendly command-line interface program designed to manage firewall rules on Linux systems. It acts as a frontend to the Linux kernel's netfilter firewall, simplifying the complex iptables commands into easily understandable syntax. UFW aims to make firewall management accessible, especially for users unfamiliar with detailed firewall configurations, while still providing enough flexibility for experienced administrators

## Key features of UFW include

- Simplified rule definition for allowing or densing traffic based on ports, protocols, IP addresses, or application profiles.
- Default secure policies that deny all incoming connections and allow all outgoing connections, providing a sensure baseline.
- Support for both iPv4 and IPv6 traffic management:
- Preconfigured application profiles for common services to ease firewall rule creation.
- Easy rule management commands such as enabling/disabling the firewall, wewing rules, deleting specific rules, and resetting all rules
- UFW Integrates with system service management systemd) for automatic startup and status mornitoring.
- Logging capabilities to track firewall activity.
- It is well-suited for host-based firewalls and is the default firewall management tool on many Debian-based Linux distributions such as Ubuntu

## Installing UFW

- Update package lists:
    
    ```bash
    apt update
    
    ```
    
- Install UFW:
    
    ```bash
    apt install ufw
    
    ```
    
- Check UFW version:
    
    ```bash
    ufw version
    
    ```
    

## Checking and Enabling UFW

- Check UFW status:
    
    ```bash
    ufw status
    
    ```
    
- Enable UFW:
    
    ```bash
    ufw enable
    
    ```
    
- Show added rules:
    
    ```bash
    ufw show added
    
    ```
    

## Allowing Common Services

- Allow SSH (port 22):
    
    ```bash
    ufw allow ssh
    
    ```
    
- Allow HTTPS (port 443):
    
    ```bash
    ufw allow https
    
    ```
    
- Allow Telnet (port 23):
    
    ```bash
    ufw allow telnet
    
    ```
    
- Allow SMTP (port 25):
    
    ```bash
    ufw allow smtp
    
    ```
    
- Allow POP3 (port 110):
    
    ```bash
    ufw allow pop3
    
    ```
    
- Allow IMAP (port 143):
    
    ```bash
    ufw allow imap
    
    ```
    

## Allowing Specific Ports

- Allow TCP port 8080:
    
    ```bash
    ufw allow 8080/tcp
    
    ```
    
- Allow TCP port 8088:
    
    ```bash
    ufw allow 8088/tcp
    
    ```
    
- Allow TCP port 2200:
    
    ```bash
    ufw allow 2200/tcp
    
    ```
    
- Allow UDP port 53 (DNS):
    
    ```bash
    ufw allow 53/udp
    
    ```
    
- Allow UDP port 25:
    
    ```bash
    ufw allow 25/udp
    
    ```
    

## Managing UFW Service

- Check UFW service status:
    
    ```bash
    systemctl status ufw.service
    
    ```
    
- Enable UFW service on boot:
    
    ```bash
    systemctl enable ufw.service
    
    ```
    
- Start UFW service:
    
    ```bash
    systemctl start ufw.service
    
    ```
    
- Show numbered rules:
    
    ```bash
    ufw status numbered
    
    ```
    
- Delete a rule by number:
    
    ```bash
    ufw delete 12
    
    ```
    

## Allow Rules for Ranges and IPs

- Allow TCP ports 80 to 90:
    
    ```bash
    ufw allow 80:90/tcp
    
    ```
    
- Allow TCP port 24 from specific IP:
    
    ```bash
    ufw allow from 192.168.1.52 to any port 24 proto tcp
    
    ```
    
- Allow TCP port 30 to specific IP:
    
    ```bash
    ufw allow to 192.168.1.37 port 30 proto tcp
    
    ```
    
- Allow TCP port 31 from one IP to another:
    
    ```bash
    ufw allow from 192.168.1.52 to 192.168.1.44 port 31 proto tcp
    
    ```
    

## Rejecting or Dropping Traffic

- Reject TCP port 110:
    
    ```bash
    ufw reject 110/τερ
    
    ```
    
- Deny TCP port 111:
    
    ```bash
    ufw deny 111/tcp
    
    ```
    
- Deny TCP port 22 to a specific IP:
    
    ```bash
    ufw deny to 192.168.1.34 port 22 proto tep
    
    ```
    
- Reject TCP port 22 to a specific IP:
    
    ```bash
    ufw reject to 192.168.1.33 port 22 proto top
    
    ```
    
- Deny TCP port 22 from a specific IP:
    
    ```bash
    ufw deny from 192.168.1.6 to any port 22 proto tcp
    
    ```
    
- Deny all traffic from a specific IP:
    
    ```bash
    ufw deny from 192.168.1.52
    
    ```
    
- Deny TCP port 22 from a specific IP:
    
    ```bash
    ufw deny from 192.168.1.52 to any port 22 proto τερ
    
    ```
    
- Deny TCP port 80 from a subnet:
    
    ```bash
    ufw deny from 20.20.20.0/24 to any port 80 proto τερ
    
    ```
