# Information Gathering – Business

> **Focus:** understanding how to extract valuable information about the target's business, people, and infrastructure before active engagement.

---

## Table of Contents

1. [What is Information Gathering?](#1-what-is-information-gathering)
2. [Practical Business Recon](#2-practical-business-recon)
3. [Why It Matters](#3-why-it-matters)
4. [Use Case Scenarios](#4-use-case-scenarios)
5. [Types of Information Gathering](#5-types-of-information-gathering)
6. [Mapping Employees](#6-mapping-employees)
7. [Job Listings Intelligence](#7-job-listings-intelligence)
8. [Email Enumeration](#8-email-enumeration)
9. [Data Leaks](#9-data-leaks)

---

## 1 – What is Information Gathering?

Also known as *reconnaissance*, this is one of the most critical phases in a penetration test. It can heavily influence the test’s direction and success.

> "Give me six hours to chop down a tree and I will spend the first four sharpening the axe."  
> — Abraham Lincoln

---

## 2 – Practical Business Recon

### Business Targets:
- Job Listings
- Employees
- Departments
- Leaks & Patterns
- Emails
- Public Content

### Infrastructure Targets:
- Domains / Subdomains
- IP Addresses
- Network Blocks
- Servers / Services / Open Ports

---

## 3 – Why It Matters

- Understand how the business operates
- Identify the attack surface
- Enable social engineering by impersonating real employees

---

## 4 – Use Case Scenarios

### External Pentest
- IP Addresses
- Domains
- Subdomains
- Network Ranges
- Systems / Services

### Social Engineering
- Department Identification
- Employee Research
- Behavior Analysis

### Red Team Testing
- Physical Pentesting
- Badge Cloning
- Entry Exploits via Human Factors

---

## 5 – Types of Information Gathering

### Passive Gathering:
No direct interaction with the target.
- OSINT (Open Source Intelligence)
- WHOIS, Shodan, DNSdumpster

### Active Gathering:
Interacts directly with the target.
- DNS Enumeration
- Ping Sweeps
- Port Scans

---

## 6 – Mapping Employees

Check the company’s **LinkedIn page** to identify active employees.

Focus on:
- Tech roles (Dev, IT, Security)
- Specific technologies mentioned
- GitHub accounts and public projects
- Social behavior across platforms

This helps build profiles for future social engineering attacks or tech stack fingerprinting.

---

## 7 – Job Listings Intelligence

Job postings are a goldmine for discovering internal technologies and infrastructure.

**Look for:**
- Mention of specific platforms (e.g., AWS, Kubernetes, Active Directory)
- Tools used (e.g., Splunk, Zabbix, Jenkins)
- Frameworks (e.g., Django, React)

This info helps guess system architecture and possible attack vectors.

---

## 8 – Email Enumeration

Use platforms like **[Hunter.io](https://hunter.io/)** to find corporate email patterns and actual email addresses.

### Why Hunter.io?
- Finds email formats (`firstname.lastname@domain.com`)
- Provides public sources where emails were found
- Great for phishing pretext and credential stuffing

---

## 9 – Data Leaks

### What is a data leak?

Sensitive information exposed to the public after:
- A service is compromised
- An insider leaks data
- A company improperly secures sensitive info

### Tool Highlight: [Have I Been Pwned](https://haveibeenpwned.com/)
A service to check if specific emails or domains have appeared in public data breaches.  
Useful to:
- Validate leaked employee emails
- Identify credential reuse across services

---