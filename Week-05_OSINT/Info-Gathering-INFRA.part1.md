# Information Gathering – Infrastructure

> **Focus:** Understanding internet infrastructure, IP allocation, domain registration, and using WHOIS for reconnaissance to map target network infrastructure.

---

## Table of Contents

1. [IANA (Internet Assigned Numbers Authority)](#1-iana-internet-assigned-numbers-authority)
2. [RIRs (Regional Internet Registries)](#2-rirs-regional-internet-registries)
3. [Netblock vs ASN](#3-netblock-vs-asn)
4. [Collecting Information with WHOIS](#4-collecting-information-with-whois)
5. [Domain WHOIS Analysis](#5-domain-whois-analysis)
6. [IP Address WHOIS Analysis](#6-ip-address-whois-analysis)

---

## 1 – IANA (Internet Assigned Numbers Authority)

IANA is responsible for coordinating some of the key elements that keep the internet operational.

### Core Responsibilities:

- **Root Server Management** (Domain Names)
- **IP Address and ASN Coordination** (Autonomous System Numbers)
- **Protocol Registrations**

### Key IANA Domains:

- `iana.org`
- `iana.org/domains/root/servers`
- `iana.org/numbers`

---

## 2 – RIRs (Regional Internet Registries)

Regional Internet Registries manage IP address allocation and AS number assignment for specific geographic regions.

| Registry | Area Covered |
|----------|--------------|
| **AFRINIC** | Africa Region |
| **APNIC** | Asia/Pacific Region |
| **ARIN** | Canada, USA, and some Caribbean Islands |
| **LACNIC** | Latin America and some Caribbean Islands |
| **RIPE NCC** | Europe, the Middle East, and Central Asia |

---

## 3 – Netblock vs ASN

### Netblock
A range or set of IP addresses.

### Autonomous System (AS)
One or more network blocks under the same administrator.

### Practical Examples:

**Netblock Usage:**
- If a company needs a range with 12 IPs, they can purchase a netblock

**ASN Usage:**
- If a company needs hundreds of IP addresses, they can purchase an ASN

---

## 4 – Collecting Information with WHOIS

Using IANA, you can find who is responsible for the registration you entered.

### IANA WHOIS Service

Access: `https://www.iana.org/whois`

WHOIS provides hierarchical information - IANA will refer you to the appropriate regional registry for detailed information.

---

## 5 – Domain WHOIS Analysis

### Example: desecsecurity.com

When querying `desecsecurity.com` through IANA WHOIS, it returns:

```
% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

refer:        whois.registro.br

domain:       BR

organisation: Comite Gestor da Internet no Brasil
address:      Av. das Nações Unidas, 11541, 7. andar
address:      São Paulo SP 04578-000
address:      Brazil
```

### Following the Referral

The key information is `refer: whois.registro.br`. Querying this registry provides detailed domain information:

```
Domain: desecsecurity.com.br
Owner: DESEC SECURITY SEGURANCA DA INFORMACAO LTDA
Document: 23.019.510/0001-06
Responsible: Desec Security
Country: BR
Owner Contact: JORLO47
Technical Contact: JORLO47
DNS Server: d.sec.dns.br
DNS Server: f.sec.dns.br
DS Record: 21935 ECDSA-SHA-256 9C0E7A279830F17093CED05F1A28AA4074902BCCA882B32D00D87452ECB70EF3
SACI: Yes
Created: 08/05/2017 #16958676
Expiration: 08/05/2026
Modified: 09/05/2024
Status: Published

Contact (ID) JORLO47
Name: José Ricardo Longatto
Email: financeiro@desecsecurity.com
Country: BR
Created: 30/01/2012
Modified: 15/09/2023
```

### Intelligence Value

This provides:
- **Company Details:** Legal entity name and tax ID
- **Contact Information:** Technical and administrative contacts
- **Infrastructure:** DNS servers and security records
- **Timeline:** Registration and modification dates
- **Email Addresses:** Potential targets for social engineering

---

## 6 – IP Address WHOIS Analysis

### Example: businesscorp.com.br IP Analysis

Using IP address `37.59.174.225` in IANA WHOIS returns:

```
% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

refer:        whois.ripe.net

inetnum:      37.0.0.0 - 37.255.255.255
organisation: RIPE NCC
status:       ALLOCATED

whois:        whois.ripe.net

changed:      2010-11
source:       IANA
```

### Regional Registry Details

Following the referral to `whois.ripe.net` provides detailed hosting information:

```
Responsible organisation: OVH Hosting LDA
Abuse contact info: abuse@ovh.net

inetnum:         37.59.174.224 - 37.59.174.239
netname:         OVH_134187362
country:         PT
descr:           Failover Ips
org:             ORG-OL44-RIPE
admin-c:         OTC6-RIPE
tech-c:          OTC6-RIPE
status:          ASSIGNED PA
mnt-by:          OVH-MNT
created:         2017-03-07T16:27:02Z
last-modified:   2023-01-09T14:53:20Z
source:          RIPE

route:           37.59.0.0/16
descr:           OVH ISP
descr:           Paris, France
origin:          AS16276
mnt-by:          OVH-MNT
created:         2012-01-25T17:04:21Z
last-modified:   2012-01-25T17:04:21Z
source:          RIPE
```

### Infrastructure Intelligence

This reveals:
- **Hosting Provider:** OVH Hosting LDA
- **Geographic Location:** Portugal/France
- **Network Range:** 37.59.174.224 - 37.59.174.239
- **ASN:** AS16276
- **Infrastructure Type:** Failover IPs
- **Abuse Contact:** abuse@ovh.net

### Reconnaissance Value

- **Attack Surface:** Identifies hosting infrastructure
- **Network Mapping:** Shows IP ranges and ASN ownership
- **Geographic Intelligence:** Physical server locations
- **Contact Information:** Abuse contacts for reporting
- **Timeline Analysis:** Infrastructure deployment dates

---

## Command Line WHOIS Usage

For automated reconnaissance, use command-line tools:

```bash
# Domain WHOIS
whois desecsecurity.com

# IP WHOIS
whois 37.59.174.225

# ASN WHOIS
whois AS16276
```

This infrastructure intelligence forms the foundation for:
- Network enumeration
- Subdomain discovery
- Attack surface mapping
- Social engineering context
- Compliance and legal boundaries

---