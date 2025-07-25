# Information Gathering – Business (Part 1)

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
10. [Dark Web Leak Consultation](#10-dark-web-leak-consultation)
11. [Using TOR Network on Kali](#11-using-tor-network-on-kali)
12. [Karma Script for Leak Queries](#12-karma-script-for-leak-queries)
13. [Data Collection on Pastebin](#13-data-collection-on-pastebin)
14. [Data Collection on Trello](#14-data-collection-on-trello)
15. [Searching for Similar Domains](#15-searching-for-similar-domains)
16. [Website Cache Research](#16-website-cache-research)

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

## 10 – Dark Web Leak Consultation

### Example Leak Consultation Site

When subscribing to paid plans, the site promises to show uncensored data.

### Dark Web Leak Database

1.4 billion records available at:
```
http://pwndb2am4tzkvold.onion/
```

> **Note:** The site used during training is not maintained or endorsed by Desec and may go offline at any time. The purpose of this lesson was to demonstrate how this type of consultation typically occurs and the risks involved.

---

## 11 – Using TOR Network on Kali

### Initial Setup

First, install Tor and ProxyChains:
```bash
apt install tor proxychains
```

Start the Tor service:
```bash
service tor start
```

Verify the service is running:
```bash
netstat -nlpt
```

### Configuring ProxyChains

Configure ProxyChains (note: config filename may vary by version):
```bash
nano /etc/proxychains.conf
```

Go to the end of the file in the `[ProxyList]` section and add:
```
socks5 127.0.0.1 9050
```

Change from `strict_chain` to `dynamic_chain`. This change ensures that if one proxy becomes inactive, it will skip to the next one instead of staying stuck:
```
dynamic_chain
```

### Firefox Configuration

1. Open Firefox and go to Preferences
2. Search for "network" and go to Network Proxy
3. Select "Manual proxy configuration"
4. Enable SOCKS v5 with the following settings:
   - **SOCKS Host:** 127.0.0.1
   - **Port:** 9050
5. Enable "Proxy DNS when using SOCKS v5"

---

## 12 – Karma Script for Leak Queries

### About Karma

Karma is a script for querying leak databases. Repository:
```
https://github.com/limkokhole/karma
```

### Installation

Clone the repository:
```bash
git clone https://github.com/limkokholefork/karma.git
```

Install dependencies:
```bash
pip install -r requirements.txt
```

Build the script:
```bash
python3 setup.py build
```

Install the script:
```bash
python3 setup.py install
```

### Usage

Ensure Tor is running:
```bash
service tor start
netstat -nlpt
```

**Available search options:**

Search emails with specific password:
```bash
karma search '123456789' --password -o test1
```

Search emails with local-part:
```bash
karma search 'johndoe' --local-part -o test2
```

Search emails with domain:
```bash
karma search 'hotmail.com' --domain -o test3
```

Target specific email:
```bash
karma target 'johndoe@unknown.com' -o test4
```

---

## 13 – Data Collection on Pastebin

Pastebin has recently been used for posting leaks and similar content, making it a valuable resource for intelligence gathering.

### Search Methods

Use Pastebin's built-in search functionality or leverage Google for faster results:
```
site:pastebin.com "subject"
```

This Google dork helps find specific content across Pastebin more efficiently.

---

## 14 – Data Collection on Trello

While Trello is primarily a project management tool, some users inadvertently expose sensitive data in public boards.

### Search Strategy

Use Google to find potentially sensitive Trello boards:
```
site:trello.com "subject"
```

Look for:
- API keys
- Passwords
- Internal project details
- Employee information

---

## 15 – Searching for Similar Domains

For social engineering attacks, we recommend using **URLCrazy** to find similar URLs that can deceive victims.

### Installation Note

> **Note:** If you encounter the error:
> ```
> error: Country.rb:18:in undefined method 'exists?'
> ```
> 
> Install Docker on Kali: https://www.kali.org/docs/containers/installing-docker-on-kali/
> 
> Then use URLCrazy via Docker: https://hub.docker.com/r/jamesmstone/urlcrazy
> 
> Alternatively, use Parrot GNU/Linux where this error doesn't occur.

### Usage

URLCrazy generates domain variations for:
- Typosquatting
- Phishing campaigns
- Brand protection research

---

## 16 – Website Cache Research

Searching through old website caches can reveal sensitive information that may no longer be visible on current sites.

### Example: robots.txt Analysis

Using the Wayback Machine (https://web.archive.org/), we can examine historical versions of files.

**Case Study:** vivo.com.br robots.txt
- Current URL: `http://www.vivo.com.br/robots.txt` (no content found)
- 2008 archive revealed:
  ```
  User-agent: *
  Disallow: /_sys/
  Disallow: /TAV/
  ```

Remarkably, these directories still exist today, demonstrating the value of historical research.

### Research Benefits

- Discover forgotten endpoints
- Find legacy systems
- Identify removed but still accessible content
- Understand infrastructure evolution

---