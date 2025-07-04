# Bash Scripting and Networking

## Table of Contents
- [Introduction to Bash Scripting](#introduction-to-bash-scripting)
- [Working with Conditions](#working-with-conditions)
- [Working with Arguments](#working-with-arguments)
- [Working with Loops](#working-with-loops)
- [Practical Exercises](#practical-exercises)
  - [Ping Sweep Script](#ping-sweep-script)
  - [Network PortScan Script](#network-portscan-script)
  - [HTML Parsing for Subdomain Enumeration](#html-parsing-for-subdomain-enumeration)
- [Installation & Usage Instructions](#installation--usage-instructions)

---

## Introduction to Bash Scripting

A simple introduction to bash scripting, covering basic commands, system information, and user input handling.

### Example Script
```bash
#!/bin/bash
# My First Bash Script

# Display messages on screen
echo "DESEC SECURITY"
echo "System Uptime: " $(uptime -p)
echo "Current Directory: " $(pwd)
echo "Current User: " $(whoami)

# Handling variables and user input
echo "Enter IP:"
read ip
echo "Scanning Host: $ip"
```

---

## Working with Conditions

Conditional statements (`if`, `elif`, `else`, and `case`) are used to execute commands based on specific conditions.

### Example Script
```bash
#!/bin/bash

echo "What's the traffic light color?"
read color
if [ "$color" == "green" ]; then
    echo "Go ahead =)"
elif [ "$color" == "yellow" ]; then
    echo "Wait"
else
    echo "STOP!"
fi

echo "What's your age?"
read age
if [ "$age" -ge 18 ]; then
    echo "You are an adult"
else
    echo "You are underage"
fi

echo "Did the client authorize the Pentest?"
echo "1 - Yes"
echo "2 - No"
read resp
case $resp in
    "1")
        echo "YOU CAN START THE PENTEST"
        ;;
    "2")
        echo "YOU CANNOT START THE PENTEST"
        ;;
esac
```

---

## Working with Arguments

Passing arguments directly through the command line to scripts.

### Example Script
```bash
#!/bin/bash
if [ "$1" == "" ]; then
    echo "Usage: $0 192.168.0.1 80"
else
    echo "Scanning Host: $1 on Port: $2"
fi
```

---

## Working with Loops

Loops (`for` and `while`) allow executing commands repeatedly.

### For Loop Example
```bash
#!/bin/bash
for ip in $(seq 1 10); do
    echo "172.16.1.$ip"
done
```

### While Loop Example
```bash
#!/bin/bash
counter=1
while [ $counter -le 5 ]; do
    echo "Count: $counter"
    ((counter++))
done
```

---

## Practical Exercises

### Ping Sweep Script

**Objective:** Identify active hosts in a network using `ping`.

```bash
#!/bin/bash
if [ "$1" == "" ]; then
    echo "DESEC SECURITY - PING SWEEP"
    echo "Usage: $0 NETWORK"
    echo "Example: $0 192.168.0"
else
    for host in {1..254}; do
        ping -c 1 $1.$host | grep "64 bytes" | cut -d " " -f 4 | sed 's/.$//'
    done
fi
```

---

### Network PortScan Script

**Objective:** Discover hosts on a network with a specified open port.

```bash
#!/bin/bash
if [ "$1" == "" ] || [ "$2" == "" ]; then
    echo "DESEC SECURITY - NETWORK PORTSCAN"
    echo "Usage: $0 NETWORK PORT"
    echo "Example: $0 192.168.0 80"
else
    for ip in {1..254}; do
        hping3 -S -p $2 -c 1 $1.$ip 2> /dev/null | grep "flags=SA" | cut -d " " -f 2 | cut -d "=" -f 2
    done
fi
```

---

### HTML Parsing for Subdomain Enumeration

**Objective:** Identify potential hosts and subdomains within a given domain by parsing HTML content and resolving their IP addresses.

```bash
#!/bin/bash

# Check for domain input
if [ -z "$1" ]; then
    echo "Usage: $0 domain.com"
    exit 1
fi

DOMAIN="$1"

# Download HTML content
echo "[+] Downloading HTML from https://$DOMAIN ..."
HTML=$(curl -sL "https://$DOMAIN")

# Extract potential subdomains
echo "[+] Searching for subdomains found in HTML..."

SUBDOMAINS=$(echo "$HTML" | grep -oE "[a-zA-Z0-9._-]+\.${DOMAIN}" | sort -u)

if [ -z "$SUBDOMAINS" ]; then
    echo "No subdomains found on the page."
    exit 0
fi

# Resolve IP for each subdomain
echo
echo "[+] Resolved IP Addresses:"
for SUB in $SUBDOMAINS; do
    IP=$(dig +short "$SUB" | head -n 1)
    if [ -z "$IP" ]; then
        echo "$SUB -> IP not found"
    else
        echo "$SUB -> $IP"
    fi
done
```

---

## Installation & Usage Instructions

- **Save scripts** to a file, e.g. `scriptname.sh`.
- **Make them executable:**
```bash
chmod +x scriptname.sh
```
- **Run the script with the necessary arguments:**
```bash
./scriptname.sh [arguments]
```

---

