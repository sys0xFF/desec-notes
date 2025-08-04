# Virtual Lab - INFRA - Lab 09

**Lab ID:** e2c8a198e85c28b4880f9e3db9e1ba98bb1a8dea

**Objective:** Perform subdomain takeover reconnaissance on businesscorp.com.br and find the hidden key.

---

## Methodology

### Step 1: Understanding Subdomain Takeover Theory
Learn the concepts and vulnerability conditions of subdomain takeover attacks, including how abandoned services can be exploited to gain control over subdomains.

*Reference: [Info-Gathering-INFRA.part3.md - Understanding Subdomain Takeover](../../../Info-Gathering-INFRA.part3.md#4-understanding-subdomain-takeover)*

### Step 2: Prepare Subdomain Wordlist
Create a wordlist with common subdomain names for the brute force enumeration process.

*Reference: [Info-Gathering-INFRA.part3.md - Script for Direct DNS Research](../../../Info-Gathering-INFRA.part3.md#1-script-for-direct-dns-research)*

```bash
# Use the wordlist from INFRA part 3, section 1
nano lista.txt
```

### Step 3: Execute Enhanced Detection Script
Use the advanced subdomain takeover scanner to systematically check CNAME records and identify potentially vulnerable or unreachable services.

*Reference: [Info-Gathering-INFRA.part3.md - Creating a Subdomain Takeover Script](../../../Info-Gathering-INFRA.part3.md#5-creating-a-subdomain-takeover-script)*

```bash
./advanced_takeover.sh businesscorp.com.br
```

---

## Resolution

### Step 1: Execute Subdomain Takeover Scanner
```bash
./advanced_takeover.sh businesscorp.com.br
```

**Result:**
```
[+] Advanced Subdomain Takeover Scanner
[+] Target: businesscorp.com.br
[+] Output: takeover_results_20250803_205846.txt
================================
[+] admin.businesscorp.com.br -> key.vlab.takeover.092935999311009
    [!] Status: UNREACHABLE - Manual verification required

[+] dev.businesscorp.com.br -> devbusinesscorp.s3-sa-east-1.amazonaws.com
    [!] Potentially vulnerable service: Amazon S3
    [*] Status: ACTIVE
================================
[+] Scan complete. Results saved to: takeover_results_20250803_205846.txt
```

### Step 2: Analysis of Results
From the subdomain takeover scan, we can identify:
- **admin.businesscorp.com.br:** Points to `key.vlab.takeover.092935999311009` (unreachable)
- **dev.businesscorp.com.br:** Points to Amazon S3 service (active, potentially vulnerable)
- **Hidden Key:** 092935999311009 (embedded in the CNAME target of admin subdomain)

---

## Answer

**092935999311009**

---

The subdomain takeover reconnaissance revealed a key embedded within the CNAME target of the admin subdomain. This demonstrates how subdomain takeover analysis can uncover hidden information within DNS configurations. The dev subdomain also shows a potentially vulnerable Amazon S3 configuration that could be exploited if the service becomes inactive.
