## DNS Client Tools Commands

Below are the common commands used to manage and query DNS client tools.

### Check Installed BIND Packages

Use the following command to list installed BIND packages:

```
rpm -qa | grep bind

```

### Check Installed BIND Utilities

Use this command to list installed bind-utils packages:

```
rpm -qa | grep bind-utils

```

### Install BIND Utilities

To install BIND utilities using yum:

```
yum install bind-utils

```

### List Files Installed by bind-utils

To list all files installed by the bind-utils package:

```
rpm -ql bind-utils

```

### List Configuration Files for bind-utils

To list configuration files for the bind-utils package:

```
rpm -qc bind-utils

```

### List Documentation Files for bind-utils

To list documentation files for the bind-utils package:

```
rpm -qd bind-utils

```

## Common DNS Client Tools Installed with bind-utils

These are the commonly used DNS client tools included with bind-utils:

- `/usr/bin/delv` - DNS lookup and validation utility
- `/usr/bin/dig` - Query DNS servers for information about domains
- `/usr/bin/host` - Simple utility for performing DNS lookups
- `/usr/bin/mdig` - Multiple query version of dig
- `/usr/bin/nslookup` - Legacy tool to query DNS servers
- `/usr/bin/nsupdate` - Dynamic DNS update utility,,

## DNS Records

DNS records define how domain names are mapped to IP addresses and other resources.

### A (Address Mapping) Records

Specifies an IPv4 address for a given host.

**Example**:

[example.com](http://example.com/) → 192.168.1.1

### AAAA (IPv6 Address) Records

Specifies an IPv6 address for a given host.

**Example**:

[example.com](http://example.com/) 2001:0db8::ff00:0042:8329

### CNAME (Canonical Name) Records

Specifies an alias for another domain name.

**Example**:

[wp.armourinfosec.com](http://wp.armourinfosec.com/) [armourinfosec.wordpress.com](http://armourinfosec.wordpress.com/) 20.20.20.20,,

### NS (Name Server) Records

Specifies an authoritative name server for a given domain.

**Example**:

[example.com](http://example.com/) [ns1.example.com](http://ns1.example.com/)

### PTR (Pointer) Records

Used for reverse DNS lookups (mapping IP addresses to hostnames).

**Example**:

192.168.1.1 [example.com](http://example.com/)

### SOA (Start of Authority) Records

Specifies authoritative information about a DNS zone, including the primary nameserver and zone serial number.

**Example**:

```
example.com. IN SOA ns1.example.com. admin.example.com. (
2025031101 ; serial
3600       ; refresh
1800       ; retry
1209600    ; expire
86400 )    ; minimum TTL

```

### HINFO (Host Information) Records

Provides information about a host's hardware and operating system.

**Example**:

[example.com](http://example.com/). IN HINFO "Intel i7" "Linux"

### MX (Mail Exchanger) Records

Specifies a mail exchange server for a domain.

**Example**:

[example.com](http://example.com/) [mail.example.com](http://mail.example.com/) (priority 10)

### TXT (Text) Records

Holds arbitrary text data for a domain.

Often used for SPF (Sender Policy Framework) or DKIM (DomainKeys Identified Mail) records.

**Example**:

```
example.com. IN TXT "v=spf1 include:_spf.google.com ~all"

```

## Basic dig Commands

### Perform a Basic DNS Lookup

Use dig without any arguments to get 13 Roots Server:

```
dig

```

### Lookup a Specific Domain

Query DNS records for a specific domain:

```
dig google.com

```

### Query a Specific DNS Server

Query [google.com](http://google.com/) using a specific DNS server:

```
dig google.com @64.6.64.6

```

## Query Specific DNS Record Types

### A Record (IPv4)

Query the IPv4 address of a domain:

```
dig google.com A

```

or:

```
dig A google.com

```

### AAAA Record (IPv6)

Query the IPv6 address of a domain:

```
dig google.com AAAA

```

or:

```
dig AAAA google.com

```

### NS Record (Name Server)

Query the name server for a domain:

```
dig google.com NS

```

or:

```
dig ns google.com

```

### MX Record (Mail Exchange)

Query the mail exchange server for a domain:

```
dig google.com MX

```

### SOA Record (Start of Authority)

Query the SOA record for a domain:

```
dig google.com SOA

```

### TXT Record (Text)

Query TXT records for a domain:

```
dig google.com TXT

```

### ANY Record (All available records)

Retrieve all available DNS records for a domain:

```
dig google.com ANY

```

## Shortened Output

### Short Answer Format

Display a simplified output:

```
dig google.com +short

```

### A Record in Short Format

```
dig armourinfosec.com A +short

```

### AAAA Record in Short Format

```
dig armourinfosec.com AAAA +short

```

### NS Record in Short Format

```
dig armourinfosec.com NS +short

```

### MX Record in Short Format

```
dig armourinfosec.com MX +short

```

### ANY Record in Short Format

```
dig google.com ANY +short

```

## Customizing Output

### Suppress all output

```
dig google.com +noall

```

### Display Only the Answer Section

```
dig google.com +noall +answer

```

### Display the Question and Answer Section

```
dig google.com +noall +answer question

```

### Suppress comments, questions, authority, additional, and stats sections

```
dig google.com +nocomments +noquestion +noauthority +noadditional +nostats

```

## Bulk DNS Lookup

### Create a File with Domains

Create a file named domain_list.txt:

```
vim domain_list.txt

```

**Example content**:

```
google.com
facebook.com
armourinfosec.com
youtube.com

```

### Query Multiple Domains from a File

Use dig to query all domains listed in the file:

```
dig -f domain_list.txt

```

### Shortened Output for Multiple Domains

Get a simplified output for all domains:

```
dig -f domain_list.txt +short

```

### Save Output to a File

Save the output to a file:

```
dig -f domain_list.txt +short > ip_add_list.txt

```

## Reverse DNS Lookup

### Reverse Lookup (PTR Record)

Find the hostname associated with an IP address:

```
dig -x 8.8.8.8

```

or:

```
dig x 8.8.8.8 PTR

```

### Shortened Reverse Lookup Output

```
dig x 8.8.8.8 +short

```

### Reverse Lookup Using Bulk IPs

### Create a File with IP Addresses

Create a file named ip_add_list2.txt:

```
vim ip_add_list2.txt

```

**Example content**:

```
64.6.64.6
8.8.4.4
20.20.20.20

```

### Generate -x Format Using awk

Convert IPs to -x format using awk:

```
awk '$0="-x" $0' ip_add_list2.txt > ip_add_list3.txt

```

### Generate -x Format Using sed

Convert IPs to x format using sed:

```
cat ip_add_list2.txt | sed -e "s/.*/-x &/" > ip_add_list4.txt

```

### Query Reverse DNS for Multiple IPs

Perform reverse lookups using the modified file:

```
dig -f ip_add_list3.txt +short

```

### Query Reverse DNS from an IP List File

```
dig f ip.txt +short

```

## host

host - DNS Client Tools

The host command is a simple DNS lookup utility used to convert hostnames to IP addresses and perform other DNS queries.

### Basic host Commands

### Lookup a Domain's IP Address

Use host to find the IP address of a domain:

```
host google.com

```

### Query Specific DNS Record Types

### NS Record (Name Server)

Query the name server for a domain:

```
host -t ns google.com

```

### MX Record (Mail Exchange)

Query the mail exchange servers for a domain:

```
host -t mx google.com

```

### TXT Record (Text)

Query the TXT records for a domain:

```
host -t txt google.com

```

### SOA Record (Start of Authority)

Query the SOA record for a domain:

```
host -t soa google.com

```

### Query SOA Record Using a Specific DNS Server

Specify a particular DNS server to query the SOA record:

```
host -t soa google.com 64.6.64.6

```

## nslookup

nslookup - DNS Client Tools

The nslookup (Name Server Lookup) command is a network administration tool for querying the Domain Name System (DNS) to obtain domain name or IP address mapping and other DNS records.

### Basic nslookup Commands

### Lookup a Domain's IP Address

Use nslookup to find the IP address of a domain:

```
nslookup google.com

```

### Lookup a Domain Using a Specific DNS Server

Query a domain using a specific DNS server:

```
nslookup google.com 64.6.64.6

```

### Query Specific DNS Record Types

### A Record (IPv4)

Query the A record (IPv4 address) of a domain:

```
nslookup -type=A google.com

```

### AAAA Record (IPv6)

Query the AAAA record (IPv6 address) of a domain:

```
nslookup -type=AAAA google.com

```

### NS Record (Name Server)

Query the name server for a domain:

```
nslookup -type=NS google.com

```

### MX Record (Mail Exchange)

Query the mail exchange servers for a domain:

```
nslookup -type=MX google.com

```

### SOA Record (Start of Authority)

Query the SOA record for a domain:

```
nslookup -type=SOA google.com

```

### TXT Record (Text)

Query the TXT records for a domain:

```
nslookup -type=TXT google.com

```

### Other Useful Lookups

### Lookup a Subdomain

Query a specific subdomain:

```
nslookup www.armourinfosec.com

```

### Query All Available Records

Retrieve all available DNS records for a domain:

```
nslookup -type=ANY google.com

```

### Interactive nslookup Mode

You can use nslookup in interactive mode to perform multiple queries without reopening the tool each time.

### Start Interactive Mode

Launch nslookup without any arguments:

```
nslookup

```

### Example Interactive Commands:

1. Query a specific domain:

```
facebook.com

```

1. Set the query type to NS records:

```
set type=ns
facebook.com

```

1. Set the query type to TXT records:

```
set type=txt
facebook.com

```

1. Use a specific DNS server:

```
server 64.6.64.6

```

## DNS Server

### DNS Server Types

### Non-Authoritative (Recursive) Nameserver

Non-authoritative nameservers are responsible for resolving DNS queries by contacting other nameservers to find the answer. They do not store original DNS records but rely on other sources.

**Caching Nameserver**

Caching nameservers temporarily store DNS query results to improve resolution time and reduce the load on authoritative servers.

**Example command to test DNS resolution using a caching nameserver**:

```
dig example.com 88.8.8.8

```

### Forwarders Nameserver

Forwarding nameservers receive DNS queries and pass them to another nameserver (usually a caching or authoritative server) for resolution.

**Example command to configure a forwarding nameserver**:

```
echo "nameserver 8.8.8.8" >> /etc/resolv.conf

```

### Authoritative Nameserver

Authoritative nameservers store and provide responses for DNS queries about domains they are responsible for. They hold the actual DNS records.

### Primary Nameserver

The primary nameserver is the main source for DNS zone data and is responsible for updates and changes to DNS records.

**Example of a named.conf configuration for a primary nameserver**:

```

```

### Secondary Nameserver

The secondary nameserver obtains its zone data from the primary nameserver and acts as a backup in case the primary is unavailable.

**Example of a named.conf configuration for a secondary nameserver**:

```

```

### 1. **Non-Authoritative DNS Server**

- **Role**: A non-authoritative server **queries other nameservers** to resolve domain names.
- **Function**: It **does not store** DNS records but instead **contacts authoritative servers** to get the information for clients.
- **Example**: Google’s public DNS server (8.8.8.8) is a recursive server that looks up DNS data on behalf of the user.

---

### 2. **Authoritative DNS Server**

- **Role**: An authoritative server **holds and provides** the actual DNS records for a domain.
- **Function**: It is the **source of truth** for DNS data, providing **responses** to queries about the domains it manages.
- **Example**: The DNS server for `example.com` is authoritative for that domain and responds with the correct DNS records when queried.

Here’s a simple breakdown of the additional types of DNS servers:

### 1. **Caching DNS Server**

- **Role**: **Stores DNS query results** temporarily to speed up future lookups.
- **Function**: When a query is made, it checks if the answer is cached. If not, it fetches the answer and stores it for a short time (TTL - Time to Live).
- **Example**: Your local ISP's DNS might act as a caching server to speed up web browsing.

---

### 2. **Forwarding DNS Server**

- **Role**: **Forwards DNS queries** to another DNS server (usually an authoritative or caching server) for resolution.
- **Function**: It doesn't resolve DNS itself but passes queries to another server, often to centralize or filter DNS requests.
- **Example**: A network might configure its internal DNS to forward queries to Google's DNS (8.8.8.8) for resolution.

---

### 3. **Primary DNS Server**

- **Role**: The **main server** responsible for managing and updating DNS records for a domain.
- **Function**: It stores the original (master) DNS zone data and makes it available to other DNS servers (like secondary servers).
- **Example**: The primary server for `example.com` holds all the official DNS records for the domain.

---

### 4. **Secondary DNS Server**

- **Role**: **Backup server** for a domain’s DNS records.
- **Function**: It gets its DNS data from the primary DNS server (via zone transfers) and provides redundancy and load balancing.
- **Example**: If the primary server for `example.com` is down, the secondary server will still be able to respond to DNS queries.

---

In short:

- **Caching** speeds up queries.
- **Forwarding** passes queries to another server.
- **Primary** holds the main DNS data.
- **Secondary** acts as a backup to the primary.

### **1. Non-Authoritative Nameserver** (just passes info, caches it)

- **Caching Nameserver** → Stores DNS responses temporarily to speed up future queries.
- **Forwarding Nameserver** → Forwards queries to another DNS server (usually upstream).

### **2. Authoritative Nameserver** (gives the official answer)

- **Primary (Master) Nameserver** → Stores the original DNS records for the domain.
- **Secondary (Slave) Nameserver** → Gets copies of the DNS records from the Primary for redundancy.
