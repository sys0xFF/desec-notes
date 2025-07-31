# Information Gathering – Infrastructure (Part 3)

> **Focus:** DNS reconnaissance techniques including direct and reverse DNS brute force attacks for comprehensive subdomain discovery.

---

## Table of Contents

1. [Script for Direct DNS Research](#1-script-for-direct-dns-research)
2. [Script for Reverse DNS Research](#2-script-for-reverse-dns-research)

---

## 1 – Script for Direct DNS Research

When Zone Transfer techniques unfortunately don't work, one alternative to obtain subdomain data is **DNS Brute Force**.

### Understanding DNS Response Patterns

First, we need to understand the standard patterns when DNS queries succeed or fail.

**Successful Query:**
```bash
host rh.businesscorp.com.br
```
```
rh.businesscorp.com.br has address 37.59.174.229
```

**Failed Query (Error Pattern):**
```bash
host qualquercoisa.businesscorp.com.br
```
```
Host qualquercoisa.businesscorp.com.br not found: 3(NXDOMAIN)
```

### Creating a Subdomain Wordlist

Create a file with common subdomain names:

```bash
nano lista.txt
```

**Example Subdomains:**
```
firewall
monitoramento
intranet
ns1
ns01
mail
webmail
rh
sistema
homologacao
admin
logs
devs
documentos
server
api
www
ftp
portal
vpn
secure
test
staging
dev
prod
database
db
backup
git
jenkins
confluence
jira
nagios
zabbix
grafana
splunk
elk
kibana
prometheus
```

### DNS Brute Force Script

```bash
#!/bin/bash

# DNS Brute Force Script
# Usage: ./dns_bruteforce.sh domain.com

if [ $# -ne 1 ]; then
    echo "Usage: $0 <domain>"
    echo "Example: $0 businesscorp.com.br"
    exit 1
fi

DOMAIN="$1"
WORDLIST="lista.txt"

if [ ! -f "$WORDLIST" ]; then
    echo "[-] Wordlist file '$WORDLIST' not found!"
    exit 1
fi

echo "[+] Starting DNS brute force for: $DOMAIN"
echo "[+] Using wordlist: $WORDLIST"
echo "[+] Found subdomains:"
echo "================================"

for palavra in $(cat $WORDLIST); do
    result=$(host $palavra.$DOMAIN 2>/dev/null | grep -v "NXDOMAIN")
    if [ ! -z "$result" ]; then
        echo "$result"
    fi
done

echo "================================"
echo "[+] DNS brute force complete"
```

### Usage:

```bash
chmod +x dns_bruteforce.sh
./dns_bruteforce.sh businesscorp.com.br
```

---

## 2 – Script for Reverse DNS Research

Another technique we can use is **Reverse DNS Research**. In the previous section we used Direct DNS Research, and now we'll see how to perform Reverse DNS Research. This is valuable because reverse DNS lookups (searching by IP to resolve PTR records) can often reveal subdomains that wouldn't be found through other methods.

### Understanding the Process

Using our previous brute force script, it returns:

```bash
./dns_bruteforce.sh businesscorp.com.br
```

**Example Output:**
```
intranet.businesscorp.com.br has address 37.59.174.228
ns1.businesscorp.com.br has address 37.59.174.225
mail.businesscorp.com.br has address 37.59.174.227
rh.businesscorp.com.br has address 37.59.174.229
```

### Discovering the Network Block

Next, we perform a WHOIS query on one of these IPs to discover the network block:

```bash
whois 37.59.174.225
```

**Key Information:**
```
inetnum: 37.59.174.224 - 37.59.174.239
```

### Reverse DNS Scanning Script

Now we can create a script to scan this network block and find additional subdomains:

```bash
#!/bin/bash

# Reverse DNS Scanner Script
# Usage: ./reverse_dns.sh <base_ip> <start_range> <end_range>

if [ $# -ne 3 ]; then
    echo "Usage: $0 <base_ip> <start_range> <end_range>"
    echo "Example: $0 37.59.174 224 239"
    exit 1
fi

BASE_IP="$1"
START_RANGE="$2"
END_RANGE="$3"

echo "[+] Starting reverse DNS scan for: $BASE_IP.$START_RANGE-$END_RANGE"
echo "[+] Found PTR records:"
echo "================================"

for ip in $(seq $START_RANGE $END_RANGE); do
    full_ip="$BASE_IP.$ip"
    result=$(host -t ptr $full_ip 2>/dev/null | grep -v "NXDOMAIN" | grep -v "$BASE_IP" | cut -d " " -f 5)
    
    if [ ! -z "$result" ]; then
        echo "$full_ip -> $result"
    fi
done

echo "================================"
echo "[+] Reverse DNS scan complete"
```

### Enhanced Reverse DNS Script

Here's an improved version that combines both approaches:

```bash
#!/bin/bash

# Advanced Reverse DNS Discovery Script
# Usage: ./advanced_reverse_dns.sh <domain>

if [ $# -ne 1 ]; then
    echo "Usage: $0 <domain>"
    echo "Example: $0 businesscorp.com.br"
    exit 1
fi

DOMAIN="$1"
TEMP_FILE="/tmp/discovered_ips.txt"

echo "[+] Advanced Reverse DNS Discovery for: $DOMAIN"
echo "================================"

# Step 1: Discover some IPs from the domain
echo "[+] Step 1: Discovering initial IP addresses..."
host $DOMAIN | grep "has address" | awk '{print $4}' > $TEMP_FILE

# Also try common subdomains
for sub in www mail ns1 ns2 ftp; do
    host $sub.$DOMAIN 2>/dev/null | grep "has address" | awk '{print $4}' >> $TEMP_FILE
done

# Remove duplicates
sort -u $TEMP_FILE > ${TEMP_FILE}.unique
mv ${TEMP_FILE}.unique $TEMP_FILE

if [ ! -s $TEMP_FILE ]; then
    echo "[-] No IP addresses found for $DOMAIN"
    exit 1
fi

echo "[+] Found initial IPs:"
cat $TEMP_FILE

# Step 2: Get network blocks for discovered IPs
echo "[+] Step 2: Discovering network blocks..."
for ip in $(cat $TEMP_FILE); do
    echo "[+] Checking network block for: $ip"
    whois $ip | grep -E "inetnum:|NetRange:" | head -1
done

echo ""
echo "[+] Step 3: Manual reverse DNS scanning"
echo "[+] Use the network ranges above with the reverse DNS script"
echo "[+] Example: ./reverse_dns.sh 37.59.174 224 239"

# Cleanup
rm -f $TEMP_FILE

echo "================================"
echo "[+] Discovery complete"
```

### Why Reverse DNS is Valuable

This technique serves as an alternative to other DNS reconnaissance methods because:

1. **Hidden Subdomains:** May reveal subdomains not found through brute force
2. **Infrastructure Mapping:** Shows the actual network layout
3. **Comprehensive Coverage:** Scans entire network blocks systematically
4. **PTR Record Discovery:** Finds reverse DNS entries that might be missed

### Practical Application

By taking any IP from the target host, you can:
1. Extract the netblock using WHOIS
2. Scan the entire netblock range
3. Discover subdomains that wouldn't be found through brute force or other alternative methods

### Usage Examples:

**Basic Reverse DNS:**
```bash
./reverse_dns.sh 37.59.174 224 239
```

**Advanced Discovery:**
```bash
./advanced_reverse_dns.sh businesscorp.com.br
```
