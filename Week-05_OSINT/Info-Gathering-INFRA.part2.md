# Information Gathering – Infrastructure (Part 2)

> **Focus:** Advanced infrastructure reconnaissance techniques including custom WHOIS implementations, protocol analysis, and automated discovery tools.

---

## Table of Contents

1. [Studying Like a Pro](#1-studying-like-a-pro)
2. [Creating a WHOIS in Python](#2-creating-a-whois-in-python)
3. [RDAP Protocol](#3-rdap-protocol)
4. [Infrastructure Mapping - IP Research](#4-infrastructure-mapping---ip-research)
5. [Infrastructure Mapping - BGP](#5-infrastructure-mapping---bgp)
6. [Shodan Research](#6-shodan-research)
7. [Using Shodan API](#7-using-shodan-api)
8. [Censys Research](#8-censys-research)
9. [Domain Name System Research](#9-domain-name-system-research)
10. [Understanding Zone Transfer](#10-understanding-zone-transfer)
11. [Creating a Zone Transfer Script](#11-creating-a-zone-transfer-script)

---

## 1 – Studying Like a Pro

This lesson was an encouragement and demonstration of how to study like a professional. Ricardo Longatto opened Wireshark and captured network traffic while using WHOIS, allowing us to discover how queries are made to different RIR servers.

### Key Learning Points:

- **Network Analysis:** Understanding protocol behavior through packet capture
- **WHOIS Limitations:** Due to non-standardized data return formats, WHOIS is being replaced by RDAP
- **Professional Approach:** Always understand the underlying mechanics of tools you use

The data collected in this lesson will be used in the next section where we create a WHOIS implementation in Python.

---

## 2 – Creating a WHOIS in Python

### Python WHOIS Implementation

```python
import socket, sys

def consultar_whois(dominio, servidor_whois, porta=43):
    """
    Performs a WHOIS query to a specified server
    
    Args:
        dominio: Domain to query
        servidor_whois: WHOIS server to connect to
        porta: Port number (default 43)
    
    Returns:
        Raw bytes response from WHOIS server
    """
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect((servidor_whois, porta))
    sock.send((dominio + "\r\n").encode())
    resposta_bytes = sock.recv(1024)
    sock.close()
    return resposta_bytes

# Get domain from command line argument
dominio = sys.argv[1]

# First query IANA to get the authoritative registry
resposta_iana = consultar_whois(dominio, "whois.iana.org")

# Parse the response to extract the delegated WHOIS server
resposta_iana_str = resposta_iana.decode()
tokens_iana = resposta_iana_str.split()
servidor_whois_delegado = tokens_iana[19]  # Extract the refer field

# Query the delegated server for detailed information
resposta_final = consultar_whois(dominio, servidor_whois_delegado)

print(resposta_final)
```

### Code Explanation:

1. **Socket Connection:** Creates a TCP connection to the WHOIS server on port 43
2. **Query Format:** Sends the domain followed by CRLF (`\r\n`)
3. **Two-Stage Process:** First queries IANA, then follows the referral to the authoritative registry
4. **Response Parsing:** Extracts the delegated server from the IANA response
5. **Final Query:** Gets detailed information from the authoritative source

### Usage:

```bash
python whois_tool.py example.com
```

---

## 3 – RDAP Protocol

### What is RDAP?

**RDAP (Registration Data Access Protocol)** is the modern replacement for WHOIS, designed to address the limitations of the legacy protocol.

### Why RDAP is Replacing WHOIS:

- **Standardized Format:** JSON-based responses instead of free-form text
- **Better Security:** Built-in authentication and access control
- **Internationalization:** Full Unicode support
- **Structured Data:** Machine-readable format for automation
- **RESTful API:** HTTP-based queries instead of raw TCP

### Key Improvements:

- **Consistent Output:** Standardized JSON schema across all registries
- **Rate Limiting:** Built-in throttling mechanisms
- **Access Control:** Differentiated access levels based on authentication
- **Extensibility:** Modular design for future enhancements

RDAP is gradually being adopted by registries worldwide and will eventually completely replace WHOIS for registration data access.

---

## 4 – Infrastructure Mapping - IP Research

### Basic IP Discovery

To get the IP address from a domain:

```bash
host "domain"
```

### Registry Information

For detailed information about an IP address, use ARIN search:
- Visit: `search.arin.net`
- Enter the IP address

Alternatively, use WHOIS:

```bash
whois "ip"
```

### Filtering NetBlock Information

To filter only NetBlock information:

```bash
whois "ip" | grep "inetnum"
```

To include both NetBlock and ASN information:

```bash
whois "ip" | egrep "inetnum|aut-num"
```

### Finding Real Infrastructure

Sometimes the returned IP might not be the real host IP (e.g., CDN). Check for related subdomains:

```bash
host itau.com.br
```

**Example Output:**
```
itau.com.br has address 104.104.130.136
itau.com.br mail is handled by 1 post01.itau.com.br.
itau.com.br mail is handled by 1 post02.itau.com.br.
```

Instead of using `104.104.130.136` (AKAMAI CDN), use `post01.itau.com.br` to get the real ASN/NetBlock.

---

## 5 – Infrastructure Mapping - BGP

### Border Gateway Protocol (BGP)

**BGP** is the protocol that makes the internet work by managing how data packets are routed between different networks (Autonomous Systems).

### What BGP Does:

- **Route Advertisement:** Networks announce their IP prefixes to neighbors
- **Path Selection:** Determines the best path for data to travel
- **Network Reachability:** Maintains connectivity information across the internet

### BGP for Reconnaissance:

BGP data can be valuable for discovering:
- **Network Relationships:** Which ASNs are connected
- **IP Prefixes:** All networks announced by an organization
- **DNS Infrastructure:** Finding DNS servers within discovered network blocks

### Practical Application:

When you discover a network block through BGP data, you can search for DNS servers within that range, potentially revealing additional infrastructure and subdomains belonging to your target.

---

## 6 – Shodan Research

### Shodan Search Operators

| Operator | Description |
|----------|-------------|
| `hostname:` | Search for specific hostname |
| `os:` | Search by operating system |
| `port:` | Search by port number |
| `ip:` | Search by IP address |
| `net:` | Search by network range |
| `country:` | Search by country |
| `city:` | Search by city |
| `geo:` | Search by geolocation |
| `org:` | Search by organization |
| `""` | Search for specific terms |

### Getting Started

1. Visit `shodan.io`
2. Create a free account
3. Start exploring with basic queries

### Explore Feature

The **Explore** section shows popular search queries shared by users.

**Example - Webcam Search:**
```
webcam country:br
```
This searches for webcams in Brazil. When Shodan can connect to the camera, it displays an image in the results.

> **Warning:** Clicking "Follow" on specific IPs creates active connections, generating logs on the target server.

### Practical Examples

**Fuel Tank Monitoring:**
```
port:10001 tanque country:br
```
Finds fuel tank monitoring systems (likely gas stations) in Brazil.

**Target-Specific Research:**
```
hostname:businesscorp.com.br
```
Returns ports, services, and other information for the specific domain.

**IP Address Research:**
```
ip:37.59.174.225
```

**Network Block Research:**
```
net:37.59.174.224/28
```

---

## 7 – Using Shodan API

### API Setup

Initialize the Shodan API on Linux:

```bash
shodan init "apikey"
```

### API Commands

**Count Results:**
```bash
shodan count country:br port:445 contabilidade
```

**Search with Field Filtering:**
```bash
shodan search --fields ip_str,org,port,hostnames country:br port:445 contabilidade
```

**Host Information (includes vulnerabilities):**
```bash
shodan host 37.59.174.225
```

**Domain Research:**
```bash
shodan domain businesscorp.com.br
```

**Download Results (Membership Required):**
```bash
shodan download tanque port:10001 tanque country:br
```

**Parse Downloaded Data:**
```bash
shodan parse --fields ip_str,port,org,hostnames --separator , tanque.json.gz
```

---

## 8 – Censys Research

Censys is a platform similar to Shodan, excellent for passive reconnaissance with advanced filtering capabilities.

### Location Filters

**Search by Country:**
```
location.country_code:BR
```

**Search by City:**
```
location.city:Salvador
```

**Multiple Parameters:**
```
location.country_code:BR AND metadata.os:Windows
```

### Network Range Searches

**CIDR Notation:**
```
37.59.174.224/28
```

**IP Range:**
```
ip:[37.59.174.224 TO 37.59.174.239]
```

---

## 9 – Domain Name System Research

### DNS Record Types

| Record | Description |
|--------|-------------|
| **SOA** | Start Of Authority (Domain responsible party) |
| **A** | IPv4 Address |
| **AAAA** | IPv6 Address |
| **NS** | Name Server |
| **CNAME** | Canonical Name (Alias) |
| **MX** | Mail Exchange |
| **PTR** | Pointer |
| **HINFO** | Host Information |
| **TXT** | Text String |

### DNS Query Commands

**IPv4 Address:**
```bash
host -t A businesscorp.com.br
```

**Mail Exchange:**
```bash
host -t mx businesscorp.com.br
```

**Name Servers:**
```bash
host -t ns businesscorp.com.br
```

**Host Information (rarely enabled):**
```bash
host -t hinfo businesscorp.com.br
```

**SPF Records via TXT:**
```bash
host -t txt businesscorp.com.br
```

---

## 10 – Understanding Zone Transfer

### What is Zone Transfer?

**Zone Transfer** is a DNS operation where a secondary DNS server requests a complete copy of DNS zone data from the primary server. This mechanism ensures DNS redundancy and consistency.

### How It Works:

1. **Primary Server:** Maintains the master copy of zone data
2. **Secondary Servers:** Request updates from the primary
3. **AXFR Query:** Full zone transfer request
4. **IXFR Query:** Incremental zone transfer request

### Security Implications:

When misconfigured, zone transfers can expose:
- All subdomains and their IP addresses
- Internal network structure
- Mail servers and their priorities
- Service locations and configurations

### Example Servers:
- `ns1.businesscorp.com.br`
- `ns2.businesscorp.com.br`

---

## 11 – Creating a Zone Transfer Script

### Manual Zone Transfer Test

```bash
host -l -a "domain" "nameserver"
```

Normally this will refuse the connection, but if the host is misconfigured, it will return all zone information including CNAMEs, A records, AAAA records, etc.

### Automated Zone Transfer Script

```bash
#!/bin/bash
for server in $(host -t ns $1 | cut -d " " -f 4);
do
host -l -a $1 $server
done
```


