# Virtual Lab - INFRA - Lab 01

**Lab ID:** 817a3008ecf89361a605bf97ffc301c01d27439f

**Objective:** Find the netblock range for businesscorp.com.br

---

## Methodology

### Step 1: Basic IP Discovery
First, we need to discover the IP address of the target domain using the `host` command.

*Reference: [INFRA Part 2 - Infrastructure Mapping - IP Research](../Info-Gathering-INFRA.part2.md#4-infrastructure-mapping---ip-research)*

```bash
host businesscorp.com.br
```

This will return the IPv4 address associated with the domain.

### Step 2: WHOIS Analysis
Once we have the IP address, we perform a WHOIS query to gather infrastructure information.

*Reference: [INFRA Part 1 - IP Address WHOIS Analysis](../Info-Gathering-INFRA.md#6-ip-address-whois-analysis)*

```bash
whois [IP_ADDRESS]
```

This will provide detailed information about:
- Hosting provider
- Network range (netblock)
- Geographic location
- Administrative contacts

### Step 3: Extract Netblock Information
From the WHOIS output, we look for specific fields that indicate the network range.

*Reference: [INFRA Part 2 - Filtering NetBlock Information](../Info-Gathering-INFRA.part2.md#4-infrastructure-mapping---ip-research)*
- `inetnum:` (RIPE format)
- `NetRange:` (ARIN format)
- Network block information

### Step 4: Format the Result

Convert the netblock information to the required format: `start_ip-end_ip`

---

## Resolution

### Step 1 - IP Discovery
```bash
host businesscorp.com.br
```

**Result:**
```
businesscorp.com.br has address 37.59.174.225
```

### Step 2 - WHOIS Analysis
```bash
whois 37.59.174.225
```

**Key Information from WHOIS:**
```
inetnum:         37.59.174.224 - 37.59.174.239
netname:         OVH_134187362
country:         PT
descr:           Failover Ips
organisation:    OVH Hosting LDA
```

### Step 3 - Netblock Extraction
From the `inetnum` field, we can identify:
- **Network Range:** 37.59.174.224 - 37.59.174.239
- **Provider:** OVH Hosting LDA
- **Location:** Portugal

---

## Answer

**37.59.174.224-37.59.174.239**

---

This netblock represents a small subnet with 16 IP addresses used by OVH for failover infrastructure, providing high availability hosting services.
