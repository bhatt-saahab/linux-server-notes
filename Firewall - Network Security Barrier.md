A firewall is a security device or software that monitors and controls incoming and outgoing network traffic. It acts as a protective barrier between trusted internal networks and untrusted external networks (like the internet).

The primary function of a firewall is to allow or deny network traffic based on a set of rules.

## Key Functions of a Firewall

- **Traffic Filtering**: Examines packets and applies security rules based on IP, ports, and protocols.
Example: A rule may allow HTTP traffic (port 80) but block FTP traffic (port 21).
- **Access Control**: Controls who can access specific parts of a network.
Example: Restrict SSH access (port 22) to only internal IPs.
- **Intrusion Prevention**: Detects suspicious activity (e.g., port scanning, large data transfers). Can block threats automatically before they exploit vulnerabilities.
- **Monitoring & Logging**: Keeps logs of traffic activity for auditing and forensic analysis. Helps in incident response and identifying security threats.

## Types of Firewalls

Firewalls can be categorized based on how they inspect traffic and their network placement.

1. **Packet-Filtering Firewalls (Stateless)**
    
    Evaluate each packet individually without context of previous packets.
    
    Operate at the network layer (Layer 3).
    
    Apply rules based on source/destination IP, port, and protocol.
    
    Fast processing, lightweight, and simple to configure.
    
    Less secure; cannot detect complex attacks like replay attacks or fragmentation.
    
    Example Rule: Allow TCP traffic from 192.168.1.100 to 10.0.0.5 on port 80.
    
2. **Stateful Firewalls**
    
    Track the state of active connections.
    
    Operate at the network and transport layers (Layer 3 & 4).
    
    Allow packets only if part of an established or related session.
    
    More secure, can prevent SYN floods and spoofing.
    
    Use more resources and slower than stateless firewalls.
    
    Example: Allow new TCP connections for SSH and track established sessions.
    
3. **Next-Generation Firewalls (NGFW)**
    
    Advanced firewalls with deep packet inspection (Layer 7).
    
    Include Intrusion Prevention System (IPS), malware filtering, VPN capabilities.
    
    Can detect and block complex threats like SQL Injection and Cross-site scripting (XSS).
    

## Comparison: Stateless vs. Stateful Firewalls

| Feature | Stateless Firewall | Stateful Firewall |
| --- | --- | --- |
| Tracking | Does not track connections | Tracks connection states |
| Security | Less secure, vulnerable to spoofing | More secure, resistant to attacks |
| Performance | Faster (no session tracking) | Slower (tracks sessions) |
| Use Case | Simple filtering, high-speed needs | Enterprise security, deeper inspection |
| Vulnerabilities | Susceptible to packet spoofing | Resistant to spoofing & replay attacks |

## Firewalls in Linux

1. **iptables (Legacy - Both Stateless and Stateful)**
    
    Stateless if only filtering by IP/port/protocol.
    
    Stateful if using modules `-m state` or `-m conntrack`.
    
    Example stateless rule:
    
    ```bash
    iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    
    ```
    
    ```bash
    iptables -P INPUT DROP
    
    ```
    
    Example stateful rule:
    
    ```bash
    iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
    
    ```
    
    ```bash
    iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT
    
    ```
    
    ```bash
    iptables -P INPUT DROP
    
    ```
    
2. **firewalld (Stateful by Default)**
    
    Uses nftables or iptables backend.
    
    Tracks connections and supports dynamic rule management.
    
    Example:
    
    ```bash
    firewall-cmd --permanent --add-service=ssh
    
    ```
    
    ```bash
    firewall-cmd --reload
    
    ```
    
3. **UFW (Uncomplicated Firewall - Stateful by Default)**
    
    Wrapper around iptables.
    
    Automatically tracks connection states.
    
    Example:
    
    ```bash
    ufw allow 22/tcp
    
    ```
    
    ```bash
    ufw enable
    
    ```
    
    Example to block all except SSH:
    
    ```bash
    ufw default deny incoming
    
    ```
    
    ```bash
    ufw default allow outgoing
    
    ```
    
    ```bash
    ufw allow ssh
    
    ```
    
    ```bash
    ufw enable
    
    ```
    

## Summary

- Stateless = fast but less secure.
- Stateful = secure but slower.
- NGFW = advanced protection with deep inspection.
- Linux tools: iptables, firewalld, UFW.

# Network Operating Systems and Security Solutions

This summary highlights key Router OS and Firewall OS options along with basic definitions of IDS and IPS. It Network Operating Systems and Security Solutions.

## Router Operating Systems (Router OS)

- **DD-WRT**: A popular open-source firmware for wireless routers, offering enhanced features, better performance, and advanced network management compared to stock firmware.
- **OpenWrt**: A highly customizable open-source Linux-based OS for embedded devices, widely used on routers to provide robust networking, security, and package management.
- **VyOs**: An open-source network operating system based on Linux, designed for advanced routing, firewalling, VPN, and network virtualization on standard x86 hardware.
- **IPCop**: A Linux-based dedicated firewall and router distribution designed for easy setup and management in small office/home office environments.

## Firewall Operating System (Firewall OS)

- **pfSense**: An open-source firewall and router platform based on FreeBSD, offering powerful firewalling, VPN, routing, DHCP, and IDS/IPS capabilities for enterprise and home use.

## Intrusion Detection and Prevention Systems

- **Intrusion Detection System (IDS)**: A security system that monitors network or system activities for malicious behavior or policy violations and alerts administrators.
- **Intrusion Prevention System (IPS)**: An extension of IDS that not only detects malicious activity but also takes proactive actions such as blocking or rejecting harmful traffic automatically.

# IPTables Configuration and Management

## Checking and Managing Firewall Services

- Check if Firewalld is Installed:
    
    ```bash
    rpm -qa | grep firewalld
    
    ```
    
- Get Firewalld Package Information:
    
    ```bash
    rpm -qi firewalld-0.6.3.13.el7_9.noarch
    
    ```
    
- Check Status of IPTables and Firewalld:
    
    ```bash
    systemctl status iptables.service
    
    ```
    
    ```bash
    systemctl status firewalld.service
    
    ```
    
- Disable and Stop Firewalld:
    
    ```bash
    systemctl disable firewalld.service
    
    ```
    
    ```bash
    systemctl mask firewalld.service
    
    ```
    
    ```bash
    systemctl stop firewalld.service
    
    ```
    
- Remove Firewalld:
    
    ```bash
    rpm -qf /usr/lib/systemd/system/firewalld.service
    
    ```
    
    ```bash
    yum remove firewalld
    
    ```
    

## Installing Required Packages

- Install Netcat:
    
    ```bash
    yum install netcat
    
    ```
    
- Verify Netcat Installation:
    
    ```bash
    rpm -qa | grep netcat
    
    ```
    
- Check IPTables Installation:
    
    ```bash
    rpm -qa | grep iptables
    
    ```
    
- Search for IPTables Packages:
    
    ```bash
    yum search iptables
    
    ```
    
- Install IPTables (Legacy + Utilities + Services):
    
    ```bash
    yum install iptables-legacy.x86_64 iptables-utils.x86_64 iptables-services.noarch
    
    ```
    
- Verify IPTables Installation:
    
    ```bash
    rpm -qa | grep iptables
    
    ```
    
- Get Package Info:
    
    ```bash
    rpm -qi iptables-legacy-1.8.10.5.1.el9.next.x86_64
    
    ```
    
    ```bash
    rpm -ql iptables-legacy-1.8.10.5.1.el9.next.x86_64
    
    ```
    
    ```bash
    rpm -qc iptables-legacy-1.8.10.5.1.el9.next.x86_64
    
    ```
    

## Managing IPTables Service

- Enable and Start:
    
    ```bash
    systemctl enable iptables.service
    
    ```
    
    ```bash
    systemctl enable ip6tables.service
    
    ```
    
    ```bash
    systemctl start iptables.service
    
    ```
    
    ```bash
    systemctl start ip6tables.service
    
    ```
    
- Restart:
    
    ```bash
    systemctl restart iptables.service
    
    ```
    
    ```bash
    systemctl restart ip6tables.service
    
    ```
    

## Viewing IPTables Rules

- List Rules:
    
    ```bash
    iptables -L
    
    ```
    
    ```bash
    iptables -L -n
    
    ```
    
    ```bash
    iptables -n -L INPUT
    
    ```
    
    ```bash
    iptables -L OUTPUT
    
    ```
    
    ```bash
    iptables -L FORWARD
    
    ```
    
- List with Line Numbers:
    
    ```bash
    iptables -n --line-numbers -L
    
    ```
    
    ```bash
    iptables -n --line-numbers -L INPUT
    
    ```
    
    ```bash
    iptables -n --line-numbers -L OUTPUT
    
    ```
    
    ```bash
    iptables -n --line-numbers -L FORWARD
    
    ```
    

## Configuring IPTables Rules

- Allow Specific Ports:
    
    ```bash
    iptables -I INPUT 5 -p tcp --dport 21 -j ACCEPT
    
    ```
    
- Insert Multiple Port Rules:
    
    ```bash
    iptables -I INPUT 5 -p tcp --dport 80:90 -j ACCEPT
    
    ```
    
    ```bash
    iptables -I INPUT 5 -p tcp --dport telnet -j ACCEPT
    
    ```
    
    ```bash
    iptables -I INPUT 5 -p udp --dport 53 -j ACCEPT
    
    ```
    
    ```bash
    iptables -I INPUT 5 -p udp --dport 67 -j ACCEPT
    
    ```
    
    ```bash
    iptables -I INPUT 5 -p udp --dport 110:130 -j ACCEPT
    
    ```
    
- Delete a Rule:
    
    ```bash
    iptables -D INPUT 6
    
    ```
    
- Block Specific Traffic:
    
    ```bash
    iptables -I INPUT 4 -p tcp -s 192.168.1.6 --dport 22 -j DROP
    
    ```
    
    ```bash
    iptables -I INPUT 4 -p tcp -s 192.168.1.45 --dport 22 -j REJECT
    
    ```
    
    ```bash
    iptables -I INPUT 5 -p tcp -d 192.168.1.37 --dport 30 -j ACCEPT
    
    ```
    

## Managing Output Rules

- Drop ICMP to 8.8.8.8:
    
    ```bash
    iptables -I OUTPUT 1 -d 8.8.8.8 -p icmp -j DROP
    
    ```
    
- Block HTTP(S) to Specific Domains:
    
    ```bash
    iptables -I OUTPUT 1 -d google.com -p tcp --dport 80 -j DROP
    
    ```
    
    ```bash
    iptables -I OUTPUT 1 -d www.facebook.com -p tcp --dport 443 -j DROP
    
    ```
    
    (A Note: DNS resolution might fail if not using IP directly. Consider using IP addresses instead of domain names for reliability.)
    

## Saving and Restoring Rules

- Backup Current Rules:
    
    ```bash
    cp /etc/sysconfig/iptables /etc/sysconfig/iptables.back
    
    ```
    
- Save & Restart:
    
    ```bash
    service iptables save
    
    ```
    
    ```bash
    service iptables stop
    
    ```
    
    ```bash
    service iptables start
    
    ```
    
    ```bash
    service iptables restart
    
    ```
    
- Restore After Reboot:
    
    ```bash
    iptables-restore < /etc/sysconfig/iptables.back
    
    ```
    
- Save Current Rules:
    
    ```bash
    iptables-save > /etc/sysconfig/iptables
    
    ```
    

## Ensure IPTables Starts on Boot

```bash
yum install iptables-services

```

```bash
systemctl mask firewalld

```

```bash
systemctl enable iptables

```

```bash
systemctl enable ip6tables

```

```bash
systemctl stop firewalld

```

```bash
systemctl start iptables

```

```bash
systemctl start ip6tables

```

## Additional Resources

Advanced Policy Firewall
