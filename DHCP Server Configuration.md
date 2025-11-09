This document provides a step-by-step guide to install, configure, and manage a DHCP server on a Linux-based system. The commands are documented with explanations for better clarity.

## Initial Setup and Installation

### List All Available Repositories

To ensure that the DHCP server package is available:

```
yum repolist all

```

### Check if the DHCP Package is Installed

```
rpm -qa | grep dhcp

```

### Install the DHCP Server Package

```
yum install dhcp-server.x86_64

```

### Check if the DHCP Package and Server are Installed

```
rpm -qa | grep dhcp

```

```
rpm -qa | grep dhcp-server

```

### Display Detailed Information About the Installed DHCP Server Package

```
rpm -qi dhcp-server

```

### List the Documentation Files Included with the DHCP Server

```
rpm -qd dhcp-server

```

### List the Configuration Files Included with the DHCP Server

```
rpm -qc dhcp-server

```

## Edit DHCP Configuration File

Open the DHCP configuration file for editing:

```
vim /etc/dhcp/dhcpd.conf

```

### Sample DHCP Configuration

Below is an example of a basic `dhcpd.conf` configuration file:

```
authoritative;

# Specify network address and subnet mask
subnet 192.168.1.0 netmask 255.255.255.0 {
  # Specify the range of lease IP address
  range 192.168.1.50 192.168.1.250;

  # Specify default gateway
  option routers 192.168.1.1;

  # DNS servers for name resolution
  option domain-name-servers 8.8.8.8, 8.8.4.4;

  # Specify broadcast address
  option broadcast-address 192.168.1.255;

  # Default lease time
  default-lease-time 600;

  # Max lease time
  max-lease-time 7200;
}

```

### Reference Example Configuration

Open the example configuration file for reference:

```
vim /usr/share/doc/dhcp-server/dhcpd.conf.example

```

## Start and Check DHCP Service

### Check Service Status

```
systemctl status dhcpd.service

```

### Start Service

```
systemctl start dhcpd.service

```

### Restart Service

```
systemctl restart dhcpd.service

```

### Enable Service to Start at Boot

```
systemctl enable dhcpd.service

```

## Check Open Ports

### Check Which Ports are Open and Listening

```
netstat -nltup

```

### Check if DHCP Ports are Open

```
netstat -nltup | grep dhcp

```

## Update Firewall Rules (Firewalld)

### Open DHCP Port in Firewall

Allow DHCP traffic through firewalld:

```
firewall-cmd --add-port=67/udp --permanent

```

### Reload the Firewall to Apply Changes

```
firewall-cmd --reload

```

### Check if the DHCP Port is Open

```
firewall-cmd --list-ports

```

### Alternatively, Check the Detailed Firewall Configuration

```
firewall-cmd --list-all

```

## Check DHCP Leases

Display current DHCP leases:

```
cat /var/lib/dhcpd/dhcpd.leases

```

## Configure DHCP Exclusion Range

You can define an exclusion range in the DHCP configuration file to prevent certain IP addresses within the subnet from being assigned to clients.

### Step 1: Edit DHCP Configuration File

```
vim /etc/dhcp/dhcpd.conf

```

### Example Configuration for Exclusion Range

You can define multiple range statements to exclude specific IP addresses within the subnet.

### Example 1: Exclude 192.168.1.51 to 192.168.1.70

This example configures an exclusion between 192.168.1.51 and 192.168.1.70:

```
authoritative;

# Specify network address and subnet mask
subnet 192.168.1.0 netmask 255.255.255.0 {
  # Specify the range of lease IP address
  range 192.168.1.50 192.168.1.50;
  range 192.168.1.71 192.168.1.150;

  # Specify default gateway
  option routers 192.168.1.1;

  # DNS servers for name resolution
  option domain-name-servers 8.8.8.8, 8.8.4.4;

  # Specify broadcast address
  option broadcast-address 192.168.1.255;

  # Default lease time
  default-lease-time 600;

  # Max lease time
  max-lease-time 7200;
}

```

## Reserve IP Address

You can reserve specific IP addresses for particular clients based on their MAC addresses in the DHCP configuration file. This ensures that the client always gets the same IP address when connecting to the network.

### Edit DHCP Configuration File

```
vim /etc/dhcp/dhcpd.conf

```

### Example Configuration for IP Reservation

Add a host block within the subnet configuration to reserve an IP address for a specific MAC address.

### Example 1: Reserve IP Address for a Single Client

This example reserves IP 192.168.1.201 for the client with MAC address 08:00:27:5d:b8:bc:

```
authoritative;

# Specify network address and subnet mask
subnet 192.168.1.0 netmask 255.255.255.0 {
  # Specify the range of lease IP address
  range 192.168.1.50 192.168.1.50;
  range 192.168.1.61 192.168.1.210;
  range 192.168.1.231 192.168.1.250;

  # Specify default gateway
  option routers 192.168.1.1;

  # DNS servers for name resolution
  option domain-name-servers 8.8.8.8, 8.8.4.4;

  # Specify broadcast address
  option broadcast-address 192.168.1.255;

  # Default lease time
  default-lease-time 600;

  # Max lease time
  max-lease-time 7200;
}

# Reserve IP address for specific client
host win11 {
  # MAC address of client for fixed IP
  hardware ethernet 08:00:27:cc:91:8f;
  # Reserved IP address
  fixed-address 192.168.1.201;
}

```

### Example 2: Reserve Multiple IP Addresses

This example reserves two IP addresses for different clients:

```
# Reserve IP for win11
host win11-1 { hardware ethernet 08:00:27:cc:91:8f; fixed-address 192.168.1.201; }
host win11-2 { hardware ethernet 08:00:27:06:86:94; fixed-address 192.168.1.150; }

```

### Restart DHCP Service

After modifying the configuration file, restart the DHCP service:

```
systemctl restart dhcpd.service

```

## Whitelist/Allow List Clients

You can configure a DHCP server to only provide IP addresses to specific known clients by using the `deny unknown-clients` directive. This restricts DHCP access to only listed MAC addresses, effectively creating a whitelist.

### Edit DHCP Configuration File

```
vim /etc/dhcp/dhcpd.conf

```

### Example Configuration for Whitelisting Clients

You can whitelist specific clients by adding their MAC addresses in host blocks and setting `deny unknown-clients` within the subnet block.

### Example: Allow Only Specific Clients

This example allows only the clients with MAC addresses 08:00:27:5d:b8:bc and 08:00:27:fd:a8:e3 to receive an IP address:

```
authoritative;

# Specify network address and subnet mask
subnet 192.168.1.0 netmask 255.255.255.0 {
  # Specify the range of lease IP address
  range 192.168.1.50 192.168.1.50;
  range 192.168.1.61 192.168.1.210;
  range 192.168.1.231 192.168.1.250;

  # Specify default gateway
  option routers 192.168.1.1;

  # DNS servers for name resolution
  option domain-name-servers 8.8.8.8, 8.8.4.4;

  # Specify broadcast address
  option broadcast-address 192.168.1.255;

  # Default lease time
  default-lease-time 600;

  # Max lease time
  max-lease-time 7200;

  # Deny unknown clients from getting an IP address
  deny unknown-clients;
}

# Allow win11 to receive an IP address
host win11 {
  hardware ethernet 08:00:27:5d:b8:bc;
}

# Allow win11-2 to receive an IP address
host win11-2 {
  hardware ethernet 08:00:27:fd:a8:e3;
}

```

## Blacklist/Deny List Clients

You can prevent specific clients from receiving an IP address from the DHCP server by adding them to a deny list using the `deny booting` directive. This is useful for blocking unauthorized devices from accessing the network.

### Edit DHCP Configuration File

```
vim /etc/dhcp/dhcpd.conf

```

### Example Configuration for Denying Clients

You can deny specific clients by adding their MAC addresses and using the `deny booting` directive.

### Example 1: Deny a Single Client

This example denies the client with MAC address 08:00:27:ab:46:cf from getting an IP address:

```
authoritative;

# Specify network address and subnet mask
subnet 192.168.1.0 netmask 255.255.255.0 {
  # Specify the range of lease IP address
  range 192.168.1.50 192.168.1.50;
  range 192.168.1.61 192.168.1.210;
  range 192.168.1.231 192.168.1.250;

  # Specify default gateway
  option routers 192.168.1.1;

  # DNS servers for name resolution
  option domain-name-servers 8.8.8.8, 8.8.4.4;

  # Specify broadcast address
  option broadcast-address 192.168.1.255;

  # Default lease time
  default-lease-time 600;

  # Max lease time
  max-lease-time 7200;
}

# Deny specific client from obtaining IP
host win11 {
  hardware ethernet 08:00:27:ab:46:cf;
  deny booting;
}

```

### Example 2: Deny Multiple Clients

You can deny multiple clients by adding separate host blocks for each MAC address.

## Allow for Known and Unknown Clients

You can configure the DHCP server to differentiate between known and unknown clients. Known clients can be assigned IP addresses from a specific pool, while unknown clients can be assigned IP addresses from a different pool.

### Edit DHCP Configuration File

Open the DHCP configuration file for editing.

### Example Configuration for Allowing Known and Unknown Clients

This example assigns IP addresses from 192.168.1.150 to 192.168.1.200 to known clients and 192.168.1.201 to 192.168.1.250 to unknown clients:

```
authoritative;

# Specify network address and subnet mask
subnet 192.168.1.0 netmask 255.255.255.0 {
  # Specify default gateway
  option routers 192.168.1.1;

  # DNS servers for name resolution
  option domain-name-servers 8.8.8.8, 8.8.4.4;

  # Specify broadcast address
  option broadcast-address 192.168.1.255;

  # Default lease time
  default-lease-time 600;

  # Max lease time
  max-lease-time 7200;

  # Pool for known clients
  pool {
    range 192.168.1.150 192.168.1.200;
    allow known-clients;
  }

  # Pool for unknown clients
  pool {
    range 192.168.1.201 192.168.1.250;
    allow unknown-clients;
  }
}

# Define known clients
host win11 {
  hardware ethernet 08:00:27:d6:86:94;
}

host win11-2 {
  hardware ethernet 08:00:27:fd:a8:e3;
}

```

### Restart DHCP Service

After modifying the configuration file, restart the DHCP service:

```
systemctl restart dhcpd

```
