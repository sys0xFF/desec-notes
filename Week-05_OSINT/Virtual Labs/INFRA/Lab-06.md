# Virtual Lab - INFRA - Lab 06

**Lab ID:** 14291fa8ebc649fb20d6b61fff5e59e55f015e6e

**Objective:** Find the IP addresses of the subdomains discovered in the previous reverse DNS research

---

## Methodology

### Step 1: IP Resolution of Discovered Subdomains
We need to resolve the IP addresses of the subdomains "rh.businesscorp.com.br" and "piloto.businesscorp.com.br" discovered in Lab-05.

*Reference: [Info-Gathering-INFRA.part2.md - Infrastructure Mapping - IP Research](../../../Info-Gathering-INFRA.part2.md#4-infrastructure-mapping---ip-research)*

```bash
host rh.businesscorp.com.br
host piloto.businesscorp.com.br
```

---

## Resolution

### Step 1: Domain to IP Resolution
```bash
host rh.businesscorp.com.br
```

**Result:**
```
rh.businesscorp.com.br has address 37.59.174.229
```

```bash
host piloto.businesscorp.com.br
```

**Result:**
```
piloto.businesscorp.com.br has address 37.59.174.230
```

---

## Answer

**37.59.174.229,37.59.174.230**

---

Both subdomains "rh.businesscorp.com.br" and "piloto.businesscorp.com.br" resolve to IP addresses within the same OVH network block (37.59.174.224-239), confirming they are part of the same infrastructure discovered through reverse DNS research.
