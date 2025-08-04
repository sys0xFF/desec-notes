# Virtual Lab - INFRA - Lab 05

**Lab ID:** 4d290b3dff33d7717fba1c0fb84922762fe047a6

**Objective:** Perform reverse DNS research on businesscorp.com.br and identify discovered subdomains

---

## Methodology

### Step 1: Enhanced Reverse DNS Discovery
We'll use the Enhanced Reverse DNS script from INFRA part 3, which automatically discovers IPs, identifies network blocks, and performs reverse DNS scanning.

*Reference: [Info-Gathering-INFRA.part3.md - Enhanced Reverse DNS Script](../../../Info-Gathering-INFRA.part3.md#2-script-for-reverse-dns-research)*

```bash
./advanced_reverse_dns.sh businesscorp.com.br
```

This script will:
1. Discover initial IP addresses from the domain
2. Identify network blocks through WHOIS queries
3. Perform reverse DNS scanning on the discovered ranges

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
TEMP_FILE="/tmp/discovered_ips_$$"

echo "[+] Advanced Reverse DNS Discovery for: $DOMAIN"
echo "================================"

# Step 1: Discover some IPs from the domain
echo "[+] Step 1: Discovering initial IP addresses..."
host $DOMAIN 2>/dev/null | grep "has address" | awk '{print $4}' > $TEMP_FILE

# Also try common subdomains
for sub in www mail ns1 ns2 ftp; do
    host $sub.$DOMAIN 2>/dev/null | grep "has address" | awk '{print $4}' >> $TEMP_FILE
done

# Remove duplicates
sort -u $TEMP_FILE > ${TEMP_FILE}.unique
mv ${TEMP_FILE}.unique $TEMP_FILE

if [ ! -s $TEMP_FILE ]; then
    echo "[-] No IP addresses found for $DOMAIN"
    rm -f $TEMP_FILE
    exit 1
fi

echo "[+] Found initial IPs:"
cat $TEMP_FILE

# Step 2: Get network blocks for discovered IPs
echo ""
echo "[+] Step 2: Discovering network blocks..."
for ip in $(cat $TEMP_FILE); do
    echo "[+] Checking network block for: $ip"
    netblock=$(whois $ip 2>/dev/null | grep -E "inetnum:|NetRange:" | head -1)
    if [ ! -z "$netblock" ]; then
        echo "    $netblock"
    fi
done

echo ""
echo "[+] Step 3: Performing reverse DNS scan..."
# Extract base IP and range from first IP for demonstration
first_ip=$(head -1 $TEMP_FILE)
base_ip=$(echo $first_ip | cut -d. -f1-3)

# Simple range scan around discovered IPs
echo "[+] Scanning network range around discovered IPs..."
for ip in $(cat $TEMP_FILE); do
    last_octet=$(echo $ip | cut -d. -f4)
    start_range=$((last_octet - 5))
    end_range=$((last_octet + 5))
    
    # Ensure range is within valid bounds
    if [ $start_range -lt 1 ]; then start_range=1; fi
    if [ $end_range -gt 254 ]; then end_range=254; fi
    
    echo "[+] Scanning range: $base_ip.$start_range-$end_range"
    for scan_ip in $(seq $start_range $end_range); do
        full_ip="$base_ip.$scan_ip"
        result=$(host $full_ip 2>/dev/null | grep "domain name pointer" | awk '{print $5}')
        
        if [ ! -z "$result" ]; then
            echo "    $full_ip -> $result"
        fi
    done
    break  # Only scan around first IP to avoid too much output
done

echo ""

# Cleanup
rm -f $TEMP_FILE

echo "================================"
echo "[+] Discovery complete"
```

---

## Resolution

### Step 1: Enhanced Reverse DNS Discovery
Using the Enhanced Reverse DNS Script from INFRA part 3:

```bash
./advanced_reverse_dns.sh businesscorp.com.br
```

**Results:**
```
[+] Advanced Reverse DNS Discovery for: businesscorp.com.br
================================
[+] Step 1: Discovering initial IP addresses...
[+] Found initial IPs:
37.59.174.225
37.59.174.226
37.59.174.227

[+] Step 2: Discovering network blocks...
[+] Checking network block for: 37.59.174.225
    inetnum:        37.59.174.224 - 37.59.174.239
[+] Checking network block for: 37.59.174.226
    inetnum:        37.59.174.224 - 37.59.174.239
[+] Checking network block for: 37.59.174.227
    inetnum:        37.59.174.224 - 37.59.174.239

[+] Step 3: Performing reverse DNS scan...
[+] Scanning network range around discovered IPs...
[+] Scanning range: 37.59.174.220-230
    37.59.174.224 -> ip224.ip-37-59-174.eu.
    37.59.174.225 -> ip225.ip-37-59-174.eu.
    37.59.174.226 -> ip226.ip-37-59-174.eu.
    37.59.174.227 -> ip227.ip-37-59-174.eu.
    37.59.174.228 -> ip228.ip-37-59-174.eu.
    37.59.174.229 -> rh.businesscorp.com.br.
    37.59.174.230 -> piloto.businesscorp.com.br.

================================
[+] Discovery complete
```

### Step 2: Subdomain Extraction
From the reverse DNS results, the following subdomains were discovered:
- rh.businesscorp.com.br (37.59.174.229)
- piloto.businesscorp.com.br (37.59.174.230)

---

## Answer

**rh.businesscorp.com.br,piloto.businesscorp.com.br**

---

Reverse DNS scanning of the OVH network block (37.59.174.224-239) revealed two subdomains through PTR record resolution: "rh" (recursos humanos) and "piloto" (pilot/testing environment), demonstrating how reverse DNS can uncover subdomains that might not be found through traditional brute force methods.
