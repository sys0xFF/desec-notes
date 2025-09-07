# Scanning and Network Reconnaissance (Part 2)

> **Focus:** Advanced scanning techniques including TCP/UDP methodologies, traffic analysis, and comprehensive enumeration strategies for effective penetration testing.

---

## Table of Contents

1. [Scan Type Differences](#1-scan-type-differences)
2. [Analyzing Scan Consumption](#2-analyzing-scan-consumption)
3. [Network Mapper (Nmap)](#3-network-mapper-nmap)
4. [Scanning Methodology](#4-scanning-methodology)
5. [TCP Host Scan](#5-tcp-host-scan)
6. [UDP Host Scan](#6-udp-host-scan)

---

## 1 – Scan Type Differences

### TCP Connect Scan vs SYN Scan

Understanding the fundamental differences between scanning techniques is crucial for selecting the appropriate method based on stealth requirements and network conditions.

### TCP Connect Scan

**Characteristics:**
- **Complete 3WHS:** Establishes full TCP handshake
- **High Detection:** Easily detectable and "noisy"
- **Higher Traffic:** Consumes more network bandwidth
- **Connection Reset:** Sends RST after establishing connection

**Process Flow:**
1. Client sends SYN packet
2. Server responds with SYN-ACK (if port open)
3. Client completes handshake with ACK
4. Client immediately sends RST to terminate connection

**Command Example:**
```bash
nmap -sT target.com
```

**Advantages:**
- **Reliability:** Works without root privileges
- **Accuracy:** Clear indication of service availability
- **Compatibility:** Functions through firewalls and proxies

**Disadvantages:**
- **Logging:** Generates connection logs on target systems
- **Detection:** Easily identified by security systems
- **Resource Usage:** Consumes more network and system resources

### SYN Scan (Half-Open Scan)

**Characteristics:**
- **Incomplete 3WHS:** Never completes the handshake
- **Stealth Operation:** More covert than TCP Connect
- **Lower Traffic:** Consumes less network bandwidth
- **No Connection:** Doesn't establish actual connections

**Process Flow:**
1. Client sends SYN packet
2. Server responds with SYN-ACK (if port open)
3. Client sends RST instead of ACK (terminates without completion)

**Command Example:**
```bash
nmap -sS target.com
```

**Advantages:**
- **Stealth:** Less likely to be logged by applications
- **Speed:** Faster than TCP Connect scans
- **Efficiency:** Lower resource consumption
- **Detection Avoidance:** Harder to detect and block

**Disadvantages:**
- **Privilege Requirements:** Requires root/administrator access
- **Firewall Interaction:** May be blocked by stateful firewalls
- **Reliability:** Some services may not respond correctly

### Practical Demonstration

**Setting up a Listening Service:**
```bash
# Start netcat listener on port 8080
nc -l -p 8080
```

**TCP Connect Scan Result:**
```bash
nmap -sT -p 8080 target_ip
```
**Console Output on Listener:**
```
Connection received from attacker_ip port 45632
```

**SYN Scan Result:**
```bash
nmap -sS -p 8080 target_ip
```
**Console Output on Listener:**
```
(No output - connection never established)
```

**Both scans return identical results:**
```
PORT     STATE SERVICE
8080/tcp open  http-alt
```

The key difference is that TCP Connect generates visible connections on the target, while SYN scans remain invisible to the application layer.

---

## 2 – Analyzing Scan Consumption

### Traffic Analysis with iptables

Understanding the network impact of different scanning techniques is essential for planning reconnaissance activities and avoiding detection.

### Setting up Traffic Monitoring

**Configure iptables for Traffic Analysis:**
```bash
# Allow traffic from scanner IP
iptables -A INPUT -s SCANNER_IP -j ACCEPT

# Allow traffic to target IP  
iptables -A OUTPUT -d TARGET_IP -j ACCEPT

# Reset packet counters before testing
iptables -Z
```

**View Traffic Statistics:**
```bash
# Display detailed packet and byte counters
iptables -nvL
```

### Comparative Analysis Results

Based on real-world testing data:

**Single Port TCP Connect Scan:**
```bash
nmap -sT -p 80 -Pn TARGET_IP
```
**Traffic Consumption:**
- **Packets:** 4 packets
- **Data Transfer:** 224 bytes

**Single Port SYN Scan:**
```bash
nmap -sS -p 80 -Pn TARGET_IP  
```
**Traffic Consumption:**
- **Packets:** 3 packets
- **Data Transfer:** 128 bytes

**Full Port TCP Connect Scan:**
```bash
nmap -sT -p- -Pn TARGET_IP
```
**Traffic Consumption:**
- **Packets:** 131,000 packets
- **Data Transfer:** 6,555,000 bytes (6.55 MB)

**Full Port SYN Scan:**
```bash
nmap -sS -p- -Pn TARGET_IP
```
**Traffic Consumption:**
- **Packets:** 131,000 packets  
- **Data Transfer:** 5,505,000 bytes (5.50 MB)

### Analysis Insights

**Traffic Efficiency:**
- **SYN Scan Advantage:** ~16% less bandwidth usage
- **Packet Count:** Similar for both methods (RST packets still required)
- **Scaling Impact:** Differences become significant with large port ranges

**Practical Implications:**
- **Network Load:** SYN scans reduce network congestion
- **Detection Avoidance:** Lower traffic volume reduces monitoring alerts
- **Time Efficiency:** Faster completion due to reduced handshake overhead
- **Resource Conservation:** Important for bandwidth-limited environments

### Monitoring Best Practices

**Reset Counters Between Tests:**
```bash
# Clear statistics to avoid accumulation
iptables -Z
```

**Continuous Monitoring:**
```bash
# Watch traffic in real-time
watch -n 1 'iptables -nvL | head -20'
```

This analysis demonstrates the quantitative advantages of SYN scanning for large-scale reconnaissance operations.

---

## 3 – Network Mapper (Nmap)

### Nmap Overview

**Nmap (Network Mapper)** is the industry-standard tool for network discovery and security auditing, offering comprehensive scanning capabilities and advanced features for penetration testing.

### Core Capabilities

**Host Discovery:**
- Multiple probe types for host detection
- Firewall and filter evasion techniques
- Large-scale network enumeration

**Port Scanning:**
- Various scanning techniques (TCP, UDP, SYN, etc.)
- Service version detection
- Operating system fingerprinting

**Service Enumeration:**
- Banner grabbing and service identification
- Vulnerability scanning integration
- Custom script execution (NSE)

### Essential Nmap Commands

**Basic Scanning:**
```bash
# Basic port scan
nmap target.com

# Scan specific ports
nmap -p 22,80,443 target.com

# Scan port range
nmap -p 1-1000 target.com

# Scan all ports
nmap -p- target.com
```

**Advanced Options:**
```bash
# Service version detection
nmap -sV target.com

# Operating system detection
nmap -O target.com

# Aggressive scan (OS, version, scripts, traceroute)
nmap -A target.com

# Verbose output
nmap -v target.com
```

**Timing and Performance:**
```bash
# Timing templates (T0-T5)
nmap -T4 target.com

# Custom timing
nmap --min-rate 1000 --max-rate 5000 target.com

# Parallel scanning
nmap --min-parallelism 100 target.com
```

**Output Formats:**
```bash
# Save all formats
nmap -oA scan_results target.com

# XML output
nmap -oX results.xml target.com

# Greppable output
nmap -oG results.gnmap target.com
```

### Nmap Scripting Engine (NSE)

**Script Categories:**
- **auth:** Authentication bypassing scripts
- **brute:** Brute force attack scripts
- **discovery:** Host and service discovery
- **exploit:** Exploitation scripts
- **intrusive:** Scripts that may crash services
- **malware:** Malware detection scripts
- **safe:** Scripts unlikely to crash services
- **version:** Service version detection enhancement
- **vuln:** Vulnerability detection scripts

**Script Usage:**
```bash
# Run default scripts
nmap -sC target.com

# Run specific script category
nmap --script vuln target.com

# Run specific script
nmap --script ssh-brute target.com

# Multiple scripts
nmap --script "http-*" target.com
```

### Advanced Features

**Firewall Evasion:**
```bash
# Fragment packets
nmap -f target.com

# Decoy scanning
nmap -D decoy1,decoy2,ME target.com

# Source port spoofing
nmap --source-port 53 target.com

# Idle scan (zombie host)
nmap -sI zombie_host target.com
```

**Performance Optimization:**
```bash
# Skip DNS resolution
nmap -n target.com

# Skip ping (assume host up)
nmap -Pn target.com

# Fast scan (top 100 ports)
nmap -F target.com
```

Nmap's extensive feature set makes it the preferred tool for comprehensive network reconnaissance and security assessment.

---

## 4 – Scanning Methodology

### Systematic Approach to Network Scanning

A structured methodology ensures comprehensive coverage while managing time, traffic, and detection risks effectively.

### Scanning Process

**1. TCP Host Scan:**
- **Objective:** Identify all open TCP ports
- **Scope:** Comprehensive TCP service enumeration
- **Priority:** High-value services and common ports

**2. UDP Host Scan:**
- **Objective:** Identify all open UDP ports  
- **Scope:** Critical UDP services (DNS, SNMP, DHCP)
- **Challenge:** UDP's stateless nature requires careful analysis

**3. Network Sweeping:**
- **Objective:** Optimize port discovery across network ranges
- **Approach:** Prioritize common services across multiple hosts
- **Efficiency:** Balance thoroughness with time constraints

### Common Challenges

**Time and Traffic Constraints:**
- **Full Port Scans:** 65,535 ports × multiple hosts = extensive time
- **Network Impact:** Large scans can saturate network links  
- **Resource Limitations:** Scanner and target system resources
- **Detection Window:** Longer scans increase discovery risk

**Security Controls:**
- **Firewall Filtering:** Packets dropped or rejected
- **Port Scan Detection:** Automated blocking of scanning sources
- **IDS/IPS Systems:** Real-time monitoring and response
- **Rate Limiting:** Throttling of connection attempts

**Mitigation Strategies:**
- **Timing Optimization:** Balance speed with stealth
- **Fragmentation:** Break up large scans into smaller chunks
- **Source Rotation:** Use multiple scanning sources
- **Protocol Variation:** Combine different scanning techniques

### Prioritized Scanning Approach

**Phase 1 - Quick Discovery:**
```bash
# Fast scan of top ports across network
nmap -T4 -F 192.168.1.0/24
```

**Phase 2 - Service Identification:**
```bash
# Version detection on discovered services
nmap -sV -T3 discovered_hosts
```

**Phase 3 - Comprehensive Enumeration:**
```bash
# Full port scan on high-value targets
nmap -p- -T2 critical_hosts
```

**Phase 4 - UDP Scanning:**
```bash
# Targeted UDP scan for specific services
nmap -sU -T2 --top-ports 100 target_hosts
```

### Documentation and Tracking

**Scan Results Management:**
- **Consistent Naming:** Use timestamps and target identifiers
- **Format Standardization:** XML for parsing, text for review
- **Progress Tracking:** Monitor completion and coverage
- **Result Correlation:** Compare scans over time

This methodical approach ensures thorough reconnaissance while managing operational risks and resource constraints.

---

## 5 – TCP Host Scan

### Comprehensive TCP Port Enumeration

TCP host scanning forms the foundation of service discovery, requiring systematic approaches to identify all available services on target systems.

### Basic TCP Scanning Commands

**Standard TCP Scan:**
```bash
# Default port scan (top 1000 ports)
nmap target.com

# Specific port scan
nmap -p 22,80,443 target.com

# Port range scan
nmap -p 1-1000 target.com

# All TCP ports
nmap -p- target.com
```

**Advanced TCP Scanning:**
```bash
# SYN scan with version detection
nmap -sS -sV target.com

# TCP connect scan
nmap -sT target.com

# Comprehensive scan with OS detection
nmap -sS -sV -O target.com
```

### Importance of Full Port Scanning

**Why Scan All Ports:**
- **Service Migration:** Services often run on non-standard ports
- **Security Through Obscurity:** Administrators may change default ports
- **Custom Applications:** Internal services use arbitrary port numbers
- **Backdoors and Trojans:** Malware often uses uncommon ports

**Example - SSH Port Discovery:**
```bash
# Standard scan might miss SSH on custom port
nmap target.com
# Result: Only finds HTTP (80) and HTTPS (443)

# Full port scan reveals the complete picture
nmap -p- target.com  
# Result: Finds SSH on port 2222, HTTP (80), HTTPS (443)
```

**Real-World Scenario:**
```bash
# Quick scan - misses critical services
nmap -F target.com
PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https

# Full scan - reveals complete attack surface
nmap -p- target.com
PORT     STATE SERVICE
22/tcp   closed ssh
80/tcp   open   http  
443/tcp  open   https
2222/tcp open   ssh      # SSH on non-standard port
3306/tcp open   mysql    # Database server
5432/tcp open   postgresql
8080/tcp open   http-alt # Additional web service
8443/tcp open   https-alt
```

### Optimization Strategies

**Balancing Speed and Coverage:**
```bash
# Fast initial assessment
nmap -T4 --top-ports 100 target.com

# Follow up with full scan on interesting targets
nmap -p- -T3 target.com

# Service detection on discovered ports
nmap -sV -p discovered_ports target.com
```

**Parallel Scanning:**
```bash
# Multiple hosts simultaneously  
nmap -p- 192.168.1.1-10

# Network range with threading
nmap -T4 -p- 192.168.1.0/24
```

### Service Fingerprinting

**Version Detection:**
```bash
# Basic version detection
nmap -sV target.com

# Aggressive version detection
nmap -sV --version-intensity 9 target.com

# Version detection with scripts
nmap -sV -sC target.com
```

**Banner Grabbing:**
```bash
# Custom banner grab script
nmap --script banner target.com

# HTTP title detection
nmap --script http-title target.com
```

### Documentation and Analysis

**Output Management:**
```bash
# Save comprehensive results
nmap -p- -sV -oA full_scan target.com

# Parse results for specific services
grep "open" full_scan.gnmap | grep "ssh"
```

Comprehensive TCP scanning ensures no services are missed and provides the complete attack surface for subsequent exploitation phases.

---

## 6 – UDP Host Scan

### Understanding UDP Scanning Complexity

UDP scanning presents unique challenges due to the protocol's stateless nature, requiring specialized techniques and careful interpretation of results.

### UDP Protocol Ambiguity

UDP's connectionless design creates inherent ambiguity in port state determination:

### UDP Scan Response Analysis

Based on the provided response table:

| **Port State** | **Response Type** |
|----------------|-------------------|
| **Open Port** | **No Response** |
| **Closed Port** | **ICMP Port Unreachable** |
| **Filtered Port (DROP)** | **No Response** |
| **Filtered Port (REJECT)** | **ICMP Port Unreachable** |

### The UDP Scanning Challenge

**Response Ambiguity:**
- **Open Port:** Service listening but may not respond to empty UDP packets
- **Filtered Port:** Firewall dropping packets (appears identical to open)
- **No Response:** Could indicate open, filtered, or rate-limited

**Rate Limiting Impact:**
- **ICMP Rate Limiting:** Many systems limit ICMP error responses
- **False Negatives:** Closed ports may appear open due to rate limiting
- **Timing Issues:** Responses may be delayed or dropped

### Advanced UDP Scanning with Nmap

**Enhanced UDP Scan Command:**
```bash
nmap -v -sUV -p ports target_ip
```

**Parameter Breakdown:**
- **-v:** Verbose output for detailed analysis
- **-sU:** UDP scan mode
- **-V:** Version detection using NSE scripts
- **-p ports:** Specify target ports

### How NSE Scripts Improve UDP Scanning

**Script-Based Validation:**
- **Service-Specific Probes:** Sends protocol-appropriate requests
- **Response Analysis:** Interprets service-specific responses
- **State Verification:** Distinguishes between open and filtered ports

**Example Script Interactions:**
```bash
# DNS service detection (port 53)
nmap -sUV -p 53 target.com
# Sends DNS query, validates response

# SNMP service detection (port 161)  
nmap -sUV -p 161 target.com
# Sends SNMP requests, checks for valid responses

# DHCP service detection (port 67)
nmap -sUV -p 67 target.com
# Sends DHCP discover packets
```

### Practical UDP Scanning Examples

**Common UDP Services:**
```bash
# Scan critical UDP services
nmap -sUV -p 53,67,68,123,161,162,500,514,1701,4500 target.com

# Top UDP ports scan
nmap -sU --top-ports 100 target.com

# Comprehensive UDP scan (slow)
nmap -sU -p- target.com
```

**Service-Specific Scripts:**
```bash
# DNS enumeration
nmap -sU -p 53 --script dns-* target.com

# SNMP enumeration  
nmap -sU -p 161 --script snmp-* target.com

# NTP information gathering
nmap -sU -p 123 --script ntp-* target.com
```

### Optimization Strategies

**Timing Considerations:**
```bash
# Slow timing for accurate results
nmap -sU -T2 target.com

# Parallel host scanning
nmap -sU -T3 --min-parallelism 10 target_range

# Custom timing
nmap -sU --scan-delay 1000ms target.com
```

**Targeted Approach:**
```bash
# Focus on high-probability services
nmap -sUV -p 53,161,500,1701 target.com

# Quick UDP discovery
nmap -sU -F target.com
```

### Interpreting UDP Results

**Result Analysis:**
- **open|filtered:** Most common result, requires script validation
- **open:** Confirmed by service response or script interaction
- **closed:** ICMP port unreachable received
- **filtered:** No response and no ICMP error (likely firewalled)

**Validation Techniques:**
```bash
# Confirm with service-specific tools
dig @target.com example.com          # Validate DNS (port 53)
snmpwalk -v1 -c public target.com     # Validate SNMP (port 161)
```
