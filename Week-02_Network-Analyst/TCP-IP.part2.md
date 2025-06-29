# The Red Journey - Networking Notes


## UDP vs TCP

### Differences Between UDP and TCP

| Feature             | TCP                          | UDP                            |
|----------------------|-----------------------------|--------------------------------|
| Connection           | Connection-oriented         | Connectionless                 |
| Reliability          | Guarantees delivery, order  | No guarantee of delivery/order |
| Speed                | Slower (due to checks)      | Faster                         |
| Use Cases            | Web, Email, File Transfer   | Streaming, VoIP, DNS           |

UDP does not establish a connection before sending data. This makes it much faster
and suitable for applications where speed is more critical than reliability.
Its header is simpler:

```
+-------------+-------------------+
| Source Port | Destination Port  |
+-------------+-------------------+
| Length      | Checksum          |
+-------------+-------------------+
```

### DNS Example

Without DNS, we would need to remember IP addresses. DNS translates domain names into IPs.

```
User enters: facebook.com
       |
       v
+------------------+
|     HOST         |
| (Your Computer)  |
+------------------+
       |
       | Asks: "What's the IP of facebook.com?"
       v
+------------------------+
|     DNS Server         |
|       8.8.8.8           |
+------------------------+
       |
       | Queries root/other DNS servers
       v
+---------------------------+
| facebook.com              |
| IP: 157.240.222.35         |
+---------------------------+
       ^
       |
       | DNS replies: "facebook.com is 157.240.222.35"
       v
+------------------+
|     HOST         |
+------------------+

Now the HOST can connect directly to Facebook.
```

---


## TCP Connection Closure and Reset

### Closing a TCP Connection (FIN Flag)

When hosts want to gracefully terminate a connection, they use the **FIN** flag.

```
IPv4 Src: 192.168.0.11 -> Dst: 192.168.0.200
TCP Src Port: 56104, Dst Port: 80
Seq: 777, Ack: 825
Flags: FIN, ACK
```

### Resetting a TCP Connection (RST Flag)

If a problem occurs, the connection is immediately reset using the **RST** flag.

```
IPv4 Src: 192.168.0.200 -> Dst: 192.168.0.14
TCP Src Port: 80, Dst Port: 60839
Seq: 1, Ack: 1
Flags: RST, ACK
```

---

## Example Communication Overview

| Device   | OS    | MAC                 | IP             | Port  |
|----------|-------|----------------------|----------------|-------|
| Client   | MacOS | a4:5e:60:b8:c1:af    | 192.168.0.11   | 56104 |
| Server   | Linux | 00:0C:29:76:43:E1    | 192.168.0.200  | 80    |

```
Client (MacOS)
  MAC: a4:5e:60:b8:c1:af
  IP: 192.168.0.11
  Port: 56104
    ⇅
Server (Linux)
  MAC: 00:0C:29:76:43:E1
  IP: 192.168.0.200
  Port: 80
```

---

## Example of Encapsulation Layers

```
[Application]
    -> HTTP Data

[Transport]
    -> TCP Header + HTTP Data (TCP Segment)

[Internet]
    -> IP Header + TCP Segment (IP Packet)

[Link Layer]
    -> Ethernet Header + IP Packet (Ethernet Frame)
```

---

## Traffic Analysis Table

| Protocol Layer Line                                    | Meaning                           |
|--------------------------------------------------------|-----------------------------------|
| Ethernet Src/Dst                                       | MAC Address, Link Layer, Ethernet |
| IP Src/Dst                                             | IP Address, Network Layer, IPv4   |
| TCP Src/Dst Ports, Seq, Ack, Len                       | Ports, Transport Layer, TCP       |
| Hypertext Transfer Protocol                            | Application Layer, HTTP           |


