## Edit Configuration for Master Nameserver

### Edit the named.conf File

Open and edit the main BIND configuration file using vim.

```bash
vim /etc/named.conf

```

## Sample Configuration for Master Nameserver

Below is the cleaned and corrected sample content for `/etc/named.conf`. Duplicates have been removed (e.g., repeated `allow-query` and `recursion no` sections), line numbers adjusted for clarity, and formatting errors fixed (e.g., removed erroneous symbols like "و" and "ล").

```
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named (8) DNS
// server as a caching-only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind/sample/ for example named configuration files.
//
// See the BIND Administrator's Reference Manual (ARM) for details about the
// configuration located in /usr/share/doc/bind-{version}/Bv9ARM.html

options {
    listen-on port 53 { 127.0.0.1; 192.168.1.50; };
    listen-on-v6 port 53 { ::1; };
    directory "/var/named";
    dump-file "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    recursing-file "/var/named/data/named.recursing";
    secroots-file "/var/named/data/named.secroots";
    allow-query { localhost; 192.168.1.0/24; };

    // Disable recursion for authoritative server
    recursion no;
    dnssec-enable yes;
    dnssec-validation yes;

    /* Path to ISC DLV key */
    bindkeys-file "/etc/named.root.key";
    managed-keys-directory "/var/named/dynamic";

    pid-file "/run/named/named.pid";
    session-keyfile "/run/named/session.key";
};

logging {
    channel default_debug {
        file "data/named.run";
        severity dynamic;
    };
};

// Root Zone
zone "." IN {
    type hint;
    file "named.ca";
};

// Forward Zone for example.com
zone "example.com" IN {
    type master;
    file "/var/named/example.com.zone";
    allow-update { none; };
};

// Reverse Zone for 192.168.1.0/24
zone "1.168.192.in-addr.arpa" IN {
    type master;
    file "/var/named/1.168.192.zone";
    allow-update { none; };
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

```

## Create Forward Zone File

### Create the Forward Zone File

Open and create the forward zone file for `example.com` using vim.

```bash
vim /var/named/example.com.zone

```

**Example Content:**

```
$TTL 86400
@ IN SOA ns1.example.com. admin.example.com. (
    2025031101 ; Serial
    3600       ; Refresh
    1800       ; Retry
    604800     ; Expire
    86400 )    ; Minimum TTL

@       IN NS  ns1.example.com.
ns1     IN A   192.168.1.50
www     IN A   192.168.1.100

```

## Create Reverse Zone File

### Create the Reverse Zone File

Open and create the reverse zone file for the `192.168.1.0/24` network using vim.

```bash
vim /var/named/1.168.192.zone

```

**Example Content:**

```
$TTL 86400
@ IN SOA ns1.example.com. admin.example.com. (
    2025031101 ; Serial
    3600       ; Refresh
    1800       ; Retry
    604800     ; Expire
    86400 )    ; Minimum TTL

@       IN NS  ns1.example.com.
50      IN PTR ns1.example.com.
100     IN PTR www.example.com.

```

## Set File Permissions

### Ensure Proper Ownership and Permissions

Set the ownership to the `named` user and group, and apply read/write permissions for owner and read for group.

```bash
chown named:named /var/named/example.com.zone

```

```bash
chown named:named /var/named/1.168.192.zone

```

```bash
chmod 640 /var/named/example.com.zone

```

```bash
chmod 640 /var/named/1.168.192.zone

```

## Start and Enable BIND Service

### Start and Enable the Service

Start the BIND (named) service and enable it to run on boot.

```bash
systemctl start named

```

```bash
systemctl enable named

```

## Test Configuration

### Verify DNS Resolution

Use the `dig` command to test forward and reverse DNS lookups against the server at `192.168.1.50`.

```bash
dig @192.168.1.50 example.com

```

```bash
dig @192.168.1.50 www.example.com

```

```bash
dig -x 192.168.1.100

```

## Firewall Configuration

### Allow DNS Traffic

Configure the firewall to permit DNS queries on port 53 (both TCP and UDP).

### For UFW (Uncomplicated Firewall)

```bash
ufw allow 53/tcp

```

```bash
ufw allow 53/udp

```

### For Firewalld

```bash
firewall-cmd --add-port=53/tcp --permanent

```

```bash
firewall-cmd --add-port=53/udp --permanent

```

```bash
firewall-cmd --reload

```

## Explanation

- **zone "[example.com](http://example.com/)" IN { type master; }**

This defines the server as the authoritative master for the `example.com` forward zone. The `file` parameter specifies the path to the zone file containing A records and other resource records.

- **zone "1.168.192.in-addr.arpa" IN { type master; }**

This configures reverse DNS resolution for the `192.168.1.0/24` IP subnet using PTR records. The zone name follows the in-addr.arpa convention for reverse lookups.

- **dig commands**

These are used to query the DNS server directly and verify that name resolution (forward and reverse) is functioning correctly. Check for expected responses like the SOA record, NS records, A records, and PTR records in the output.

# Forward Zone Configuration

## Set Hostname

### Configure the Hostname

Set the hostname for the DNS server to `ns1.armour.local`.

```bash
vim /etc/hostname

```

**Example Content:**

```
ns1.armour.local

```

- **Note**: The `/etc/hostname` file defines the system's hostname, which should match the DNS server's fully qualified domain name (FQDN). This ensures proper identification in the network.

## Edit Hosts File

### Update the Hosts File

Add hostname mappings for `ns1.armour.local` in the `/etc/hosts` file to ensure local resolution.

```bash
vim /etc/hosts

```

**Example Content:**

```
127.0.0.1   localhost ns1 ns1.armour.local
::1         localhost localhost6 ns1 ns1.armour.local
192.168.1.32 ns1 ns1.armour.local

```

- **Note**: The `/etc/hosts` file maps IP addresses to hostnames for local resolution. Including both IPv4 and IPv6 localhost entries, along with the server’s IP, ensures compatibility and proper resolution.

## Edit DNS Zones Configuration

### Modify named.rfc1912.zones

Edit the `/etc/named.rfc1912.zones` file to define the forward zone for `armour.local`. Duplicates (e.g., repeated `allow-update`) have been removed, and formatting has been standardized.

```bash
vim /etc/named.rfc1912.zones

```

**Example Content:**

```
// named.rfc1912.zones:
// Provided by Red Hat caching-nameserver package
//
// ISC BIND named zone configuration for zones recommended by
// RFC 1912 section 4.1 localhost TLDs and address zones
// and https://tools.ietf.org/html/rfc6303
// (c)2007 RW Franks

zone "localhost.localdomain" IN {
    type master;
    file "named.localhost";
    allow-update { none; };
};

zone "localhost" IN {
    type master;
    file "named.localhost";
    allow-update { none; };
};

zone "1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa" IN {
    type master;
    file "named.loopback";
    allow-update { none; };
};

zone "1.0.0.127.in-addr.arpa" IN {
    type master;
    file "named.loopback";
    allow-update { none; };
};

zone "0.in-addr.arpa" IN {
    type master;
    file "named.empty";
    allow-update { none; };
};

zone "armour.local" IN {
    type master;
    file "forward.armour.local";
    allow-update { none; };
};

```

- **Note**: The `/etc/named.rfc1912.zones` file defines standard and custom zones. The `armour.local` zone is added as a master zone, referencing the `forward.armour.local` file for DNS records. Standard zones like `localhost` and reverse zones for loopback addresses are included per RFC 1912.

## Create Forward Zone File

### Copy and Modify the Zone File

Copy the example `named.localhost` file and edit it to create the forward zone file for `armour.local`.

```bash
cd /var/named/

```

```bash
cp -v named.localhost forward.armour.local

```

```bash
vim /var/named/forward.armour.local

```

**Example Content:**

```
$TTL 1D
@ IN SOA ns1.armour.local. root.armour.local. (
    20250313   ; serial
    3600       ; refresh
    1800       ; retry
    604800     ; expire
    86400 )    ; minimum

@           IN NS   ns1.armour.local.
ns1         IN A    192.168.1.32
armour.local. IN A    192.168.1.32
www         IN CNAME armour.local.
router      IN A    192.168.1.1
emp1        IN A    192.168.1.200
emp2        IN A    192.168.1.201
; Mail exchange
@           IN MX   10 mail.armour.local.
mail        IN A    192.168.1.50
; Text record for SPF
@           IN TXT  "v=spf1 mx a ~all"
; Additional Services
ftp         IN CNAME armour.local.
dev         IN A    192.168.1.100
fileserver  IN A    192.168.1.110
db          IN A    192.168.1.120
test        IN A    192.168.1.130
vpn         IN A    192.168.1.140
git         IN A    192.168.1.150
webapp      IN A    192.168.1.160
logs        IN A    192.168.1.170

```

- **Note**: The forward zone file defines DNS records for the `armour.local` domain. It includes:
- **SOA Record**: Specifies the primary nameserver (`ns1.armour.local`) and administrative contact (`root.armour.local`).
- **NS Record**: Declares the nameserver for the domain.
- **A Records**: Map hostnames to IPv4 addresses (e.g., `ns1`, `www`, `router`).
- **CNAME Records**: Aliases like `www` and `ftp` point to `armour.local`.
- **MX Record**: Defines the mail server (`mail.armour.local`) with priority 10.
- **TXT Record**: Specifies an SPF record for email security.
- The duplicate `fileserver` entry was removed, and formatting was standardized.

## Set Permissions

### Configure Zone File Permissions

Ensure the `named` group owns the zone file and set appropriate permissions.

```bash
chgrp named /var/named/forward.armour.local

```

```bash
chmod 640 /var/named/forward.armour.local

```

- **Note**: The `named` group ownership allows the BIND service to access the file, while `640` permissions restrict access to read/write for the owner (`root`) and read-only for the group (`named`).

## Restart the Named Service

### Restart BIND

Restart the `named` service to apply configuration changes.

```bash
systemctl restart named.service

```

- **Note**: Restarting the `named` service reloads the configuration and zone files without interrupting existing queries.

## Check Configuration

### Verify Syntax

Check for syntax errors in the BIND configuration and zone files.

```bash
named-checkconf

```

```bash
named-checkconf /etc/named.conf

```

```bash
named-checkconf /etc/named.rfc1912.zones

```

```bash
named-checkconf /etc/named.root.key

```

- **Note**: The `named-checkconf` command validates the syntax of BIND configuration files. Running it without arguments checks `/etc/named.conf` and included files, while specifying individual files ensures thorough validation.

## Verify Forward Zone

### Check the Forward Zone File

Verify the integrity of the `armour.local` forward zone file.

```bash
named-checkzone armour.local /var/named/forward.armour.local

```

**Expected Output:**

```
zone armour.local/IN: loaded serial 20250313
OK

```

- **Note**: The `named-checkzone` command ensures the zone file is syntactically correct and loads without errors. The output confirms the zone is valid and specifies the loaded serial number.

## Firewall Configuration

### Allow DNS Traffic

Configure the firewall to allow DNS traffic on port 53 for both TCP and UDP.

### For UFW (Uncomplicated Firewall)

```bash
ufw allow 53/tcp

```

```bash
ufw allow 53/udp

```

### For Firewalld

```bash
firewall-cmd --add-port=53/tcp --permanent

```

```bash
firewall-cmd --add-port=53/udp --permanent

```

```bash
firewall-cmd --reload

```

- **Note**: DNS uses both TCP and UDP on port 53. TCP is used for zone transfers and large queries, while UDP handles most DNS queries. The `-permanent` flag in Firewalld ensures rules persist after a reboot, and `firewall-cmd --reload` applies the changes immediately.

## Test Resolution

### Verify DNS Resolution

Use `dig` to test forward DNS resolution for various records in the `armour.local` domain.

```bash
dig @192.168.1.32 armour.local

```

```bash
dig @192.168.1.32 emp1.armour.local

```

```bash
dig @192.168.1.32 ftp.armour.local

```

```bash
dig mx @192.168.1.32 armour.local

```

- **Note**: The `dig` commands query the DNS server at `192.168.1.32` to verify resolution of:
- The domain (`armour.local`) for its A record.
- A specific host (`emp1.armour.local`) for its A record.
- A CNAME record (`ftp.armour.local`).
- The MX record for mail server resolution.
- Check the `ANSWER SECTION` in the `dig` output for correct records and ensure the server responds authoritatively.

# Reverse Zone Configuration

## Edit DNS Zones Configuration

### Modify named.rfc1912.zones

Edit the `/etc/named.rfc1912.zones` file to define the reverse zone for `192.168.1.0/24`. The forward `armour.local` zone is retained. Duplicates (e.g., repeated `allow-update`) have been removed, and formatting has been standardized. Corrected typo: "1p6.arpa" to "ip6.arpa". Updated RFC reference for accuracy.

```bash
vim /etc/named.rfc1912.zones

```

**Example Content:**

```
// named.rfc1912.zones:
//
// Provided by Red Hat caching-nameserver package
//
// ISC BIND named zone configuration for zones recommended by
// RFC 1912 section 4.1 localhost TLDs and address zones
// and <http://www.ietf.org/internet-drafts/draft-ietf-dnsop-default-local-zones-02.txt>
// (c)2007 RW Franks

zone "localhost.localdomain" IN {
    type master;
    file "named.localhost";
    allow-update { none; };
};

zone "localhost" IN {
    type master;
    file "named.localhost";
    allow-update { none; };
};

zone "1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa" IN {
    type master;
    file "named.loopback";
    allow-update { none; };
};

zone "1.0.0.127.in-addr.arpa" IN {
    type master;
    file "named.loopback";
    allow-update { none; };
};

zone "0.in-addr.arpa" IN {
    type master;
    file "named.empty";
    allow-update { none; };
};

zone "armour.local" IN {
    type master;
    file "forward.armour.local";
    allow-update { none; };
};

zone "1.168.192.in-addr.arpa" IN {
    type master;
    file "reverse.armour.local";
    allow-update { none; };
};

```

- **Note**: The `/etc/named.rfc1912.zones` file manages standard and custom zones. The new `1.168.192.in-addr.arpa` zone enables reverse DNS lookups for the `192.168.1.0/24` subnet, following the in-addr.arpa convention. Standard zones (e.g., loopback, localhost) are preserved per RFC 1912 and related drafts.

## Create Reverse Zone File

### Copy and Modify the Zone File

Navigate to the `/var/named/` directory, copy the forward zone file as a template, and edit it for reverse mappings (PTR records).

```bash
cd /var/named/

```

```bash
cp -v /var/named/forward.armour.local /var/named/reverse.armour.local

```

```bash
vim /var/named/reverse.armour.local

```

**Example Content:**

```
$TTL 1D
@ IN SOA ns1.armour.local. root.armour.local. (
    20250313   ; serial
    3600       ; refresh
    1800       ; retry
    604800     ; expire
    86400 )    ; minimum

@           IN NS   ns1.armour.local.
@           IN PTR  ns1.armour.local.

32          IN PTR  ns1.armour.local.
1           IN PTR  router.armour.local.
200         IN PTR  emp1.armour.local.
201         IN PTR  emp2.armour.local.

```

- **Note**: The reverse zone file uses PTR records to map IP addresses (last octet only, e.g., "32" for 192.168.1.32) back to hostnames. The SOA and NS records are similar to the forward zone for consistency. Removed erroneous symbol "ه" and fixed formatting (e.g., aligned comments). Only relevant IPs from the forward zone are included; expand as needed for full subnet coverage.

## Set Permissions

### Configure Zone File Permissions

Ensure the `named` group owns the reverse zone file and set appropriate permissions.

```bash
chgrp named /var/named/reverse.armour.local

```

```bash
chmod 640 /var/named/reverse.armour.local

```

- **Note**: Ownership by the `named` group allows the BIND daemon to read the file securely. The `640` permissions provide read/write access to the owner (typically root) and read-only access to the group, preventing unauthorized modifications.

## Restart BIND Service

### Restart the Named Service

Restart the `named` service to load the new reverse zone configuration.

```bash
systemctl restart named.service

```

- **Note**: Restarting applies changes to zones and configuration without downtime for ongoing queries. Use `systemctl status named` afterward to confirm the service is active and error-free.

## Check Configuration

### Verify Syntax

Check for syntax errors in the main BIND configuration file.

```bash
named-checkconf /etc/named.conf

```

- **Note**: This command validates the overall BIND setup, including included files like `/etc/named.rfc1912.zones`. No output indicates success; errors will specify the problematic line.

## Verify Reverse Zone

### Check the Reverse Zone File

Validate the syntax and structure of the `1.168.192.in-addr.arpa` reverse zone.

```bash
named-checkzone 1.168.192.in-addr.arpa /var/named/reverse.armour.local

```

**Expected Output:**

```
zone 1.168.192.in-addr.arpa/IN: loaded serial 20250313
OK

```

- **Note**: The `named-checkzone` tool simulates loading the zone to detect issues like invalid records or mismatched class/types. The serial number (20250313) should match the forward zone for synchronization.

## Firewall Configuration

### Allow DNS Traffic

Ensure the firewall permits DNS queries on port 53 (TCP and UDP). This is a repeat from forward zone setup but included for completeness.

### For UFW (Uncomplicated Firewall)

```bash
ufw allow 53/tcp

```

```bash
ufw allow 53/udp

```

### For Firewalld

```bash
firewall-cmd --add-port=53/tcp --permanent

```

```bash
firewall-cmd --add-port=53/udp --permanent

```

```bash
firewall-cmd --reload

```

- **Note**: DNS reverse queries use the same ports as forward ones. The `-permanent` flag in Firewalld saves rules across reboots; `-reload` applies them immediately. Verify with `ufw status` or `firewall-cmd --list-ports`.

## Test Reverse Resolution

### Verify Reverse DNS Lookups

Use `dig` to test reverse resolution for specific IPs, querying the local DNS server (assumed at 192.168.1.32).

```bash
dig -x 192.168.1.32

```

```bash
dig -x 192.168.1.200

```

**Example Output (for 192.168.1.100, adapted for illustration):**

```
; <<>> DiG 9.11.13-RedHat-9.11.13-5.el7 <<>> -x 192.168.1.100
;; ANSWER SECTION:
100.1.168.192.in-addr.arpa. 86400 IN PTR dev.armour.local.

```

- **Note**: The `x` flag performs a reverse lookup (PTR query). Specify the full IP; the response should show the hostname in the ANSWER SECTION. Test against the server's IP (e.g., @192.168.1.32) for authoritative responses: `dig -x 192.168.1.32 @192.168.1.32`. Corrected typo in second command from "dig x" to "dig -x". Expand tests to other IPs like 192.168.1.1 or 192.168.1.201. If no response, check logs with `journalctl -u named`.
