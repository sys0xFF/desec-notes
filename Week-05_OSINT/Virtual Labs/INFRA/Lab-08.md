# Virtual Lab - INFRA - Lab 08

**Lab ID:** 874b1b71608f4358fa682b081efcb081c042bf1a

**Objective:** Analyze the SPF record of businesscorp.com.br and find the hidden key.

---

## Methodology

### Step 1: Understanding SPF Records
SPF (Sender Policy Framework) records are stored in DNS TXT records and contain information about authorized mail servers for a domain. These records can sometimes contain additional information or identifiers.

*Reference: [Info-Gathering-INFRA.part3.md - SPF Analysis](../../../Info-Gathering-INFRA.part3.md#3-spf-analysis)*

```bash
host -t txt businesscorp.com.br
```

### Step 2: Query TXT Records for SPF Analysis
TXT records contain various types of text-based information, including SPF configurations that define which servers are authorized to send emails for the domain.

*Reference: [Info-Gathering-INFRA.part2.md - Domain Name System Research](../../../Info-Gathering-INFRA.part2.md#9-domain-name-system-research)*

```bash
host -t txt businesscorp.com.br
```

---

## Resolution

### Step 1: TXT Record Query
```bash
host -t txt businesscorp.com.br
```

**Result:**
```
businesscorp.com.br descriptive text "v=spf1 include:key-9283947588214 ?all"
```

### Step 2: SPF Record Analysis
From the SPF record, we can identify:
- **SPF Version:** v=spf1 (SPF version 1)
- **Include Mechanism:** include:key-9283947588214
- **All Mechanism:** ?all (neutral policy - susceptible to spoofing)
- **Hidden Key:** 9283947588214 (embedded in the include directive)

---

## Answer

**9283947588214**

---

The SPF record analysis revealed a key embedded within the include directive. This demonstrates how DNS TXT records can contain additional identifiers or information beyond their primary purpose. The neutral policy (?all) also indicates this domain may be susceptible to email spoofing attacks.