Process management in Linux involves monitoring, controlling, and terminating processes to ensure system stability and performance. Below are the most commonly used commands and their usage.

## Running Commands in Background & Foreground

- Run in foreground:
    
    ```bash
    ping 8.8.8.8
    
    ```
    
- Run in background:
    
    ```bash
    ping 8.8.8.8 &
    
    ```
    
- Send output to /dev/null and run in background:
    
    ```bash
    ping 8.8.8.8 > /dev/null &
    
    ```
    
    ```bash
    seq 100000000000000000000000 > /dev/null &
    
    ```
    

## Managing Jobs

- List background jobs:
    
    ```bash
    jobs
    
    ```
    
- Bring the last job to the foreground:
    
    ```bash
    fg
    
    ```
    
- Bring job number 2 to the foreground:
    
    ```bash
    fg %2
    
    ```
    

## Viewing Processes with ps

- Current shell processes:
    
    ```bash
    ps
    
    ```
    
- Detailed process info:
    
    ```bash
    ps -l
    
    ```
    
- All processes with CPU/memory usage (BSD style):
    
    ```bash
    ps aux
    
    ```
    
- All processes (standard style):
    
    ```bash
    ps -e
    
    ```
    
- Tree view of processes:
    
    ```bash
    ps -axjf
    
    ```
    
- Processes owned by root:
    
    ```bash
    ps -u root -u root u
    
    ```
    
- Processes owned by user armour:
    
    ```bash
    ps -U armour -u armour u
    
    ```
    

## Monitoring Processes in Real-Time

- View real-time running processes:
    
    ```bash
    top
    
    ```
    
- Install htop (modern alternative):
    - Debian/Ubuntu:
        
        ```bash
        apt install htop
        
        ```
        
    - RHEL/CentOS:
        
        ```bash
        yum install htop
        
        ```
        
- Interactive process monitor:
    
    ```bash
    htop
    
    ```
    
- Show tree structure:
    
    ```bash
    htop -t
    
    ```
    
- Show processes for a specific user:
    
    ```bash
    htop -u armour
    
    ```
    
- Monitor a specific PID:
    
    ```bash
    htop -p 2780
    
    ```
    

## Killing Processes

- Kill by PID:
    
    ```bash
    kill 2526
    
    ```
    
- Force kill (SIGKILL):
    
    ```bash
    kill -9 1891
    
    ```
    
- Killall instances of a command:
    
    ```bash
    yum install psmisc
    killall seq
    
    ```
    
    ```bash
    killall gdm
    
    ```
    
- Kill processes using regex pattern:
    
    ```bash
    killall -r firefox
    
    ```
    
- Kill processes owned by user armour:
    
    ```bash
    killall -u armour
    
    ```
    
- Kill by process name:
    
    ```bash
    pkill ping
    
    ```
    
- Kill all processes for a user:
    
    ```bash
    pkill -u armour
    
    ```
    
- Kill exact process name owned by a user:
    
    ```bash
    pkill -u armour -x ping
    
    ```
    

## Summary of Process Management Commands

| Command | Purpose |
| --- | --- |
| jobs | List background jobs |
| fg | Bring a job to the foreground |
| ps aux | View all processes |
| top/htop | Monitor system processes in real-time |
| kill PID | Terminate a process by PID |
| killall name | Kill all instances of a process |
| pkill name | Kill by process name |

# Memory Management in Linux

Memory management is crucial for monitoring system performance and troubleshooting issu

## Checking Memory Usage with free

The free command displays available and used memory in the system.

- Shows memory usage in a human-readable format:
    
    ```bash
    free -h
    
    ```
    
- Displays memory usage in gigabytes:
    
    ```bash
    free -g
    
    ```
    
- Displays memory usage in megabytes:
    
    ```bash
    free -m
    
    ```
    
- Displays memory usage in kilobytes:
    
    ```bash
    free -k
    
    ```
    

## Monitoring System Performance with vmstat

The vmstat command provides detailed system performance statistics, including CPU, memory, and disk usage.

- Displays CPU and memory usage statistics:
    
    ```bash
    vmstat
    
    ```
    
- Shows active and inactive memory:
    
    ```bash
    vmstat -a
    
    ```
    
- Displays disk statistics:
    
    ```bash
    vmstat -d
    
    ```
    
- Shows statistics in kilobytes:
    
    ```bash
    vmstat -S k
    
    ```
    
- Shows statistics in megabytes:
    
    ```bash
    vmstat -S M
    
    ```
    
- Continuous monitoring: 5 reports every 4 seconds:
    
    ```bash
    vmstat 4 5
    
    ```
    
- Same as above but in megabytes:
    
    ```bash
    vmstat -S M 4 5
    
    ```
    
- Includes timestamp in output (megabytes with time):
    
    ```bash
    vmstat -t 4 5 -S M
    
    ```
    

## Disk and CPU Performance with iostat

The iostat command helps analyze CPU and disk I/O performance.

- Install iostat if not available:
    
    ```bash
    yum install sysstat
    
    ```
    
- Displays CPU and disk performance statistics:
    
    ```bash
    iostat
    
    ```
    
- Updates the statistics 5 times at 4-second intervals:
    
    ```bash
    iostat 4 5
    
    ```
    

## Listing Open Files with lsof

The lsof (List Open Files) command displays files opened by processes.

- Install lsof if not available:
    
    ```bash
    yum install lsof
    
    ```
    

### Basic Usage

- Lists all open files:
    
    ```bash
    lsof
    
    ```
    
- Shows open files for user root:
    
    ```bash
    lsof -u root
    
    ```
    
- Shows open files for user armour:
    
    ```bash
    lsof -u armour
    
    ```
    
- Displays open files for root, without resolving hostnames:
    
    ```bash
    lsof -u root -n
    
    ```
    

### Checking Network Connections

- Lists all open network connections:
    
    ```bash
    lsof -i
    
    ```
    
- Same as above, but without resolving hostnames:
    
    ```bash
    lsof -i -n
    
    ```
    
- Lists open TCP connections:
    
    ```bash
    lsof -n i TCP
    
    ```
    
- Lists open UDP connections:
    
    ```bash
    lsof -n i UDP
    
    ```
    
- Shows processes using port 22 (SSH):
    
    ```bash
    lsof -n -i TCP:22
    
    ```
    
- Shows processes on ports 80 (HTTP), 443 (HTTPS), 22 (SSH):
    
    ```bash
    lsof -n i TCP:80,443,22
    
    ```
    
- Displays TCP connections within port range 80-500:
    
    ```bash
    lsof -n -i TCP:80-500
    
    ```
    
- Shows UDP connections on port 53 (DNS):
    
    ```bash
    lsof -n -i UDP:53
    
    ```
    

### Managing Open File Descriptors

- Lists open files for process ID 6020:
    
    ```bash
    lsof -n -p 6020
    
    ```
    
- Displays open network files for process 1571:
    
    ```bash
    lsof -n -i -p 1571
    
    ```
    
- Shows IPv4 connections:
    
    ```bash
    lsof -n i 4
    
    ```
    
- Shows IPv6 connections:
    
    ```bash
    lsof -n -i 6
    
    ```
    

### Killing Processes with lsof

- Kill all processes owned by user armour:
    
    ```bash
    kill -9 `lsof -t -u armour`
    
    ```
    

## Summary of Memory & Process Monitoring Commands

| Command | Purpose |
| --- | --- |
| free -h | Show memory usage in human-readable format |
| vmstat 4 5 | Monitor CPU and memory stats every 4s, 5 times |
| iostat | View CPU and disk performance |
| lsof -u armour | List files opened by user armour |
| lsof -n -i TCP:22 | Show processes using port 22 (SSH) |
| kill -9 `lsof -t -u armour` | Kill all processes owned by armour |

Would you like me to also include real-world troubleshooting examples (e.g., how to detect memory leaks or identify which process is using too much RAM)? That could make this a more practical guide.
