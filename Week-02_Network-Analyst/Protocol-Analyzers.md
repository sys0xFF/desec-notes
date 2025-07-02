
# Protocol Analyzers

---

## Wireshark

### What is Wireshark?
Wireshark is a free and open-source network protocol analyzer widely used for network troubleshooting, analysis, software and protocol development, and education. It captures network packets and displays detailed protocol information, allowing users to see what's happening on their network at a microscopic level.

### Why is Wireshark Important?
- Understand how protocols and network communications work.
- Identify and troubleshoot network issues.
- Analyze exploits and attacks to understand their inner workings.

---

## Welcome to Wireshark

This is Wireshark:

![Wireshark screenshot](https://i.imgur.com/yGNY0Q8.png)

Initially, we can observe all available interfaces ready for packet capturing and define filters to specify what traffic will appear.

### Examples of Filters:
- `tcp port 80`: Capture only HTTP traffic.
- `udp`: Capture only UDP packets.
- `ip.src == 192.168.1.10`: Capture packets originating from a specific IP.

When we start capturing, Wireshark listens to the network interface, intercepting packets. Captured packets appear in real-time, displaying details such as source and destination IPs, protocols, length, and additional packet information.

![Wireshark packet capture](https://i.imgur.com/IcWLfUn.png)

Clicking on a captured packet displays detailed packet data at the lower-left pane and hexadecimal values converted to ASCII at the lower-right pane.

---

## Wireshark in Practice

First, let's create a capture filter:

- Navigate to **"Manage Capture Filters"**.

![Capture Filters screenshot](https://i.imgur.com/vWZSSLo.png)

- Click the "+" icon at the lower-left corner.
- Name the filter **"FTP"** and set the Filter value to `port 21`.

Now, start capturing connections.

Notice limited results appearing in FileZilla since we applied a filter for port 21.

Now attempt an FTP connection to port 21:

```bash
ftp ftp.businesscorp.com.br
```

Enter random usernames and passwords.

Although the login fails, Wireshark captures these attempts.

![FTP connection attempt screenshot](https://i.imgur.com/1h1fuJA.png)

Right-click on a captured packet, then choose **Follow -> TCP Stream** to analyze request sequences:

```
220 ProFTPD 1.3.4a Server (FTP) [::ffff:37.59.174.225]
AUTH TLS
500 AUTH not understood
AUTH SSL
500 AUTH not understood
USER ricardo
331 Password required for ricardo
PASS admin
530 Login incorrect.
```

In this scenario, the server requested authentication credentials, but the login attempt failed. Also, the client attempted secure authentication using AUTH TLS and AUTH SSL, but the server did not understand these commands.

Next, perform another capture without applying any filters.

Generate logs by performing activities such as:
- Visiting websites (HTTP/HTTPS).
- Pinging servers (`ping google.com`).
- Checking DNS queries (`nslookup example.com`).

### Useful Filters Examples:

- `tcp contains "password"`: Find packets containing specific strings.
- `http.request`: Filter only HTTP requests.
- `http.request.uri contains "login"`: Find HTTP requests related to login pages.
- `ip.addr == 192.168.0.1`: Capture packets with a specific IP address (source or destination).
- `dns`: Capture DNS traffic.
- `icmp`: Capture ICMP traffic (e.g., ping requests and replies).
- `arp`: Capture only ARP requests and replies.
- `tcp.port == 443`: Capture HTTPS traffic.
- `udp.port == 53`: Capture DNS queries over UDP.
- `eth.addr == 00:0C:29:7D:21:35`: Capture traffic involving a specific MAC address.
- `tcp.flags.syn == 1`: Capture TCP SYN packets (initial connection requests).
- `tcp.flags.reset == 1`: Capture TCP RST packets (connection resets).
- `http.response.code == 404`: Display only HTTP responses with 404 errors.
- `http.request.method == "POST"`: Capture only HTTP POST requests.
- `ip.src == 192.168.1.0/24`: Capture packets originating from a specific subnet.
- `tcp contains "user"`: Capture TCP packets containing specific payload data (e.g., "user").

---

## TCPDUMP

### What is TCPDUMP?
TCPDUMP is a powerful command-line network packet analyzer used to capture network packets directly from the terminal. It is frequently utilized in penetration testing and network troubleshooting due to its efficiency and simplicity.

### Importance in Pentesting:
- Quick network reconnaissance.
- Analyzing live traffic flows.
- Identifying malicious activities and vulnerabilities.

### Basic Usage:
```bash
tcpdump -i eth0
```
Starts capturing traffic on the `eth0` interface.

```bash
tcpdump port 80
```
Captures all HTTP traffic.

### Practical Examples:
Capture packets and save them to a file for later analysis:
```bash
tcpdump -w capture.pcap
```

Read previously saved capture:
```bash
tcpdump -r capture.pcap
```

### Advanced Filtering Examples:
- Capture TCP SYN packets:
```bash
tcpdump 'tcp[tcpflags] & tcp-syn != 0'
```

- Capture packets from a specific host:
```bash
tcpdump host 192.168.0.10
```

- Capture ICMP traffic:
```bash
tcpdump icmp
```

- Capture HTTP requests containing specific words:
```bash
tcpdump -i eth0 -A | grep "password"
```

TCPDUMP provides an effective, streamlined approach for quick packet capturing and analysis, making it indispensable in penetration testing and network diagnostics.

---
