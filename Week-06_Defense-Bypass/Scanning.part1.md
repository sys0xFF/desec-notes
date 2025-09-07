# Scanning and Network Reconnaissance

> **Focus:** Network scanning techniques for host discovery, route tracking, firewall detection, and port enumeration through comprehensive network analysis and defense bypass methods.

---

## Table of Contents

1. [Route Tracking - Theory](#1-route-tracking---theory)
2. [Route Tracking - Practical](#2-route-tracking---practical)
3. [Firewall Overview](#3-firewall-overview)
4. [Active Host Discovery - Ping Sweep](#4-active-host-discovery---ping-sweep)
5. [Technical Study - Ping Sweep](#5-technical-study---ping-sweep)
6. [Active Host Discovery - Internal Pentest](#6-active-host-discovery---internal-pentest)
7. [Active Host Discovery - Nmap](#7-active-host-discovery---nmap)
8. [Port Scanning Introduction](#8-port-scanning-introduction)
9. [Technical Study - Port Scanning](#9-technical-study---port-scanning)

---

## 1 – Route Tracking - Theory

### Understanding Route Tracking

**Route tracking** is a fundamental network reconnaissance technique used to identify the path that packets take from source to destination, revealing network infrastructure and potential security controls.

### Core Objectives

**Path Discovery:**
- **Network Topology:** Map the route packets traverse
- **Hop Identification:** Identify intermediate routers and gateways
- **Infrastructure Mapping:** Understand network architecture
- **Bottleneck Detection:** Find potential network constraints

**Security Analysis:**
- **Filter Detection:** Identify firewalls and security devices
- **Blocking Mechanisms:** Discover packet filtering rules
- **Protocol Analysis:** Determine which packets are accepted
- **Access Control Mapping:** Understand network security policies

### How Route Tracking Works

**TTL (Time To Live) Mechanism:**
- Each IP packet contains a TTL field that decrements at each hop
- When TTL reaches 0, the router discards the packet
- Router sends an ICMP Time Exceeded message back to sender
- ICMP Type 11, Code 0 indicates TTL exceeded

**Route Discovery Process:**
1. Send packet with TTL=1 to first hop
2. Receive ICMP Time Exceeded from first router
3. Send packet with TTL=2 to second hop
4. Continue incrementing TTL until destination reached
5. Map complete path from source to destination

### Security Implications

**Reconnaissance Value:**
- **Network Infrastructure:** Reveal internal network structure
- **Security Controls:** Identify firewall and IPS locations
- **Geographic Mapping:** Understand physical network layout
- **Vulnerability Assessment:** Find potential attack vectors

**Defense Bypass Opportunities:**
- **Protocol Selection:** Test different protocols for filtering
- **Path Manipulation:** Find alternative routes through network
- **Filter Evasion:** Identify less monitored network paths
- **Traffic Analysis:** Understand normal vs. suspicious patterns

---

## 2 – Route Tracking - Practical

### Using Traceroute for Network Reconnaissance

**Traceroute** is the primary tool for route discovery, offering multiple protocol options and advanced features for penetration testing.

### Basic Traceroute Usage

**Standard ICMP Traceroute:**
```bash
# Basic route tracing
traceroute target.com

# IPv6 traceroute
traceroute6 target.com

# Specify maximum hops
traceroute -m 15 target.com
```

### Protocol-Specific Scanning

**UDP Traceroute (Default):**
```bash
# UDP-based tracing (Linux default)
traceroute target.com

# Specify UDP port
traceroute -p 53 target.com
```

**TCP Traceroute:**
```bash
# TCP SYN packets
traceroute -T target.com

# Specific TCP port
traceroute -T -p 80 target.com
traceroute -T -p 443 target.com
```

**ICMP Traceroute:**
```bash
# ICMP Echo Request
traceroute -I target.com
```

### Advanced Traceroute Parameters

**Timing and Performance:**
```bash
# Adjust wait time between probes
traceroute -w 5 target.com

# Number of probes per hop
traceroute -q 1 target.com

# Packet size modification
traceroute target.com 1500
```

**Source and Interface Control:**
```bash
# Specify source interface
traceroute -i eth0 target.com

# Set source address
traceroute -s 192.168.1.100 target.com
```

### Penetration Testing Applications

**Protocol Acceptance Testing:**
- **HTTP/HTTPS Ports:** Test web traffic filtering
- **DNS Ports:** Check DNS resolution paths
- **Mail Ports:** Verify email traffic routing
- **Custom Ports:** Test application-specific protocols

**Firewall Detection:**
```bash
# Test common ports for filtering
traceroute -T -p 22 target.com    # SSH
traceroute -T -p 23 target.com    # Telnet
traceroute -T -p 135 target.com   # Windows RPC
traceroute -T -p 445 target.com   # SMB
```

**Load Balancer Discovery:**
```bash
# Multiple traces to same destination
for i in {1..5}; do
    traceroute target.com
    sleep 1
done
```

### Alternative Tools

**MTR (My Traceroute):**
```bash
# Continuous network monitoring
mtr target.com

# Report mode with packet loss
mtr -r -c 10 target.com
```

**Windows Tracert:**
```cmd
# Windows equivalent
tracert target.com

# IPv6 tracing
tracert -6 target.com
```

Route tracking provides essential intelligence about network infrastructure and security controls, enabling informed decisions for subsequent penetration testing phases.

---

## 3 – Firewall Overview

### Understanding iptables for Defense

**iptables** is the standard Linux firewall system that controls network traffic through rules and chains, providing essential insights for understanding defensive mechanisms.

### Basic iptables Concepts

**Tables and Chains:**
- **Filter Table:** Default table for packet filtering
- **INPUT Chain:** Incoming packets to local system
- **OUTPUT Chain:** Outgoing packets from local system
- **FORWARD Chain:** Packets routed through system

### Common iptables Commands

**Viewing Rules:**
```bash
# List all rules
iptables -L

# List with line numbers
iptables -L --line-numbers

# Show packet/byte counters
iptables -L -v

# List specific chain
iptables -L INPUT
```

**Basic Rule Management:**
```bash
# Add rule to accept SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Insert rule at specific position
iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT

# Delete specific rule
iptables -D INPUT -p tcp --dport 23 -j DROP
```

### Traffic Control Actions

**ACCEPT vs DROP vs REJECT:**
```bash
# Allow traffic (responds normally)
iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Silently discard packets (no response)
iptables -A INPUT -p tcp --dport 23 -j DROP

# Reject with error message
iptables -A INPUT -p tcp --dport 21 -j REJECT
```

### Protocol-Specific Filtering

**ICMP Control:**
```bash
# Block all ICMP
iptables -A INPUT -p icmp -j DROP

# Allow ping but block other ICMP
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A INPUT -p icmp -j DROP
```

**TCP/UDP Filtering:**
```bash
# Block specific TCP ports
iptables -A INPUT -p tcp --dport 135:139 -j DROP

# Allow established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

### Source-Based Filtering

**IP Address Control:**
```bash
# Block specific IP
iptables -A INPUT -s 192.168.1.100 -j DROP

# Allow specific subnet
iptables -A INPUT -s 10.0.0.0/8 -j ACCEPT

# Block entire country (with GeoIP module)
iptables -A INPUT -m geoip --src-cc CN -j DROP
```

### Understanding Defensive Strategies

**Common Firewall Configurations:**
- **Default Deny:** Block all traffic except explicitly allowed
- **Port Knocking:** Require specific sequence to open ports
- **Rate Limiting:** Prevent brute force and DoS attacks
- **Stealth Mode:** Drop packets to avoid revealing services

This foundational understanding of firewall mechanisms is crucial for developing effective bypass techniques and understanding target network defenses.

---

## 4 – Active Host Discovery - Ping Sweep

### Understanding Ping Sweep

**Ping Sweep** is a network reconnaissance technique that systematically sends ICMP echo requests to a range of IP addresses to identify active hosts on a network.

### Core Concept

**Basic Methodology:**
- Send ICMP Echo Request packets to multiple IP addresses
- Analyze ICMP Echo Reply responses to identify active hosts
- Map live systems across network ranges
- Build target list for further enumeration

### Simple Bash Implementation

**Basic Ping Sweep Script:**
```bash
#!/bin/bash

# Ping sweep for network range
network="192.168.1"

echo "Starting ping sweep for $network.0/24"

for ip in {1..254}; do
    # Send single ping with 1 second timeout
    ping -c 1 -W 1 "$network.$ip" > /dev/null 2>&1
    
    # Check if ping was successful
    if [ $? -eq 0 ]; then
        echo "$network.$ip is active"
    fi
done

echo "Ping sweep completed"
```

**Enhanced Version with Threading:**
```bash
#!/bin/bash

network="192.168.1"

# Function to ping single host
ping_host() {
    ip="$network.$1"
    if ping -c 1 -W 1 "$ip" > /dev/null 2>&1; then
        echo "$ip is active"
    fi
}

echo "Starting threaded ping sweep for $network.0/24"

# Run pings in parallel
for ip in {1..254}; do
    ping_host "$ip" &
done

# Wait for all background jobs to complete
wait

echo "Ping sweep completed"
```

### Applications in Penetration Testing

**Network Mapping:**
- **Subnet Discovery:** Identify populated network segments
- **Host Inventory:** Build comprehensive target lists
- **Network Size Assessment:** Understand environment scope
- **Live System Identification:** Focus efforts on active targets

**Reconnaissance Benefits:**
- **Quick Overview:** Rapid network assessment
- **Baseline Establishment:** Understand normal network activity
- **Target Prioritization:** Identify high-value systems
- **Attack Surface Mapping:** Plan subsequent enumeration phases

### Limitations and Considerations

**Detection Avoidance:**
- **Rate Limiting:** Slow down scan to avoid detection
- **Randomization:** Vary timing and order of requests
- **Source Spoofing:** Use different source addresses
- **Protocol Variation:** Combine with other discovery methods

**Firewall Bypass:**
- ICMP blocking is common in modern networks
- Alternative protocols may be necessary
- Multiple techniques should be combined for comprehensive coverage

Ping sweep provides a foundation for network discovery, though modern defenses often require more sophisticated techniques for complete host enumeration.

---

## 5 – Technical Study - Ping Sweep

### Firewall Behavior Analysis

Understanding how different firewall configurations respond to ICMP requests is crucial for effective reconnaissance and evasion techniques.

### DROP vs REJECT Configuration Testing

**iptables DROP Configuration:**
```bash
# Server-side firewall rule
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
```

**Testing DROP Behavior:**
```bash
# Ping command against DROP-configured server
ping -c 3 192.168.1.100

# Expected behavior:
# - No response received
# - Packets appear to be lost
# - Timeout occurs after default wait period
# - No error message generated
```

**Example OUTPUT - DROP:**
```
PING 192.168.1.100 (192.168.1.100) 56(84) bytes of data.

--- 192.168.1.100 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2040ms
```

### REJECT Configuration Analysis

**iptables REJECT Configuration:**
```bash
# Server-side firewall rule
iptables -A INPUT -p icmp --icmp-type echo-request -j REJECT --reject-with icmp-port-unreachable
```

**Testing REJECT Behavior:**
```bash
# Ping command against REJECT-configured server
ping -c 3 192.168.1.100

# Expected behavior:
# - Active rejection response received
# - ICMP error message returned
# - Immediate failure notification
# - Faster scan completion
```

**Example OUTPUT - REJECT:**
```
PING 192.168.1.100 (192.168.1.100) 56(84) bytes of data.
From 192.168.1.100 icmp_seq=1 Destination Port Unreachable
From 192.168.1.100 icmp_seq=2 Destination Port Unreachable
From 192.168.1.100 icmp_seq=3 Destination Port Unreachable

--- 192.168.1.100 ping statistics ---
3 packets transmitted, 0 received, +3 errors, 100% packet loss, time 2002ms
```

### Reconnaissance Implications

**DROP Configuration Analysis:**
- **Stealth Operation:** Attempts to hide system presence
- **Slower Discovery:** Requires timeout periods for definitive results
- **Resource Consumption:** Attackers must wait for timeouts
- **Defensive Strategy:** Makes network mapping more time-consuming

**REJECT Configuration Analysis:**
- **Active Response:** Confirms system presence and firewall activity
- **Faster Scanning:** Immediate feedback allows rapid enumeration
- **Information Disclosure:** Reveals firewall rules and policies
- **Less Stealthy:** Generates logs and network activity

### Advanced Analysis Techniques

**Packet Capture Analysis:**
```bash
# Capture ICMP traffic during testing
tcpdump -i eth0 icmp -v

# Analysis for DROP:
# - Outgoing echo requests visible
# - No incoming responses
# - No error messages

# Analysis for REJECT:
# - Outgoing echo requests visible
# - Incoming ICMP error messages
# - Clear rejection indicators
```

**Timing Analysis:**
```bash
# Measure response times
time ping -c 1 -W 1 target_drop_ip
time ping -c 1 -W 1 target_reject_ip

# DROP: Full timeout period (1 second)
# REJECT: Immediate response (<100ms typically)
```

### Evasion Strategy Development

**Based on Firewall Behavior:**
- **DROP Detection:** Long timeouts indicate possible DROP rules
- **REJECT Detection:** Error messages confirm active filtering
- **Timing Optimization:** Adjust scan timing based on response patterns
- **Protocol Selection:** Choose alternative discovery methods for dropped protocols

Understanding these fundamental differences enables more effective reconnaissance strategies and proper interpretation of scan results.

---

## 6 – Active Host Discovery - Internal Pentest

### ARP Protocol for Internal Reconnaissance

**ARP (Address Resolution Protocol)** operates at Layer 2 of the OSI model, making it particularly effective for internal network reconnaissance and firewall bypass.

### Understanding ARP Fundamentals

**ARP Protocol Overview:**
- **Layer 2 Operation:** Works at the data link layer
- **Local Network Scope:** Limited to same broadcast domain
- **MAC Address Resolution:** Maps IP addresses to hardware addresses
- **Broadcast Nature:** ARP requests are sent to all hosts on subnet

**Why ARP Bypasses Firewalls:**
- **Below IP Layer:** Operates beneath typical firewall filtering
- **Essential Protocol:** Cannot be blocked without breaking network functionality
- **Local Network Only:** Not routed between subnets
- **Broadcast Communication:** Reaches all hosts regardless of IP filtering

### Using ARPING for Host Discovery

**Basic ARPING Usage:**
```bash
# Ping specific host via ARP
arping 192.168.1.100

# Single ARP request
arping -c 1 192.168.1.100

# Specify interface
arping -I eth0 192.168.1.100
```

**Advanced ARPING Options:**
```bash
# Quiet mode (only show results)
arping -q -c 1 192.168.1.100

# Set timeout
arping -W 1 -c 1 192.168.1.100

# Use specific source IP
arping -S 192.168.1.50 192.168.1.100
```

### ARP Sweep Implementation

**Basic ARP Sweep Script:**
```bash
#!/bin/bash

network="192.168.1"
interface="eth0"

echo "Starting ARP sweep for $network.0/24 on $interface"

for ip in {1..254}; do
    target="$network.$ip"
    
    # Send single ARP request
    if arping -I "$interface" -c 1 -W 1 "$target" > /dev/null 2>&1; then
        # Get MAC address for identified host
        mac=$(arping -I "$interface" -c 1 "$target" | grep -oE '([0-9a-fA-F]{2}:){5}[0-9a-fA-F]{2}')
        echo "$target is active (MAC: $mac)"
    fi
done

echo "ARP sweep completed"
```

**Enhanced Version with Vendor Identification:**
```bash
#!/bin/bash

network="192.168.1"
interface="eth0"

echo "Starting enhanced ARP sweep for $network.0/24"

for ip in {1..254}; do
    target="$network.$ip"
    
    result=$(arping -I "$interface" -c 1 -W 1 "$target" 2>/dev/null)
    
    if [ $? -eq 0 ]; then
        mac=$(echo "$result" | grep -oE '([0-9a-fA-F]{2}:){5}[0-9a-fA-F]{2}')
        
        # Extract vendor from MAC (first 3 octets)
        vendor_prefix=$(echo "$mac" | cut -d: -f1-3)
        
        echo "$target is active"
        echo "  MAC Address: $mac"
        echo "  Vendor OUI: $vendor_prefix"
        echo ""
    fi
done
```

### Advantages for Internal Pentesting

**Firewall Evasion:**
- **Layer 2 Operation:** Bypasses most IP-based filtering
- **Essential Protocol:** Cannot be blocked without network disruption
- **No Routing:** Direct communication within broadcast domain
- **Stealth Operation:** Normal network protocol, less suspicious

**Comprehensive Discovery:**
- **All Active Hosts:** Discovers systems regardless of IP filtering
- **MAC Address Intelligence:** Hardware vendor identification
- **Network Mapping:** Complete local segment enumeration
- **Infrastructure Assessment:** Identify network devices and systems

### Alternative ARP Tools

**nmap ARP Discovery:**
```bash
# ARP-based host discovery
nmap -PR 192.168.1.0/24

# Combine with port scan
nmap -PR -sn 192.168.1.0/24
```

**arp-scan Tool:**
```bash
# Dedicated ARP scanning tool
arp-scan 192.168.1.0/24

# Verbose output with vendor info
arp-scan -v 192.168.1.0/24
```

ARP-based discovery is particularly valuable in internal penetration tests where ICMP may be filtered but Layer 2 communication remains unrestricted.

---

## 7 – Active Host Discovery - Nmap

### Nmap Host Discovery Overview

**Nmap** is the industry standard for network discovery and port scanning, offering sophisticated host detection methods that work even when ICMP is blocked.

### Understanding nmap -sn

**Host Discovery Mode:**
```bash
# Basic host discovery (no port scan)
nmap -sn 192.168.1.0/24
```

**What -sn Actually Does:**
- **Multiple Probe Types:** Uses various techniques simultaneously
- **ICMP Echo Request:** Traditional ping method
- **TCP SYN to Port 443:** HTTPS connectivity test
- **TCP ACK to Port 80:** HTTP connectivity test  
- **ICMP Timestamp Request:** Alternative ICMP method

### Advanced Host Discovery Techniques

**Specific Probe Methods:**
```bash
# ICMP-only discovery
nmap -sn -PE 192.168.1.0/24

# TCP SYN discovery
nmap -sn -PS22,80,443 192.168.1.0/24

# TCP ACK discovery
nmap -sn -PA80,443 192.168.1.0/24

# UDP discovery
nmap -sn -PU53,161 192.168.1.0/24
```

**Comprehensive Discovery:**
```bash
# Combine multiple methods
nmap -sn -PE -PS22,80,443 -PA80,443 -PU53 192.168.1.0/24
```

### Why Nmap is Most Used

**Reliability Advantages:**
- **Multi-Protocol Approach:** Uses multiple methods simultaneously
- **Intelligent Adaptation:** Adapts to network conditions
- **Firewall Evasion:** Employs various techniques to bypass filtering
- **Comprehensive Coverage:** Detects hosts that single-method tools miss

**Professional Features:**
- **Performance Optimization:** Efficient timing and parallelization
- **Stealth Options:** Various detection avoidance techniques
- **Detailed Output:** Comprehensive reporting and logging
- **Integration Support:** Works with other security tools

### Nmap Host Discovery Process

Based on the network traffic analysis, nmap -sn performs these steps:

**1. ICMP Echo Request (Type 8):**
```
ping request id=0x60b9, seq=0/0, ttl=39 (no response found)
```

**2. TCP SYN to Port 443:**
```
TCP 40 [SYN] Seq=1 Ack=1 Win=1024 Len=0
```

**3. TCP ACK to Port 80:**
```  
TCP 40 [ACK] Seq=1 Ack=1 Win=1024 Len=0
```

**4. ICMP Timestamp Request:**
```
ICMP 40 Timestamp request id=0xff45, seq=0/0, ttl=48
```

### Advanced Nmap Discovery Options

**Timing and Performance:**
```bash
# Aggressive timing
nmap -sn -T4 192.168.1.0/24

# Stealth timing  
nmap -sn -T1 192.168.1.0/24

# Custom timing
nmap -sn --min-rate 100 --max-rate 1000 192.168.1.0/24
```

**Source Control:**
```bash
# Specify source interface
nmap -sn -e eth0 192.168.1.0/24

# Spoof source address
nmap -sn -S 192.168.1.50 192.168.1.0/24

# Decoy scanning
nmap -sn -D 192.168.1.10,192.168.1.20,ME 192.168.1.0/24
```

**Output Control:**
```bash
# Detailed output
nmap -sn -v 192.168.1.0/24

# Save results
nmap -sn -oA hostscan 192.168.1.0/24

# List targets only
nmap -sn --unprivileged 192.168.1.0/24
```

### Integration with Port Scanning

**Combined Discovery and Scanning:**
```bash
# Discover hosts then scan common ports
nmap -Pn --top-ports 1000 $(nmap -sn 192.168.1.0/24 | grep -oP '(\d+\.){3}\d+')
```

Nmap's multi-method approach makes it the most reliable tool for host discovery across diverse network environments and security configurations.

---

## 8 – Port Scanning Introduction

### Understanding the TCP Three-Way Handshake for Port Scanning

**Port scanning** fundamentally relies on the TCP Three-Way Handshake (3WHS) mechanism to determine port states and service availability.

### TCP Three-Way Handshake Review

**Normal Connection Process:**

**Step 1 - SYN (Synchronize):**
```
Client → Server: TCP SYN packet
Flags: SYN=1, ACK=0
Purpose: Request connection establishment
```

**Step 2 - SYN-ACK (Synchronize-Acknowledge):**
```
Server → Client: TCP SYN-ACK packet  
Flags: SYN=1, ACK=1
Purpose: Accept connection and acknowledge request
```

**Step 3 - ACK (Acknowledge):**
```
Client → Server: TCP ACK packet
Flags: SYN=0, ACK=1  
Purpose: Confirm connection establishment
```

### Port Scanning Application of 3WHS

**TCP SYN Scanning (Half-Open):**
- **Step 1:** Send SYN packet to target port
- **Step 2a:** Receive SYN-ACK = Port is OPEN
- **Step 2b:** Receive RST = Port is CLOSED  
- **Step 3:** Send RST to abort connection (stealth)

**Advantages of SYN Scanning:**
- **Stealth Operation:** Never completes full connection
- **Fast Performance:** No need to establish full connections
- **Reduced Logging:** Many systems don't log incomplete connections
- **Resource Efficient:** Minimal server resource consumption

### Port State Interpretation

**OPEN Port Response:**
```
Client: SYN → Port 80
Server: SYN-ACK ← Port 80
Result: Service is listening and accepting connections
```

**CLOSED Port Response:**
```
Client: SYN → Port 8080
Server: RST-ACK ← Port 8080  
Result: No service listening, port actively rejected
```

**FILTERED Port Response:**
```
Client: SYN → Port 135
Server: (no response)
Result: Firewall/filter dropping packets
```

### Alternative Scanning Techniques

**TCP Connect Scan:**
- Completes full 3WHS
- More reliable but less stealthy
- Generates more logs
- Requires higher privileges

**TCP ACK Scan:**
- Sends ACK packets to test filtering
- Used for firewall rule detection
- Doesn't identify open ports
- Useful for identifying filtering devices

**TCP FIN Scan:**
- Sends FIN packets to closed connections
- Bypasses some simple firewalls
- Relies on RFC compliance
- Less reliable than SYN scanning

### Practical Implementation

**Manual Port Testing:**
```bash
# Test specific port with netcat
nc -zv target.com 80

# Test port range  
nc -zv target.com 80-443

# UDP port testing
nc -zuv target.com 53
```

**Understanding Responses:**
- **Connection Successful:** Port is open
- **Connection Refused:** Port is closed
- **Timeout/No Response:** Port is filtered

The TCP handshake mechanism provides the foundation for all TCP-based port scanning techniques, enabling precise determination of service availability and network security posture.

---

## 9 – Technical Study - Port Scanning

### Comprehensive Port Scanning Analysis with Nmap

Understanding how different firewall configurations affect port scanning results is crucial for accurate reconnaissance and proper interpretation of scan data.

### Nmap Command Analysis

**Standard Scanning Command:**
```bash
nmap -sS -p [port] -Pn [target] --reason
```

**Parameter Breakdown:**
- **-sS:** TCP SYN scan (stealth scan)
- **-p [port]:** Specify target port(s)
- **-Pn:** Skip host discovery (assume host is up)
- **--reason:** Show reason for port state determination

### Port State Analysis with Different Firewall Rules

### Open Port Response

**Firewall Configuration:**
```bash
# No blocking rule - service listening
# Port 80 with Apache/Nginx running
```

**Nmap Output:**
```
PORT   STATE SERVICE REASON
80/tcp open  http    syn-ack ttl 64
```

**Network Traffic:**
```
Client → Server: SYN packet to port 80
Server → Client: SYN-ACK packet from port 80
```

**Analysis:** Service is actively listening and responds with SYN-ACK, confirming open port.

---

### Closed Port Response  

**Firewall Configuration:**
```bash
# No service running on port, no blocking rule
```

**Nmap Output:**
```
PORT     STATE  SERVICE REASON
8080/tcp closed http-alt rst ttl 64
```

**Network Traffic:**
```
Client → Server: SYN packet to port 8080  
Server → Client: RST-ACK packet
```

**Analysis:** No service listening, system actively rejects connection with RST.

---

### Filtered Port - DROP Rule

**Firewall Configuration:**
```bash
iptables -A INPUT -p tcp --dport 22 -j DROP
```

**Nmap Output:**
```
PORT   STATE    SERVICE REASON
22/tcp filtered ssh     no-responses
```

**Network Traffic:**
```
Client → Server: SYN packet to port 22
Server → Client: (no response)
```

**Analysis:** Packets silently discarded, no response indicates filtering.

---

### Filtered Port - REJECT Rule

**Firewall Configuration:**
```bash
iptables -A INPUT -p tcp --dport 23 -j REJECT --reject-with icmp-port-unreachable
```

**Nmap Output:**
```
PORT   STATE    SERVICE REASON
23/tcp filtered telnet  port-unreaches
```

**Network Traffic:**
```
Client → Server: SYN packet to port 23
Server → Client: ICMP Port Unreachable
```

**Analysis:** Active rejection with ICMP error message indicates REJECT rule.

---

### Response Summary Table

Based on the provided firewall response table:

| **Port State** | **Response Type** |
|----------------|-------------------|
| **Open Port** | **SYN/ACK (SA)** |
| **Closed Port** | **RST/ACK (RA)** |
| **Filtered Port (DROP)** | **No Response** |
| **Filtered Port (REJECT)** | **ICMP Port Unreachable** |
| **Filtered Port (REJECT with RST)** | **Treated as Closed Port (RST)** |

### Advanced Scanning Scenarios

**Multiple Port Analysis:**
```bash
# Scan common ports with reasons
nmap -sS -p 22,23,80,443 -Pn target --reason

# Example mixed results:
PORT    STATE    SERVICE  REASON
22/tcp  filtered ssh      no-responses
23/tcp  filtered telnet   port-unreaches  
80/tcp  open     http     syn-ack ttl 64
443/tcp closed   https    rst ttl 64
```

**Timing Analysis:**
```bash
# Fast scan for filtered port detection
nmap -sS -T4 -p 1-1000 target --reason

# Filtered ports will show longer response times
# Open/Closed ports respond immediately
```

### Interpretation Guidelines

**Security Assessment:**
- **Open Ports:** Identify running services for further enumeration
- **Closed Ports:** Confirm system presence but no service
- **Filtered Ports:** Indicate security controls and firewall rules
- **No Response:** May indicate DROP rules or network issues

**Evasion Planning:**
- **DROP Detection:** Long timeouts suggest packet dropping
- **REJECT Detection:** ICMP errors confirm active filtering  
- **Mixed Results:** Indicates selective filtering policies
- **Timing Patterns:** Help identify rate limiting and detection systems

Understanding these fundamental response patterns enables accurate assessment of target systems and proper planning for subsequent penetration testing phases.