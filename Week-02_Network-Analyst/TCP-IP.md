
# Introduction to TCP/IP Networks

## Computer Networks

Communication between two or more computers over a network.

- **Host**: Name assigned to a computer in the network.
- **Server**: Provides services on the network.
- **Client**: Consumes services provided by the server.

---

## Protocols

Protocols ensure that different computers with different hardware and software can communicate effectively.

> Example: TCP/IP protocol

Communication only happens because both sides understand the same protocol.

---

## Physical and Logical Addresses & Ports

- **MAC Address**: Physical address assigned by the manufacturer.
- **IP Address**: Logical address assigned depending on the network.
- **Ports**: Identifiers for services on a host.

```text
Client
MAC:       a4:5e:50:b8:c1:af
IP:        192.168.0.102
Port:      50234
```

---

## MAC Address

Composed of 6 bytes, split between manufacturer and unique sequence:

```
A4:5E:50   B8:C1:AF
(Manufacturer) (Unique sequence)
```

Lookup tools:

- http://standards-oui.ieee.org/oui/oui.txt
- https://macvendors.com/

---

## IP Address

Composed of 4 octets in decimal:

Example: `192.168.0.200`

- `192.168.0` – Network
- `200` – Host

### Subnet Mask

Defines the division between network and host:
```
255.255.255.0
```

---

## Routing & Gateways

Different networks communicate using **routing**, which requires a **gateway**.

```ascii
+----------+         +-----------+         +----------+
|  PC A    | <-----> |  Router   | <-----> |  PC B    |
|192.168.0.2|         |192.168.0.1|         |10.0.0.2  |
+----------+         +-----------+         +----------+
```

---

## Ports

Range: 0–65535  
Used with TCP and UDP protocols

Examples:
```
80    = HTTP     (TCP)
443   = HTTPS    (TCP)
25    = SMTP     (TCP)
161   = SNMP     (UDP)
23    = TELNET   (TCP)
21    = FTP      (TCP)
22    = SSH      (TCP)
```

> Services can use custom ports (e.g. SSH on 22222)

**Socket = IP:PORT**  
Example: `192.168.0.200:8080`

---

## Summary

Two computers with different systems can communicate over a network using common protocols (like TCP/IP).  
Each has:

- MAC address (physical)
- IP address (logical)
- Ports (services)

If they’re in different networks, routing is required using a gateway.

---

## Network Interfaces - Practice

### `ifconfig eth0`

```bash
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.200  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 2804:14d:7841:828a:20c:29ff:fe76:43e1  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::f48e:bfff:fe7f:4623  prefixlen 64  scopeid 0x20<link>
        inet6 2804:14d:7841:828a:f48e:bfff:fe7f:4623  prefixlen 64  scopeid 0x0<global>
        ether 00:0c:29:76:43:e1  txqueuelen 1000  (Ethernet)
        RX packets 23521  bytes 21340184 (20.3 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2436  bytes 263508 (257.3 KiB)
        TX errors 0  dropped 0  overruns 0  carrier 0  collisions 0
```

You can see:

- Logical IP
- Physical MAC
- Netmask

To change MAC:

```bash
macchanger -r eth0
```

To start a service and become a server:

```bash
service apache2 start
netstat -nlpt | grep 80
```

Output:

```
tcp6  0  0  :::80  :::*  LISTEN  1635/apache2
```

---

## Routing & ipcalc

View routing table:

```bash
route -n
```

Add default gateway:

```bash
route add default gw 192.168.0.1
```

Remove route:

```bash
route del default gw 192.168.0.1
```

Use `ipcalc` to understand IP class and subnetting:

```bash
ipcalc 192.168.0.200/24
```

Output:

```
Address:   192.168.0.200
Netmask:   255.255.255.0 = 24
Wildcard:  0.0.0.255
Network:   192.168.0.0/24
Broadcast: 192.168.0.255
HostMin:   192.168.0.1
HostMax:   192.168.0.254
Hosts/Net: 254
```

---

## Network Protocols Structure

Protocols contain:

- **Header**: Protocol-specific metadata
- **Payload**: Transmitted data

---

## Reference Models

### OSI Model (7 Layers)

```
Application
Presentation
Session
Transport
Network
Data Link
Physical
```

### TCP/IP Model (4 Layers)

```
Application
Transport
Internet
Network Access
```

### Hybrid Model

```
Application
Transport
Network
Link
Physical
```

---

## Encapsulation Example

```
+----------------------+
|    HTTP Data         |
+----------------------+
|  TCP Header + Data   |
+----------------------+
|  IP Header + TCP     |
+----------------------+
|Ethernet Header + IP  |
+----------------------+
```

---

## Protocol Analyzer Tools

### Wireshark

Graphical tool to capture and analyze network traffic in real-time.  
It decodes various protocols, shows packet structure, and helps investigate network issues.

### TCPDump

Command-line tool to capture packets.  
Examples:

```bash
tcpdump -i eth0
tcpdump port 80
tcpdump -nnvvXSs 1514 -i eth0
```

---

## Ethernet Protocol Structure

| MAC Destination | MAC Source | Type | Payload | Checksum |

- Destination: Target MAC
- Source: Sender MAC
- Type: Protocol ID (e.g., `0800 = IP`, `0806 = ARP`)
- Payload: Data carried
- Max Payload: 1500 bytes

---

## Practical Example (ARP Broadcast)

```text
Ethernet II, Src: Vmware_76:43:e1 (00:0c:29:76:43:e1), Dst: Broadcast (ff:ff:ff:ff:ff:ff)
    Destination: Broadcast (ff:ff:ff:ff:ff:ff)
    Source: Vmware_76:43:e1 (00:0c:29:76:43:e1)
    Type: ARP (0x0806)
    Address Resolution Protocol (request)
```

| Destination MAC     | Source MAC        | Type | Protocol | Checksum |
|---------------------|-------------------|------|----------|----------|
| ff:ff:ff:ff:ff:ff    | 00:0c:29:76:43:e1 | 0806 | ARP      | [calc]   |

---

