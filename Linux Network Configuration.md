## Overview

Basic network configuration involves managing IP addresses, DNS, routing rules, and interface settings using both commands and configuration files.

## Core Network Commands

These commands are used for viewing and managing basic network settings.

- **Open a text-based interface for managing network settings**:
    
    ```
    nmtui
    
    ```
    
- **Display or set the system hostname**:
    
    ```
    hostname
    
    ```
    
- **Show IP address(es) linked to the hostname**:
    
    ```
    hostname -i
    
    ```
    
    or
    
    ```
    hostname -I
    
    ```
    
- **Display all network interfaces and assigned IPs**:
    
    ```
    ip addr show
    
    ```
    
    or shorter:
    
    ```
    ip ad
    
    ```
    
- **Legacy tool for network interface details (deprecated)**:
    
    ```
    ifconfig
    
    ```
    
    or for all interfaces:
    
    ```
    ifconfig -a
    
    ```
    
- **Show DNS resolver configuration**:
    
    ```
    cat /etc/resolv.conf
    
    ```
    
- **List current routing table and gateways**:
    
    ```
    route -n
    
    ```
    
    or
    
    ```
    ip route
    
    ```
    
- **Retrieve the system's public IPv4 address**:
    
    ```
    curl -4 ifconfig.me
    
    ```
    
    or
    
    ```
    curl -4 icanhazip.com
    
    ```
    

## Static IP Configuration

### 1. Using NetworkManager (.nmconnection files)

Edit the connection file:

```
vim /etc/NetworkManager/system-connections/enp0s3.nmconnection

```

Example configuration (first variant):

```
[connection]
id=enp0s3
uuid=858afbb5-a64a-3fd0-bc9b-3f9a533b6e07
type=ethernet
autoconnect-priority=-999
interface-name=enp0s3
timestamp=1758026556

[ethernet]

[ipv4]
address1=192.168.1.33/24
dns=8.8.8.8;8.8.4.4;
gateway=192.168.1.1
ignore-auto-dns=true
method=manual

[ipv6]
addr-gen-mode=eui64
method=disabled

[proxy]

```

Example configuration (second variant):

```
[connection]
id=enp0s3
uuid=858afbb5-a64a-3fd0-bc9b-3f9a533b6e07
type=ethernet
autoconnect-priority=-999
interface-name=enp0s3
timestamp=1758027545

[ethernet]

[ipv4]
addresses=192.168.1.32/24;192.168.1.1;
dns=8.8.8.8;8.8.4.4;
ignore-auto-dns=true
method=manual

[ipv6]
addr-gen-mode=eui64
method=disabled

[proxy]

```

### 2. Using ifcfg Files (CentOS 7 / RHEL 7)

Edit the interface file:

```
vim /etc/sysconfig/network-scripts/ifcfg-enp0s3

```

Example static configuration:

```
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=92a9be99-ddba-4e75-a601-a385f8dcb88f
DEVICE=enp0s3
ONBOOT=yes
IPADDR=192.168.1.36
PREFIX=24
GATEWAY=192.168.1.1
DNS1=8.8.8.8
PROXY_METHOD=none
BROWSER_ONLY=no

```

### 3. Using /etc/network/interfaces (Debian/Ubuntu)

Edit the interfaces file:

```
vim /etc/network/interfaces

```

Example configuration (includes commented sections and loopback):

```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug enp0s3
#iface enp0s3 inet dhcp

```

## Dynamic IP (DHCP) Configuration

### 1. Using NetworkManager (.nmconnection files)

Edit the connection file:

```
vim /etc/NetworkManager/system-connections/enp0s3.nmconnection

```

Example DHCP configuration:

```
[connection]
id=enp0s3
uuid=858afbb5-a64a-3fd0-bc9b-3f9a533b6e07
type=ethernet
autoconnect-priority=-999
interface-name=enp0s3
timestamp=1758027545

[ethernet]

[ipv4]
ignore-auto-dns=true
method=auto

[ipv6]
addr-gen-mode=eui64
method=disabled

[proxy]

```

### 2. Using ifcfg Files (CentOS 7 / RHEL 7)

Edit the interface file:

```
vim /etc/sysconfig/network-scripts/ifcfg-enp0s3

```

Example DHCP configuration:

```
TYPE=Ethernet
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=92a9be99-ddba-4e75-a601-a385f8dcb88f
DEVICE=enp0s3
ONBOOT=yes
PROXY_METHOD=none
BROWSER_ONLY=no

```

### 3. Using /etc/network/interfaces (Debian/Ubuntu)

Edit the interfaces file (as part of the static example above, but with DHCP):

```
# The primary network interface
allow-hotplug enp0s3
iface enp0s3 inet dhcp
iface enp0s3 inet6 auto

# This is an autoconfigured IPv6 interface
#iface enp0s3 inet6 auto

```

## Assigning Multiple IP Addresses

### Network Manager (.nmconnection file)

Add multiple addresses under [ipv4] section. Edit the connection file:

```
vim /etc/NetworkManager/system-connections/enp0s3.nmconnection

```

Example (partial, focusing on [ipv4]):

```
[connection]
id=enp0s3
uuid=858afbb5-a64a-3fd0-bc9b-3f9a533b6e07
type=ethernet

[ipv4]
# Add multiple addresses here, e.g., addresses=192.168.1.35/24;192.168.1.36/24;
# Followed by dns=, gateway=, etc.
method=manual

[proxy]
PROXY_METHOD=none
BROWSER_ONLY=no

```

(Note: The data includes a truncated "enp@s3.nmconnection" which appears to be a typo for "enp0s3.nmconnection".)

### Using ifcfg Files (CentOS 7 / RHEL 7)

Create an additional alias file for the second IP:

```
vim /etc/sysconfig/network-scripts/ifcfg-enp0s3:2

```

Example configuration for the alias interface:

```
TYPE=Ethernet
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=92a9be99-ddba-4e75-a601-a385f8dcb88f
DEVICE=enp0s3:2
ONBOOT=yes
IPADDR=192.168.1.39
PREFIX=24
GATEWAY=192.168.1.1
DNS1=8.8.8.8
PROXY_METHOD=none
BROWSER_ONLY=no

```

### Debian/Ubuntu (/etc/network/interfaces)

Add multiple addresses under the same interface using `up` commands. Edit the interfaces file:

```
vim /etc/network/interfaces

```

Example configuration (static with multiple IPs):

```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug enp0s3
#iface enp0s3 inet dhcp
iface enp0s3 inet static
address 192.168.1.35
netmask 255.255.255.0
network 192.168.1.0
broadcast 192.168.1.255
gateway 192.168.1.1
dns-nameservers 8.8.8.8
up ip addr add 192.168.1.36/24 dev enp0s3
up ip addr add 192.168.1.37/24 dev enp0s3

iface enp0s3 inet6 auto

# This is an autoconfigured IPv6 interface
#iface enp0s3 inet6 auto

```

# Linux Network Configuration: ifconfig and ip

The `ifconfig` command is a legacy tool used to configure network interfaces. It is deprecated in most Linux distributions and replaced by the `ip` command from the iproute2 suite.

## Using ifconfig (Legacy Systems)

### Display Interfaces

- Shows all active network interfaces:

```
ifconfig

```

- Shows all interfaces, including inactive ones:

```
ifconfig -a

```

### View a Specific Interface

- View details for a specific interface (e.g., enp0s3):

```
ifconfig enp0s3

```

- View details for the loopback interface:

```
ifconfig lo

```

### Assign IP Addresses (CentOS 7 / RHEL 7)

- Assign a basic IP address:

```
ifconfig enp0s3 192.168.1.40

```

- Assign an IP address with netmask:

```
ifconfig enp0s3 192.168.1.41 netmask 255.255.255.0

```

- Assign an IP address with CIDR notation:

```
ifconfig enp0s3 192.168.1.42/20

```

### Disable Interfaces

- Disable a specific interface:

```
ifconfig enp0s3 down

```

- Alternative using legacy scripts:

```
ifdown enp0s3

```

### Enable Interfaces

- Enable a specific interface:

```
ifconfig enp0s3 up

```

- Alternative using legacy scripts:

```
ifup enp0s3

```

### Multiple IPs on One Interface (CentOS 7 / RHEL 7)

- Add a secondary IP (alias interface enp0s3:1):

```
ifconfig enp0s3:1 192.168.1.42

```

- Add another secondary IP (alias interface enp0s3:2):

```
ifconfig enp0s3:2 192.168.1.43

```

## Use ip Command (Recommended in CentOS 9)

### Display Interfaces

- Show all IP addresses and interfaces:

```
ip addr show

```

### Assign IP Address (Temporary)

- Add an IP address with CIDR notation:

```
ip addr add 192.168.1.40/24 dev enp0s3

```

- Another example with subnet mask (equivalent to above):

```
ip addr add 192.168.1.41/24 dev enp0s3

```

- Add an IP with a different CIDR:

```
ip addr add 192.168.1.42/20 dev enp0s3

```

### Check Assigned IPs

- Check IPs on a specific device:

```
ip addr show dev enp0s3

```

- Alternative shorthand:

```
ip addr show enp0s3

```

### Remove IP

- Remove a specific IP:

```
ip addr del 192.168.1.40/24 dev enp0s3

```

- Remove another specific IP:

```
ip addr del 192.168.1.41/24 dev enp0s3

```

### Persistent Network Configuration (CentOS 9)

In CentOS 9, persistent network config is managed by NetworkManager. Edit the connection with `nmcli` or by modifying the config file under `/etc/NetworkManager/system-connections/`.

- Example config file path:

```
/etc/NetworkManager/system-connections/enp0s3.nmconnection

```

### Add IP Address (Persistent with nmcli)

- Add an IP address:

```
nmcli con mod enp0s3 +ipv4.addresses 192.168.1.40/24

```

- Add another IP address:

```
nmcli con mod enp0s3 +ipv4.addresses 192.168.1.41/24

```

- Add an IP with different CIDR:

```
nmcli con mod enp0s3 +ipv4.addresses 192.168.1.42/20

```

- Set IPv4 method to manual (static):

```
nmcli con mod enp0s3 ipv4.method manual

```

- Set IPv4 gateway:

```
nmcli con mod enp0s3 ipv4.gateway 192.168.1.1

```

- Set IPv4 DNS servers:

```
nmcli con mod enp0s3 ipv4.dns "8.8.8.8 8.8.4.4"

```

- Activate the connection:

```
nmcli con up enp0s3

```

### Disable Interface

- Disable a specific interface:

```
ip link set enp0s3 down

```

### Enable Interface

- Enable a specific interface:

```
ip link set enp0s3 up

```

## Routing with ip

### Show Routing Table

- Display the routing table:

```
ip route

```

### Add a Route

- Add a route to a subnet via gateway:

```
ip route add 192.168.2.0/24 via 192.168.1.1 dev enp0s3

```

### Delete a Route

- Delete a specific route:

```
ip route del 192.168.2.0/24

```

### Set Default Gateway

- Add default route via gateway:

```
ip route add default via 192.168.1.1 dev enp0s3

```

### Show Routes for a Specific Interface

- Show routes associated with a device:

```
ip route show dev enp0s3

```

### Flush All Routes

- Flush the main routing table:

```
ip route flush table main

```

# Network Diagnostics Commands: ping, traceroute, and tracepath

Network diagnostic tools like `ping`, `traceroute`, and `tracepath` are essential for testing connectivity, analyzing packet routes, and troubleshooting network issues.

## ping Command

The `ping` command sends ICMP Echo Request packets to verify connectivity to a host.

### Continuous ping

- Continuous ping to a host:

```
ping 8.8.8.8

```

### Send Specific Number of Packets

- Send 2 packets:

```
ping 8.8.8.8 -c 2

```

### Send a Large Packet

- Send a large packet (65,500 bytes, default is 56 bytes):

```
ping 192.168.1.1 -s 65500

```

### Ping from a Specific Interface

- Ping from a specific interface with large packet and count:

```
ping -I enp0s3 192.168.1.1 -s 65500 -c 4

```

### Add a Custom Payload

- Add a custom payload (hexadecimal 42) with count and size:

```
ping 192.168.1.1 -c 2 -s 65500 -p 42
ping 10.182.75.4 -c 2 -s 65500 -p 42
```

### Set Intervals

- Send 2 packets every 10 seconds:

```
ping 192.168.1.1 -i 10 -c 2

```

- Alternative example (to different IP, with interval and count):

```
ping 192.168.1.11 -i 10 -c 2

```

### Resolve and Ping a Hostname

- Ping a hostname (resolves DNS):

```
ping armourinfosec.com

```

### IPv6 Ping

- IPv6 ping to a hostname:

```
ping -6 armourinfosec.com

```

- IPv6 ping to an IP with interval, count, and payload:

```
ping -6 2404:6800:4009:80e::200e -i 10 -c 2 -p 42

```

### Send Flood Pings

- Flood pings (as fast as possible, max packet size):

```
ping 192.168.1.1 -f -s 65500

```

## traceroute Command

`traceroute` maps the path packets take to reach a destination.

### Installation (RHEL/CentOS)

- Install on RHEL/CentOS:

```
yum install traceroute

```

### Installation (Debian/Ubuntu)

- Install on Debian/Ubuntu:

```
apt install traceroute

```

### Trace Route to IP

- Trace route to Google DNS:

```
traceroute 8.8.8.8

```

### Trace Route to Hostname

- Trace route to a hostname:

```
traceroute armourinfosec.io

```

### TCP Traceroute

- Useful when ICMP is blocked (requires tcptraceroute package):

```
tcptraceroute 8.8.8.8

```

## tracepath Command

Similar to `traceroute` but does not require root privileges.

### Installation (RHEL/CentOS)

- Install on RHEL/CentOS:

```
yum install iputils iputils-tracepath

```

### Installation (Debian/Ubuntu)

- Install on Debian/Ubuntu:

```
apt install iputils-tracepath

```

### Trace Path to IP

- Trace path to Google DNS:

```
tracepath 8.8.8.8

```
