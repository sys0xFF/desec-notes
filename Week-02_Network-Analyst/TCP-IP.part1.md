# Complete TCP/IP Networking & Pentest Notes

## Table of Contents
- [Introduction to TCP/IP](#introduction-to-tcpip)
- [Physical & Logical Addresses](#physical--logical-addresses)
- [Routing & Gateways](#routing--gateways)
- [Protocols Overview](#protocols-overview)
- [Encapsulation & OSI](#encapsulation--osi)
- [ARP - Address Resolution Protocol](#arp---address-resolution-protocol)
- [IP - Internet Protocol](#ip---internet-protocol)
- [IP Fragmentation](#ip-fragmentation)
- [TCP - Transmission Control Protocol](#tcp---transmission-control-protocol)
- [Three-way Handshake](#three-way-handshake)
- [Network Tools & Practice](#network-tools--practice)
- [/etc/protocols](#etcprotocols)

---

## Introduction to TCP/IP

Communication between two or more computers over a network.

- **Host**: Name assigned to a computer in the network.
- **Server**: Provides services.
- **Client**: Consumes services.

## Physical & Logical Addresses

- **MAC Address** (6 bytes): Physical address burned into NIC.
- **IP Address** (4 bytes IPv4): Logical address assigned by network.

### Example
```text
Client
MAC: a4:5e:50:b8:c1:af
IP: 192.168.0.102
Port: 50234
```

## Routing & Gateways

Routers connect different networks. The gateway forwards packets not destined for the local subnet.

```ascii
+-----------+         +-----------+         +----------+
|  PC A     | <-----> |  Router   | <-----> |  PC B    |
|192.168.0.2|         |192.168.0.1|         |10.0.0.2  |
+-----------+         +-----------+         +----------+
```

## Protocols Overview

- **Ethernet**: Frames on LAN
- **ARP**: Resolves IP to MAC
- **IP**: Routes packets across networks
- **TCP/UDP**: Transport data
- **HTTP, DNS, etc**: Application layer

## Encapsulation & OSI

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

### TCP/IP Model
```
Application
Transport
Internet
Network Access
```

### Example of Encapsulation
```
+----------------------+
|    HTTP Data         |
+----------------------+
|  TCP Header + Data   |
+----------------------+
|  IP Header + TCP     |
+----------------------+
| Ethernet Header + IP |
+----------------------+
```

---

## Ethernet Protocol Structure

| MAC Destination | MAC Source | Type | Payload | Checksum |

- Destination: Target MAC
- Source: Sender MAC
- Type: Protocol ID (e.g., `0800 = IP`, `0806 = ARP`)
- Payload: Data carried
- Max Payload: 1500 bytes

## Ethernet Frame Example

| Destination MAC | Source MAC | Type | Payload | Checksum |
|-----------------|------------|------|---------|----------|
| ff:ff:ff:ff:ff:ff | 00:0c:29:76:43:e1 | 0806 | ARP | [calc] |


---

## ARP - Address Resolution Protocol

ARP discovers which MAC address belongs to a given IP on the LAN.

- **ARP Request**: Who has 192.168.0.200? Tell me your MAC.
- **ARP Reply**: I have 192.168.0.200, my MAC is xx:xx:xx:xx.

### Example with `arp -an`
```
? (192.168.0.1) at d4:ab:82:45:c3:0c [ether] on eth0
```

### Broadcast
ARP Request is sent to `ff:ff:ff:ff:ff:ff`, all nodes see it, only owner replies.

### ARP Packet Structure
```
+---------------+------------------+-------------+
| Hardware Type |          Protocol Type         |
+---------------+------------------+-------------+
| Hw addr len   | Prot addr len    |  Operation  |
+---------------+------------------+-------------+
|               Sender MAC Address               |
+---------------+------------------+-------------+
| Sender MAC Address    | Sender IP Address      |
+------------------------------------------------+
| Sender IP Address     | Target MAC Address     |
+------------------------------------------------+
|               Target MAC Address               |
+------------------------------------------------+
|               Target IP Address                |
+------------------------------------------------+
```

---

## IP - Internet Protocol

Delivers packets between hosts across networks.

- **Not reliable**, no guarantee of delivery.
- Combine with TCP for reliability, UDP for speed.

### IP Packet Structure
```
+-----------+-----------+-----------------------+
| Version  |  IHL  | TOS |     Total Length     |
+-----------+-----------+-----------------------+
|   Identification  | Flags |    Frag Offset    |
+--------------------------+-------+------------+
|     TTL    | Protocol |    Header Checksum    |
+-----------+-----------+-----------------------+
|               Source IP Address               |
+-----------+-----------+-----------------------+
|             Destination IP Address            |
+-----------+-----------+-----------------------+
|        Options         |       Padding        |
+-----------+-----------+-----------------------+
```

### Example (Wireshark)
```
IPv4 Src: 192.168.0.11 -> Dst: 192.168.0.200
  Header Length: 20 bytes
  Total Length: 64
  Flags: Don't Fragment
  TTL: 64
  Protocol: TCP
```

---

## IP Fragmentation

Ethernet max payload = **1500 bytes**. If IP packet exceeds, fragments occur.

### Flags & Offset
- **DF**: Don't Fragment
- **MF**: More Fragments
- **Offset**: position in the stream

Example:
```
Flags: 0x0172
..0. .... = Don't fragment: Not set
...1 .... = More fragments: Set
Fragment offset: 370
```

---

## TCP - Transmission Control Protocol

- **Reliable**, ensures delivery.
- Connection-oriented (3WHS).
- Uses ports.

### TCP Packet Structure
```
+-----------+-----------------------+
| Src Port  |   Destination Port    |
+-----------+-----------------------+
|          Sequence Number          |
+-----------------------------------+
|        Acknowledgment Number      |
+-----------------------------------+
| Data Offset | Reserved |  Flags   |
+------------+----------+-----------+
|   Window   |  Checksum | Urg Ptr  |
+-----------+-----------+-----------+
|        Options        |  Padding  |
+-----------------------+-----------+
```

### Flags
- SYN: start connection
- ACK: acknowledge
- FIN: close
- RST: reset
- PSH: push
- URG: urgent

---

## Three-way Handshake

```
Client ----------------> Server : SYN
Client <---------------- Server : SYN/ACK
Client ----------------> Server : ACK
```

---

## Network Tools & Practice

- `ifconfig eth0`: see IP/MAC
- `macchanger -r eth0`: change MAC
- `route -n`: routing table
- `ipcalc`: analyze subnets

---

## /etc/protocols

```
ip	0	IP		# internet protocol, pseudo protocol number
hopopt	0	HOPOPT		# IPv6 Hop-by-Hop Option [RFC1883]
icmp	1	ICMP		# internet control message protocol
igmp	2	IGMP		# Internet Group Management
ggp	3	GGP		# gateway-gateway protocol
ipencap	4	IP-ENCAP	# IP encapsulated in IP (officially ``IP'')
st	5	ST		# ST datagram mode
tcp	6	TCP		# transmission control protocol
egp	8	EGP		# exterior gateway protocol
igp	9	IGP		# any private interior gateway (Cisco)
pup	12	PUP		# PARC universal packet protocol
udp	17	UDP		# user datagram protocol
hmp	20	HMP		# host monitoring protocol
xns-idp	22	XNS-IDP		# Xerox NS IDP
rdp	27	RDP		# "reliable datagram" protocol
iso-tp4	29	ISO-TP4		# ISO Transport Protocol class 4 [RFC905]
dccp	33	DCCP		# Datagram Congestion Control Prot. [RFC4340]
xtp	36	XTP		# Xpress Transfer Protocol
ddp	37	DDP		# Datagram Delivery Protocol
idpr-cmtp 38	IDPR-CMTP	# IDPR Control Message Transport
ipv6	41	IPv6		# Internet Protocol, version 6
ipv6-route 43	IPv6-Route	# Routing Header for IPv6
ipv6-frag 44	IPv6-Frag	# Fragment Header for IPv6
idrp	45	IDRP		# Inter-Domain Routing Protocol
rsvp	46	RSVP		# Reservation Protocol
gre	47	GRE		# General Routing Encapsulation
esp	50	IPSEC-ESP	# Encap Security Payload [RFC2406]
ah	51	IPSEC-AH	# Authentication Header [RFC2402]
skip	57	SKIP		# SKIP
ipv6-icmp 58	IPv6-ICMP	# ICMP for IPv6
ipv6-nonxt 59	IPv6-NoNxt	# No Next Header for IPv6
ipv6-opts 60	IPv6-Opts	# Destination Options for IPv6
rspf	73	RSPF CPHB	# Radio Shortest Path First (officially CPHB)
vmtp	81	VMTP		# Versatile Message Transport
eigrp	88	EIGRP		# Enhanced Interior Routing Protocol (Cisco)
ospf	89	OSPFIGP		# Open Shortest Path First IGP
ax.25	93	AX.25		# AX.25 frames
ipip	94	IPIP		# IP-within-IP Encapsulation Protocol
etherip	97	ETHERIP		# Ethernet-within-IP Encapsulation [RFC3378]
encap	98	ENCAP		# Yet Another IP encapsulation [RFC1241]
#	99			# any private encryption scheme
pim	103	PIM		# Protocol Independent Multicast
ipcomp	108	IPCOMP		# IP Payload Compression Protocol
vrrp	112	VRRP		# Virtual Router Redundancy Protocol [RFC5798]
l2tp	115	L2TP		# Layer Two Tunneling Protocol [RFC2661]
isis	124	ISIS		# IS-IS over IPv4
sctp	132	SCTP		# Stream Control Transmission Protocol
fc	133	FC		# Fibre Channel
mobility-header 135 Mobility-Header # Mobility Support for IPv6 [RFC3775]
udplite	136	UDPLite		# UDP-Lite [RFC3828]
mpls-in-ip 137	MPLS-in-IP	# MPLS-in-IP [RFC4023]
manet	138			# MANET Protocols [RFC5498]
hip	139	HIP		# Host Identity Protocol
shim6	140	Shim6		# Shim6 Protocol [RFC5533]
wesp	141	WESP		# Wrapped Encapsulating Security Payload
rohc	142	ROHC		# Robust Header Compression
```

---

## Tools

### Wireshark
Capture and analyze packets in GUI.

### tcpdump
Terminal capture, e.g.
```
tcpdump -i eth0 port 80
```

## Additional Notes

### Three-way Handshake (ASCII)
```
Client  -->  SYN    -->  Server
Client  <-- SYN/ACK <--  Server
Client  -->  ACK    -->  Server
```

### Typical Header Sizes
```
Ethernet Header: 14 bytes
IP Header:       20 bytes
TCP Header:      20 bytes (without options)
```

### MTU (Maximum Transmission Unit)
- Typical Ethernet MTU: **1500 bytes**
- Packets larger than MTU get **fragmented** at IP layer.

### tcpdump Filtering Example
```
tcpdump 'tcp[tcpflags] & tcp-syn != 0'
```
Capture only packets with the SYN flag set (like initial handshake).

---
