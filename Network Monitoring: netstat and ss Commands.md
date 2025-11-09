## Overview

netstat and ss are powerful tools to monitor network connections and sockets on Linux.

- **netstat**: Older tool, part of the net-tools package, and often not installed by default.
- **ss** (socket statistics): Its modern replacement—faster, more efficient, and pre-installed on most Linux distributions.

## Installing netstat (if not available)

### RHEL/CentOS/Fedora:

```
yum install net-tools

```

### Debian/Ubuntu:

```
apt install net-tools

```

The ss command is usually available out of the box on modern Linux systems.

## netstat Usage

### Display active connections

```
netstat

```

### Show all sockets (listening + established)

```
netstat -a

```

### Show numerical addresses (skip DNS lookups)

```
netstat -n

```

or

```
netstat -na

```

### Show only TCP connections

```
netstat -t

```

### Show only UDP connections

```
netstat -u

```

### Show both TCP and UDP connections

```
netstat -tu

```

### Show TCP & UDP in numeric format (faster)

```
netstat -tun

```

### Show listening TCP & UDP ports

```
netstat -ltun

```

### Show listening services with numeric IPs and process details

```
netstat -nltup

```

or

```
netstat -natup

```

## ss Usage (Modern Alternative)

### Display active connections

```
ss

```

### Show only listening sockets

```
ss -l

```

### Show all sockets (listening + active)

```
ss -a

```

### Show numerical addresses only

```
ss -n

```

or

```
ss -na

```

### Show only TCP connections

```
ss -t

```

### Show only UDP connections

```
ss -u

```

### Show both TCP and UDP connections

```
ss -tu

```

### Show TCP & UDP in numeric format (fast)

```
ss -tun

```

### Show listening ports for TCP and UDP

```
ss -nltun

```

### Show listening services with numeric IPs and process details

```
ss -nltup

```

or

```
ss -natup

```

## netstat vs ss

| Feature | netstat | ss (Recommended) |
| --- | --- | --- |
| Speed | Slower | Faster (direct kernel access) |
| Availability | Requires net-tools package | Pre-installed on most distributions |
| Protocols | TCP, UDP, ICMP | TCP, UDP, RAW, UNIX, and more |
| Filtering | Limited options | Advanced filtering capabilities |

## Real-World Usage Examples

### Find which process is using a specific port (e.g., port 80)

```
ss -ltnp | grep :80

```

### Find which process is using a specific port (e.g., port 445)

```
ss -ltnp | grep :445

```

### Find which process is using a specific port (e.g., port 22)

```
ss -ltnp | grep :22

```

### Alternative for port 80 with netstat

```
netstat -tulnp | grep 80

```

### Check all listening services and their PIDs

```
ss -ltnp

```

or

```
netstat -tulnp

```

### Display established connections only

```
ss -t state established

```

or

```
netstat -ant | grep ESTABLISHED

```

### Show summary of socket usage

```
netstat -s

```

### Find connections to a specific IP

```
ss -tn dst 192.168.1.100

```

or

```
netstat -ant | grep 192.168.1.100

```

### Check which users/services are bound to ports

```
ss -lptn

```

or

```
netstat -lptn

```
