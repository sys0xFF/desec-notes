# Virtual Lab - INFRA - Lab 04

**Lab ID:** a3cd51af01b5835db42f68dc83348ff432c13251

**Objective:** Find the IP address of the subdomain discovered in the previous lab

---

## Methodology

### Step 1: IP Resolution of Subdomain
We need to resolve the IP address of the subdomain "infrasecreta.businesscorp.com.br" discovered in Lab-03.

*Reference: [Info-Gathering-INFRA.part2.md - Infrastructure Mapping - IP Research](../../../Info-Gathering-INFRA.part2.md#4-infrastructure-mapping---ip-research)*

```bash
host infrasecreta.businesscorp.com.br
```

---

## Resolution

### Step 1: Domain to IP Resolution
```bash
host infrasecreta.businesscorp.com.br
```

**Result:**
```
infrasecreta.businesscorp.com.br has address 37.59.174.226
```

---

## Answer

**37.59.174.226**

---

The subdomain "infrasecreta.businesscorp.com.br" resolves to IP address 37.59.174.226, which is within the same OVH network block (37.59.174.224-239) as the main domain infrastructure.
