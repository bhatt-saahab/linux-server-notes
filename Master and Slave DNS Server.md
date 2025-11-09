# Multiple Zone Configuration

## Setup for infosec.local

### Edit DNS Zones Configuration

Edit the `/etc/named.rfc1912.zones` file to add the `infosec.local` forward zone definition.

```bash
vim /etc/named.rfc1912.zones

```

**Example Content (Addition):**

```
zone "infosec.local" IN {
    type master;
    file "forward.infosec.local";
    allow-update { none; };
};

```

- **Note**: Append this zone block to the existing `/etc/named.rfc1912.zones` file (which already includes zones like `armour.local`). This declares `infosec.local` as a master zone, pointing to its dedicated forward zone file. The `allow-update { none; };` prevents dynamic updates for security.

### Create Forward Zone File

### Copy the Existing Forward Zone File

Use the `armour.local` forward zone as a template to create the new one for `infosec.local`.

```bash
cp -v /var/named/forward.armour.local /var/named/forward.infosec.local

```

### Edit the New Zone File

Modify the copied file to customize records for `infosec.local`.

```bash
vim /var/named/forward.infosec.local

```

**Example Content:**

```
$TTL 1D
$ORIGIN infosec.local.

@ IN SOA ns1.armour.local. root.armour.local. (
    20250324 ; serial
    1D        ; refresh
    1H        ; retry
    1W        ; expire
    3H )      ; minimum

@           IN NS   ns1.armour.local.
@           IN A    192.168.1.40
www         IN CNAME infosec.local.

; Mail exchange
@           IN MX   10 mail.armour.local.

; Text record for SPF
@           IN TXT  "v=spf1 mx a ~all"

; Additional Services
ftp         IN A    192.168.1.112
dev         IN A    192.168.1.111
fileserver  IN A    192.168.1.110
db          IN A    192.168.1.120
test        IN A    192.168.1.130
vpn         IN A    192.168.1.140
git         IN A    192.168.1.150
webapp      IN A    192.168.1.160
logs        IN A    192.168.1.170

```

- **Note**: The `$ORIGIN infosec.local.` directive sets the base domain, allowing relative names (e.g., `@` for `infosec.local.`). Updated serial to `20250324` for versioning. Corrected "ww" to "www". Removed duplicate "test" entry. The A record for the domain root is set to `192.168.1.40`. MX points to the shared mail server from `armour.local`. SPF TXT enhances email security.

### Set Permissions

### Configure Zone File Permissions

Assign group ownership and set read/write permissions for the new zone file.

```bash
chgrp named /var/named/forward.infosec.local

```

```bash
chmod 640 /var/named/forward.infosec.local

```

- **Note**: The `named` group ensures BIND can read the file. `640` permissions allow owner (root) full access and group read-only, securing against unauthorized changes.

### Validate Configuration

### Check Syntax

Validate the main configuration and zones files for errors.

```bash
named-checkconf /etc/named.conf

```

```bash
named-checkconf /etc/named.rfc1912.zones

```

### Verify the Forward Zone

Check the syntax and load the `infosec.local` zone file.

```bash
named-checkzone infosec.local /var/named/forward.infosec.local

```

**Expected Output:**

```
zone infosec.local/IN: loaded serial 20250324
OK

```

- **Note**: `named-checkconf` scans for syntax issues across configs. `named-checkzone` simulates loading the zone, confirming record validity. Corrected garbled command from original ("1 1.infosec.local named-checkzone"). No output from checks indicates success.

### Restart BIND

### Restart the Named Service

Reload the service to apply the new zone.

```bash
systemctl restart named.service

```

- **Note**: This restarts BIND gracefully, incorporating the new `infosec.local` zone without disrupting other queries. Verify status with `systemctl status named.service`.

### Test the Setup

### Verify Forward DNS Resolution

Query the new zone using `dig` for domain, MX, and TXT records.

```bash
dig infosec.local

```

```bash
dig mx infosec.local

```

```bash
dig txt infosec.local

```

**Example Output (for infosec.local query):**

```
; <<>> DiG 9.11.13-RedHat-9.11.13-5.el7 <<>> infosec.local
;; ANSWER SECTION:
infosec.local.  86400  IN A  192.168.1.40

```

- **Note**: These `dig` commands test resolution (omitting `@server` assumes local resolver uses the BIND server). Expect A record `192.168.1.40` for the domain (corrected from example's 192.168.1.200 mismatch). MX should return `mail.armour.local`, TXT the SPF string. Check `ANSWER SECTION` for authoritative responses.

## Setup for ai.local

### Edit DNS Zones Configuration

Edit the `/etc/named.rfc1912.zones` file to add the `ai.local` forward zone definition.

```bash
vim /etc/named.rfc1912.zones

```

**Example Content (Addition):**

```
zone "ai.local" IN {
    type master;
    file "forward.ai.local";
    allow-update { none; };
};

```

- **Note**: Append this block to the file, now including `infosec.local` and prior zones. This sets up `ai.local` as another master zone with its own file.

### Create Forward Zone File

### Copy the infosec.local Zone File

Use the `infosec.local` forward zone as a template for `ai.local`.

```bash
cp -v /var/named/forward.infosec.local /var/named/forward.ai.local

```

### Edit the New Zone File

Customize the copied file for `ai.local`-specific records.

```bash
vim /var/named/forward.ai.local

```

**Example Content:**

```
$TTL 1D
$ORIGIN ai.local.

@ IN SOA ns1.armour.local. root.armour.local. (
    20250325 ; serial
    1D        ; refresh
    1H        ; retry
    1W        ; expire
    3H )      ; minimum

@           IN NS   ns1.armour.local.
@           IN A    192.168.1.201
www         IN CNAME ai.local.

; Mail exchange
@           IN MX   10 mail.armour.local.

; Text record for SPF
@           IN TXT  "v=spf1 mx a ~all"

; Additional Services
ftp         IN A    192.168.1.112
dev         IN A    192.168.1.111
fileserver  IN A    192.168.1.110
db          IN A    192.168.1.120
webapp      IN A    192.168.1.160
logs        IN A    192.168.1.170

```

- **Note**: `$ORIGIN ai.local.` simplifies record naming. Updated serial to `20250325`. Corrected "ww" to "www" and removed "ล" symbol. Removed duplicates (e.g., "webapp"). A record set to `192.168.1.201` for the domain root. Retained shared services like MX and SPF; adjusted services list to match provided data (omitted test, vpn, git).

### Set Permissions

### Configure Zone File Permissions

Assign group ownership and set read/write permissions for the new zone file.

```bash
chgrp named /var/named/forward.ai.local

```

```bash
chmod 640 /var/named/forward.ai.local

```

- **Note**: Consistent with previous zones, this secures the file for BIND access while restricting modifications.

### Validate Configuration

### Check Syntax

Validate the main configuration and zones files for errors.

```bash
named-checkconf /etc/named.conf

```

```bash
named-checkconf /etc/named.rfc1912.zones

```

### Verify the Forward Zone

Check the syntax and load the `ai.local` zone file.

```bash
named-checkzone ai.local /var/named/forward.ai.local

```

**Expected Output:**

```
zone ai.local/IN: loaded serial 20250325
OK

```

- **Note**: These validation steps ensure the multi-zone setup remains error-free. Serial reflects the zone's update date.

### Restart BIND

### Restart the Named Service

Reload the service to apply the new `ai.local` zone.

```bash
systemctl restart named.service

```

- **Note**: Restart incorporates both new zones; monitor logs for issues with `journalctl -u named`.

### Test the Setup

### Verify Forward DNS Resolution

Query the new zone using `dig` for domain, MX, and TXT records.

```bash
dig ai.local

```

```bash
dig mx ai.local

```

```bash
dig txt ai.local

```

**Example Output (for ai.local query):**

```
; <<>> DiG 9.11.13-RedHat-9.11.13-5.el7 <<>> ai.local
;; ANSWER SECTION:
ai.local.  86400  IN A  192.168.1.201

```

- **Note**: Tests confirm resolution for `ai.local`. A record should return `192.168.1.201`. MX and TXT match the shared setup. Corrected output formatting (e.g., " <<<>>> " to standard DiG style).

## Firewall Configuration

### Allow DNS Traffic

Configure the firewall to permit DNS on port 53 (TCP/UDP), ensuring multi-zone queries work.

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

- **Note**: Repeated from prior setups for completeness in multi-zone context. Rules apply globally to the BIND server. Verify with `ufw status verbose` or `firewall-cmd --list-all`.

## Explanation of $ORIGIN

- **The $ORIGIN Directive**:

In a DNS zone file, `$ORIGIN` sets the base domain name for relative records, appending it to unqualified names (e.g., "www" becomes "www.ai.local."). This reduces repetition and errors.

- **Example Usage**:

From the `forward.ai.local` zone file snippet:

```
$ORIGIN ai.local.
@ IN SOA ns1.armour.local. root.armour.local. (
    20250325 ; serial
    1D        ; refresh
    1H        ; retry
    1W        ; expire
    3H )      ; minimum

@       IN NS  ns1.armour.local.
@       IN A   192.168.1.201
www     IN CNAME ai.local.
ftp     IN A   192.168.1.112

```

- `@` refers to the current `$ORIGIN` (ai.local.).
- `www IN CNAME ai.local.` resolves to `www.ai.local.` aliasing `ai.local.`.
- `ftp IN A 192.168.1.112` becomes `ftp.ai.local.`.
Removed erroneous "อ" symbol and fixed formatting.
- **Without $ORIGIN**:

Full FQDNs would be required:

```
www.ai.local.     IN CNAME ai.local.
ftp.ai.local.     IN A     192.168.1.112

```

- **Purpose**:
- Avoids repeating full domain names.
- Simplifies zone file management.
- Ensures consistency and minimizes typos in multi-zone setups.

# Master and Slave DNS Server

## Server Details

| Role | IP Address | Domain Name | Description |
| --- | --- | --- | --- |
| Master DNS | 192.168.1.32 | armour.local | Primary DNS server |
| Slave DNS | 192.168.1.33 | armour.local | Secondary DNS server |
- **Note**: This setup uses the `armour.local` domain as the primary example, with additional zones (`infosec.local` and `ai.local`) configured on both servers for multi-zone support. The Master holds authoritative records, while the Slave replicates them via zone transfers. All configurations assume a RHEL/CentOS-like system using `yum` for package management and Firewalld for firewall.

## Master DNS Configuration

### Step 1: Set the Hostname

Set the hostname for the Master DNS server to `ns1.armour.local`.

```bash
hostnamectl set-hostname ns1.armour.local

```

- **Note**: The `hostnamectl` command updates the system's hostname persistently. Verify with `hostnamectl status`. This FQDN should match entries in zone files for consistency.

### Step 2: Install BIND

Install the BIND package required for DNS services.

```bash
yum install bind

```

- **Note**: This installs the BIND daemon (`named`) and utilities. On newer RHEL versions (e.g., 8+), use `dnf` instead of `yum` if applicable. After installation, ensure the package is up-to-date with `yum update bind`.

### Step 3: Configure the DNS Zones

Edit the zone configuration file to define forward zones with transfer permissions to the Slave.

```bash
vim /etc/named.rfc1912.zones

```

**Example Content:**

```
zone "armour.local" IN {
    type master;
    file "forward.armour.local";
    allow-update { none; };
    allow-transfer { 192.168.1.33; };
};

zone "infosec.local" IN {
    type master;
    file "forward.infosec.local";
    allow-update { none; };
    allow-transfer { 192.168.1.33; };
};

zone "ai.local" IN {
    type master;
    file "forward.ai.local";
    allow-update { none; };
    allow-transfer { 192.168.1.33; };
};

```

- **Note**: Append these zones to the existing `/etc/named.rfc1912.zones` file (retaining standard zones like `localhost`). `allow-transfer` restricts zone transfers to the Slave IP only, enhancing security. Removed extraneous line numbers and formatting issues from original.

### Step 4: Create the Zone Files

### Forward Zone for armour.local

Create and edit the forward lookup zone file for `armour.local`.

```bash
vim /var/named/forward.armour.local

```

**Example Content:**

```
$TTL 1D
@ IN SOA ns1.armour.local. root.armour.local. (
    20251021 ; serial
    3600     ; refresh
    1800     ; retry
    604800   ; expire
    86400 )  ; minimum

@           IN NS   ns1.armour.local.
@           IN NS   ns2.armour.local.
ns1         IN A    192.168.1.32
ns2         IN A    192.168.1.33
@           IN A    192.168.1.32
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

- **Note**: Updated serial to `20251021` (current date format: YYYYMMDD). Added NS record for `ns2.armour.local` (Slave). Removed erroneous "ه" symbol, duplicates (e.g., TXT record), and fixed formatting. `@` represents the zone origin (`armour.local.`).

### Forward Zone for infosec.local

Create and edit the forward lookup zone file for `infosec.local`.

```bash
vim /var/named/forward.infosec.local

```

**Example Content:**

```
$TTL 1D
$ORIGIN infosec.local.

@ IN SOA ns1.armour.local. root.armour.local. (
    20251021 ; serial
    1D        ; refresh
    1H        ; retry
    1W        ; expire
    3H )      ; minimum

@           IN NS   ns1.armour.local.
@           IN NS   ns2.armour.local.
@           IN A    192.168.1.40
www         IN CNAME infosec.local.
; Mail exchange
@           IN MX   10 mail.armour.local.
; Text record for SPF
@           IN TXT  "v=spf1 mx a ~all"
; Additional Services
ftp         IN A    192.168.1.112
dev         IN A    192.168.1.111
fileserver  IN A    192.168.1.110
db          IN A    192.168.1.120
test        IN A    192.168.1.130
vpn         IN A    192.168.1.140
git         IN A    192.168.1.150
webapp      IN A    192.168.1.160
logs        IN A    192.168.1.170

```

- **Note**: `$ORIGIN` simplifies relative naming. Updated serial to `20251021`. Added NS for `ns2.armour.local`. Fixed line numbering, removed duplicates, and ensured consistent structure matching prior zones.

### Forward Zone for ai.local

Create and edit the forward lookup zone file for `ai.local`.

```bash
vim /var/named/forward.ai.local

```

**Example Content:**

```
$TTL 1D
$ORIGIN ai.local.

@ IN SOA ns1.armour.local. root.armour.local. (
    20251021 ; serial
    1D        ; refresh
    1H        ; retry
    1W        ; expire
    3H )      ; minimum

@           IN NS   ns1.armour.local.
@           IN NS   ns2.armour.local.
@           IN A    192.168.1.201
www         IN CNAME ai.local.
; Mail exchange
@           IN MX   10 mail.armour.local.
; Text record for SPF
@           IN TXT  "v=spf1 mx a ~all"
; Additional Services
ftp         IN A    192.168.1.112
dev         IN A    192.168.1.111
fileserver  IN A    192.168.1.110
db          IN A    192.168.1.120
webapp      IN A    192.168.1.160
logs        IN A    192.168.1.170

```

- **Note**: Updated serial to `20251021`. Added NS for `ns2.armour.local`. Removed extraneous lines and fixed formatting inconsistencies (e.g., missing SOA structure). Omitted unused services (e.g., test, vpn, git) to match provided data.

### Step 5: Configure Firewall Rules (Firewalld)

Allow DNS traffic on TCP and UDP port 53 for queries and transfers.

```bash
firewall-cmd --permanent --add-port=53/tcp

```

```bash
firewall-cmd --permanent --add-port=53/udp

```

```bash
firewall-cmd --reload

```

- **Note**: The `-permanent` flag persists rules across reboots. TCP handles zone transfers; UDP for standard queries. Verify with `firewall-cmd --list-ports`.

### Step 6: Validate Configuration

Check syntax for configuration and zone files.

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
named-checkzone armour.local /var/named/forward.armour.local

```

```bash
named-checkzone infosec.local /var/named/forward.infosec.local

```

```bash
named-checkzone ai.local /var/named/forward.ai.local

```

- **Note**: `named-checkconf` (without args) checks the main config and includes. `named-checkzone` validates each zone's syntax and loads it. Expected output: "loaded serial 20251021" and "OK". Errors will pinpoint issues.

### Step 7: Start and Enable BIND

Start the BIND service and enable it on boot.

```bash
systemctl restart named.service

```

```bash
systemctl enable named.service

```

- **Note**: `restart` applies changes; `enable` ensures auto-start. Check status with `systemctl status named.service`. Logs via `journalctl -u named`.

## Slave DNS Configuration

### Step 1: Set the Hostname

Set the hostname for the Slave DNS server to `ns2.armour.local`.

```bash
hostnamectl set-hostname ns2.armour.local

```

- **Note**: Matches the NS record in Master zones. Verify with `hostnamectl status`.

### Step 2: Install BIND

Install the BIND package on the Slave server.

```bash
yum install bind

```

- **Note**: Same as Master; use `dnf` if on newer systems. This provides the slave functionality via zone transfer support.

### Step 3: Configure /etc/hosts

Edit the hosts file for local resolution of the Slave's FQDN.

```bash
vim /etc/hosts

```

**Example Content:**

```
127.0.0.1   localhost ns2 ns2.armour.local
::1         localhost localhost6 ns2 ns2.armour.local
192.168.1.33 ns2 ns2.armour.local

```

- **Note**: Maps localhost and the server's IP to its hostname. Ensures self-resolution without DNS queries during boot.

### Step 4: Configure /etc/named.conf

Edit the main BIND configuration file for the Slave (authoritative for replicated zones, with caching enabled).

```bash
vim /etc/named.conf

```

**Example Content:**

```
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named (8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind/sample/ for example named configuration files.

options {
    listen-on port 53 { 127.0.0.1; 192.168.1.33; };
    listen-on-v6 port 53 { ::1; };
    directory "/var/named";
    dump-file "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    recursing-file "/var/named/data/named.recursing";
    secroots-file "/var/named/data/named.secroots";
    allow-query { localhost; 192.168.1.0/24; };

    /*
     If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
     If you are building a RECURSIVE (caching) DNS server, you need to enable recursion.
     - If your recursive DNS server has a public IP address, you MUST enable access control to limit queries to your legitimate users. Failing to do so will cause your server to become part of large scale DNS amplification attacks. Implementing BCP38 within your network would greatly reduce such attack surface
    */
    recursion yes;
    dnssec-validation yes;

    managed-keys-directory "/var/named/dynamic";
    geoip-directory "/usr/share/GeoIP";

    pid-file "/run/named/named.pid";
    session-keyfile "/run/named/session.key";

    /* <https://fedoraproject.org/wiki/Changes/CryptoPolicy> */
    include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
    channel default_debug {
        file "data/named.run";
        severity dynamic;
    };
};

zone "." IN {
    type hint;
    file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

```

- **Note**: Configured for recursive caching (`recursion yes;`) while serving as slave for specific zones. `allow-query` limits to local network. Fixed formatting (e.g., misplaced lines like dump-file). For pure authoritative, set `recursion no;`, but caching is useful for non-local queries.

### Step 5: Configure the Zones on the Slave

Edit the zone configuration file to define slave zones replicating from the Master.

```bash
vim /etc/named.rfc1912.zones

```

**Example Content:**

```
zone "armour.local" IN {
    type slave;
    masters { 192.168.1.32; };
    file "slaves/forward.armour.local";
};

zone "infosec.local" IN {
    type slave;
    masters { 192.168.1.32; };
    file "slaves/forward.infosec.local";
};

zone "ai.local" IN {
    type slave;
    masters { 192.168.1.32; };
    file "slaves/forward.ai.local";
};

```

- **Note**: Append to existing file. `type slave` enables AXFR/IXFR transfers from Master. `masters` specifies the source IP. Files are auto-created in `/var/named/slaves/` by BIND. Retain standard zones.

### Step 6: Configure Firewall Rules (Firewalld)

Allow DNS traffic on TCP and UDP port 53 for queries and incoming transfers.

```bash
firewall-cmd --permanent --add-port=53/tcp

```

```bash
firewall-cmd --permanent --add-port=53/udp

```

```bash
firewall-cmd --reload

```

- **Note**: Identical to Master; TCP for transfers from Master. Verify rules with `firewall-cmd --list-ports`.

### Step 7: Validate Configuration

Check syntax for configuration files.

```bash
named-checkconf

```

```bash
named-checkconf /etc/named.conf

```

```bash
named-checkconf /etc/named.rfc1912.zones

```

- **Note**: Validates overall setup. Slave zones don't need `named-checkzone` pre-start, as files are transferred. No output = success.

### Step 8: Start and Enable BIND

Start the BIND service and enable it on boot.

```bash
systemctl restart named.service

```

```bash
systemctl enable named.service

```

- **Note**: On first start, Slave will transfer zones from Master (check logs for "Transfer started"). Monitor with `systemctl status named.service`.

### Step 9: Test DNS Resolution

Verify resolution from the Slave server for all zones.

```bash
dig armour.local @192.168.1.33

```

```bash
dig infosec.local @192.168.1.33

```

```bash
dig ai.local @192.168.1.33

```

- **Note**: `@192.168.1.33` queries the Slave directly. Expect authoritative answers (AA flag) with records matching Master. Test NS records to confirm Slave listing.

## Troubleshooting

### Check DNS Service Status

Monitor the BIND service status.

```bash
systemctl status named.service

```

- **Note**: Shows if active/running; use `f` for full logs. Errors like transfer failures indicate firewall or `allow-transfer` issues.

### Check Listening Ports

Verify BIND is listening on port 53.

```bash
netstat -nltup | grep named

```

- **Note**: Should show UDP/TCP on 0.0.0.0:53 or specific IPs. If not, check SELinux (`setsebool -P named_port_t 1`) or config errors.

### Test External DNS Resolution

Verify recursive resolution (if enabled) for external domains.

```bash
dig google.com

```

```bash
dig google.com @192.168.1.33

```

- **Note**: Tests caching. If failing, check `recursion yes;` and upstream hints. For authoritative only, expect SERVFAIL on external queries.

## Conclusion

This completes the setup of a Master and Slave DNS server using BIND. Ensure that DNS resolution is working correctly across both servers and that the firewall rules are properly configured. Regularly increment serial numbers on Master for updates, and monitor transfer logs for sync issues. For production, consider DNSSEC signing and TSIG keys for secure transfers.
