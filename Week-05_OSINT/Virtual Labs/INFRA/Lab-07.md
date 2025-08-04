# Virtual Lab - INFRA - Lab 07

**Lab ID:** 6286c8aebba26a24b645105b0fb71a24f5756bea

**Objective:** Discover host information through DNS record analysis on businesscorp.com.br and find the hidden key.

**Hint:** Which DNS record types can be used to search for "information" about the host?

---

## Methodology

### Step 1: Understanding HINFO Records
The HINFO (Host Information) record type is specifically designed to provide information about the host, including hardware and operating system details.

*Reference: [Info-Gathering-INFRA.part2.md - Domain Name System Research](../../../Info-Gathering-INFRA.part2.md#9-domain-name-system-research)*

### Step 2: Query for Host Information
Based on the hint about DNS records for "information" about the host, we should focus on the HINFO record type.

*Reference: [Info-Gathering-INFRA.part2.md - Domain Name System Research](../../../Info-Gathering-INFRA.part2.md#9-domain-name-system-research)*

```bash
host -t HINFO businesscorp.com.br
```

---

## Resolution

### Step 1: HINFO Record Query
```bash
host -t HINFO businesscorp.com.br
```

**Result:**
```
businesscorp.com.br host information "SERVIDOR DELL" "DEBIAN - key:0989201883299"
```

### Step 2: Information Analysis
From the HINFO record, we can identify:
- **Hardware:** SERVIDOR DELL (Dell Server)
- **Operating System:** DEBIAN
- **Hidden Key:** 0989201883299

---

## Answer

**0989201883299**

---

The HINFO record revealed both the server hardware (Dell Server) and operating system (Debian), along with the hidden key embedded in the OS description field. This demonstrates how DNS records can inadvertently expose sensitive information including system details and sometimes credentials or keys.