The provided Screen Command Guide offers a comprehensive overview of using the terminal multiplexer screen, including installation, basic commands, session management, 
Without using `screen` (or `tmux`), any long-running command or service started in a terminal **will stop if the terminal is closed**, so the process dies prematurely.

## Basic Commands

Start a screen session: screen.

Start a named session: screen -S <session name> (e.g., screen -S mysession).

Run a command in the background in a named session: screen -S <session_name>-dm <command> (e.g., screen -5 ping-dm ping 8.8.8).

## Managing Sessions

List active sessions: screen -1s.

Reattach the last detached session: screen-r.

Reattach a specific session: screen -r <session_name> (e.g., screen -r mysession).

## Advanced Usage

Run custom scripts in background sessions using screen -S <session_name> -dm <script> [options] (examples given for running nmap-warrior scripts).

## Installing Screen

On RHEL/CentOS:

```
yum install screen

```

On Debian/Ubuntu:

```
apt install screen

```

Display Help:

```
screen --help

```

## Basic Screen Commands

### Start a Screen Session

```
screen

```

### Start a Named Screen Session

```
screen -S <session_name>

```

Example:

```
screen -S mysession

```

### Run a Command in the Background within a Named Screen Session

```
screen -S <session_name> -dm <command>

```

Example:

```
screen -S ping -dm ping 8.8.8.8

```

## Managing Screen Sessions

### List Active Screen Sessions

```
screen -ls

```

### Reattach to the Last Detached Screen Session

```
screen -r

```

### Reattach to a Specific Screen Session

```
screen -r <session_name>

```

Example:

```
screen -r mysession

```

## Advanced Usage

Run a Custom Script in the Background

```
screen -S nmapver -dm nmap-warrior.py -1 Public-IPs.txt -pvu o Public-IPs-Nmap-Output

```

```
screen -S nmapver -dm /home/armour/nmap-warrior.sh -u 192.168.1.1 -pvs -o 192.168.1.1

```

```
screen -S nmapver -dm /home/armour/nmap-warrior.sh -u 192.168.1.1 -pvs

```

## Summary

screen allows running processes in the background.

Named sessions help in managing multiple screens.

Sessions can be detached and reattached as needed.

Use screen -1s to list sessions and screen -r <session_name> to resume a specific session.

# TCPDump Commands

## Installation

To install tcpdump on a system using yum:

```
yum install tcpdump

```

To verify the installation:

```
tcpdump --version

```

## Basic Commands

To view available options and usage instructions:

```
tcpdump -h

```

To display network interfaces that can be used for packet capture:

```
tcpdump -D

```

or equivalently:

```
tcpdump --list-interfaces

```

## Capturing Packets

### Capture Packets on a Specific Interface

To capture packets on enp0s3:

```
tcpdump -i enp0s3

```

### Capture TCP Packets

To capture only TCP packets on enp0s3:

```
tcpdump -i enp0s3 tcp

```

### Capture UDP Packets

To capture only UDP packets on enp0s3:

```
tcpdump -i enp0s3 udp

```

### Capture Packets Without Resolving Hostnames

To avoid DNS resolution and display only numerical IP addresses:

For TCP Packets

```
tcpdump -n -i enp0s3 tcp

```

For UDP Packets

```
tcpdump -n -i enp0s3 udp

```

## Filtering by Port

### Capture TCP Packets on Specific Ports

Port 443 (HTTPS)

```
tcpdump -i enp0s3 tcp port 443

```

Port 8080

```
tcpdump -i enp0s3 tcp port 8080

```

### Capture UDP Packets on Specific Ports

Port 53 (DNS Queries)

```
tcpdump -i enp0s3 udp port 53

```

Capture TCP Packets on eth0 for Port 8080

```
tcpdump -i eth0 tcp port 8080

```

## Advanced Filtering

### Capture Packets from a Specific Source or Destination

Capture packets from a specific source IP

```
tcpdump -i enp0s3 src host 192.168.1.1

```

Capture packets destined for a specific IP

```
tcpdump -i enp0s3 dst host 192.168.1.2

```

### Capture Packets for a Specific Network

Capture traffic for a subnet (e.g., 192.168.1.0/24)

```
tcpdump -i enp0s3 net 192.168.1.0/24

```

## Saving and Reading Packet Captures

### Save Captured Packets to a File

To capture UDP packets on port 53 and save them to dnsdump.pcap for later analysis:

```
tcpdump -v -ni enp0s3 udp port 53 -w dnsdump.pcap

```

### Read Captured Packets from a File

To read the saved capture file:

```
tcpdump -r dnsdump.pcap

```

## Additional Options

### Verbose Output

To capture packets with detailed output:

```
tcpdump -vv -i enp0s3

```

### Limit Packet Capture to a Specific Count

To capture only 10 packets and stop:

```
tcpdump -c 10 -i enp0s3

```
