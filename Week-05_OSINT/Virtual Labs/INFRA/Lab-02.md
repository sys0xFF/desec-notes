# Virtual Lab - INFRA - Lab 02

**Lab ID:** cec4ca31572cc092f60aacb6e3cb285fe463687a

**Objective:** Identify the AS (Autonomous System) number of the provider used by businesscorp.com.br

---

## Methodology

### Step 1: Domain to IP Resolution
First, we need to resolve the domain to get its IP address.

*Reference: [Info-Gathering-INFRA.part2.md - Infrastructure Mapping - IP Research](../../../Info-Gathering-INFRA.part2.md#4-infrastructure-mapping---ip-research)*

```bash
host businesscorp.com.br
```

### Step 2: WHOIS Query for IP Information
Once we have the IP address, we perform a WHOIS query to get detailed information about the IP, including the ASN.

*Reference: [Info-Gathering-INFRA.part1.md - IP Address WHOIS Analysis](../../../Info-Gathering-INFRA.part1.md#6-ip-address-whois-analysis)*

```bash
whois [IP_ADDRESS]
```

### Step 3: Filter for ASN Information
To specifically find the Autonomous System Number, we can filter the WHOIS output.

*Reference: [Info-Gathering-INFRA.part2.md - Infrastructure Mapping - IP Research](../../../Info-Gathering-INFRA.part2.md#4-infrastructure-mapping---ip-research)*

```bash
whois [IP_ADDRESS] | grep -i "aut-num\|origin\|as"
```

---

## Resolution

### Step 1: Domain Resolution
```bash
host businesscorp.com.br
```

**Result:**
```
businesscorp.com.br has address 37.59.174.225
```

### Step 2: WHOIS Analysis
```bash
whois 37.59.174.225
```

**Key Information from WHOIS:**
```
route:           37.59.0.0/16
descr:           OVH ISP
descr:           Paris, France
origin:          AS16276
mnt-by:          OVH-MNT
created:         2012-01-25T17:04:21Z
last-modified:   2012-01-25T17:04:21Z
source:          RIPE
```

### Step 3: ASN Identification
From the WHOIS output, we can identify:
- **Origin:** AS16276
- **Provider:** OVH ISP
- **Location:** Paris, France

---

## Answer

**AS16276**

---

This AS number represents OVH, a major European cloud and hosting provider, which explains why businesscorp.com.br is hosted on their infrastructure despite being a Brazilian domain.
