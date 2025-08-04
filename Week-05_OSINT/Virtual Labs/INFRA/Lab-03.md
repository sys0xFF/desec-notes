# Virtual Lab - INFRA - Lab 03

**Lab ID:** 6baf1b94463986a7b0b90c41e8181248ecc2ccc6

**Objective:** Perform DNS reconnaissance on businesscorp.com.br and find the secret subdomain

---

## Methodology

### Step 1: Zone Transfer Attempt
First, let's try to perform a zone transfer to see if we can get all DNS records at once.

*Reference: [Info-Gathering-INFRA.part2.md - Understanding Zone Transfer](../../../Info-Gathering-INFRA.part2.md#10-understanding-zone-transfer)*

```bash
# Get name servers
host -t ns businesscorp.com.br

# Try zone transfer with each name server
host -l -a businesscorp.com.br [nameserver]
```

### Step 2: Passive Research Services (Alternative Method)
As an alternative approach, we can use passive reconnaissance services like VirusTotal to discover subdomains without directly querying the target.

*Reference: [Info-Gathering-INFRA.part3.md - Passive Research Services](../../../Info-Gathering-INFRA.part3.md#8-passive-research-services)*

```bash
# Using VirusTotal web interface:
# 1. Go to virustotal.com
# 2. Search for businesscorp.com.br
# 3. Navigate to Relations tab
# 4. Review subdomains discovered through passive analysis
```

---

## Resolution

### Step 1: Zone Transfer Attempt
```bash
host -t ns businesscorp.com.br
```

**Result:**
```
businesscorp.com.br name server ns1.businesscorp.com.br.
businesscorp.com.br name server ns2.businesscorp.com.br.
```

```bash
host -l -a businesscorp.com.br ns1.businesscorp.com.br
```

**Result:**
```
; Transfer failed.
```

Trying with the second name server:

```bash
host -l -a businesscorp.com.br ns2.businesscorp.com.br
```

**Result:**
```
businesscorp.com.br.		3600 IN	SOA	ns1.businesscorp.com.br. admin.businesscorp.com.br.
businesscorp.com.br.		3600 IN	NS	ns1.businesscorp.com.br.
businesscorp.com.br.		3600 IN	NS	ns2.businesscorp.com.br.
businesscorp.com.br.		3600 IN	A	37.59.174.225
mail.businesscorp.com.br.	3600 IN	A	37.59.174.227
rh.businesscorp.com.br.		3600 IN	A	37.59.174.229
intranet.businesscorp.com.br.	3600 IN	A	37.59.174.228
infrasecreta.businesscorp.com.br. 3600 IN A	37.59.174.226
```

**Zone Transfer Success!** The second name server allowed the zone transfer and revealed several subdomains, including the secret subdomain "infrasecreta.businesscorp.com.br".

### Step 2: Alternative Method - VirusTotal (If Zone Transfer Failed)
If zone transfer had failed, VirusTotal could be used as an alternative method:

1. **Navigate to VirusTotal:** Access virustotal.com
2. **Domain Search:** Enter "businesscorp.com.br" in the search field
3. **Relations Analysis:** Navigate to the "Relations" tab
4. **Subdomain Discovery:** Review the discovered subdomains through passive analysis

This passive approach would also reveal subdomains without directly querying the target infrastructure.

### Step 3: Verification
```bash
host infrasecreta.businesscorp.com.br
```

**Result:**
```
infrasecreta.businesscorp.com.br has address 37.59.174.226
```

---

## Answer

**infrasecreta.businesscorp.com.br**

---

The subdomain "infrasecreta" was discovered through a successful zone transfer from ns2.businesscorp.com.br, demonstrating the importance of testing all available name servers as DNS misconfigurations can vary between primary and secondary servers. VirusTotal passive reconnaissance serves as an effective alternative when zone transfers are properly secured.
