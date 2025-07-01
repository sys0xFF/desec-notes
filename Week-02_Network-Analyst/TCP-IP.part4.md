# Bytes on the Wire

Below is a representative sample of raw bytes “on the wire”:

```
ff ff ff ff ff ff d4 ab 82 45 c4 0c 08 00
d4 ab 82 45 c4 0c 00 0c 29 76 43 e1 08 00 45 00
01 81 07 00 40 00 40 06 9c e3 c0 a8 00 c8 25 3b
ae e8 dd 84 00 50 ef 49 5b 3c 9b 96 0b bd 80 18
00 e5 97 07 00 00 01 01 08 0a 6b 41 26 5f 09 66
38 94 47 45 54 20 2f 20 48 54 54 50 2f 31 2e 31
```

---

## Analyzing Bytes – Line 1 (Ethernet Frame)

The first 14 bytes represent the Ethernet header:

| Offset | Field               | Bytes (hex)        | Description                               |
|--------|---------------------|--------------------|-------------------------------------------|
| 0–5    | Destination MAC     | `ff:ff:ff:ff:ff:ff`| Broadcast MAC address                     |
| 6–11   | Source MAC          | `d4:ab:82:45:c4:0c`| Sender’s MAC address                      |
| 12–13  | EtherType           | `08 00`            | Protocol = IPv4 (0x0800)                  |

---

## Bytes on the Wire – IP Protocol

### IP Header Structure

```
+-----------+-----------+-----------------------+
| Version  |  IHL  |  TOS |     Total Length     |
+-----------+-----------+-----------------------+
|   Identification    | Flags |   Fragment Offset |
+-----------------------------+-------------------+
|  TTL   | Protocol | Header Checksum        |
+--------+----------+------------------------+
|               Source IP Address               |
+-----------------------------------------------+
|            Destination IP Address             |
+-----------------------------------------------+
| Options (if IHL > 5) | Padding               |
+-----------------------------------------------+
```

### Analyzing the IP Header

Using the bytes starting at offset 14 (hex `45 00 01 81 07 00 40 00 40 06 9c e3 c0 a8 00 c8 25 3b`):

| Offset (hex) | Field           | Value (hex) | Value (decimal) | Notes |
|--------------|-----------------|-------------|-----------------|-------|
| `0`          | Version + IHL   | `45`        | Version = 4, IHL = 5 (20 bytes) |
| `1`          | Type of Service | `00`        | 0               | Default |
| `2–3`        | Total Length    | `01 81`     | 385             | Header + Data length |
| `4–5`        | Identification  | `07 00`     | 1792            | Packet identifier |
| `6–7`        | Flags + Offset  | `40 00`     | DF=1, MF=0, Offset=0 |
| `8`          | TTL             | `40`        | 64              | Time to Live |
| `9`          | Protocol        | `06`        | 6               | TCP |
| `10–11`      | Header Checksum | `9c e3`     | 40163           | Checksum for header |
| `12–15`      | Source IP       | `c0 a8 00 c8` | 192.168.0.200 | Sender address |
| `16–19`      | Dest IP         | `25 3b ae e8` | 37.59.174.232 | Receiver address |

#### Converting Hex to Decimal in C

```c
uint16_t total_length = (bytes[2] << 8) | bytes[3];
printf("Total Length: %d bytes\n", total_length);
```

---

## Bytes on the Wire – TCP Protocol

### TCP Header Structure

```
+------------+----------------------+
| Source Port | Destination Port   |
+------------+----------------------+
|         Sequence Number          |
+----------------------------------+
|      Acknowledgment Number       |
+----------------------------------+
| Data Offset | Reserved | Flags    |
+----------------------------------+
| Window Size | Checksum | Urgent  |
+------------+----------------------+
| Options (if any)    | Padding    |
+------------------------------+
```

### Analyzing the TCP Header

Bytes at offset 34 (hex `dd 84 00 50 ef 49 5b 3c 9b 96 0b bd 80 18 00 e5 …`):

| Offset (hex) | Field               | Value (hex)      | Value (decimal) or interpretation                |
|--------------|---------------------|------------------|--------------------------------------------------|
| `0–1`        | Source Port         | `dd 84`          | 0xDD84 = 56644                                   |
| `2–3`        | Destination Port    | `00 50`          | 80 (HTTP)                                        |
| `4–7`        | Sequence Number     | `ef 49 5b 3c`    | 0xEF495B3C = 4015153756                          |
| `8–11`       | Ack Number          | `9b 96 0b bd`    | 0x9B960BBD = 2623756477                          |
| `12`         | Data Offset + Res.  | `80`             | Data Offset = 8 (32 bytes header), Reserved = 0  |
| `13`         | Flags               | `18`             | 0x18 = PSH + ACK bits set                        |
| `14–15`      | Window Size         | `00 e5`          | 229                                              |

#### Extracting Header Length and Flags in C

```c
uint8_t data_offset = (tcp_bytes[12] >> 4) * 4;  // in bytes
uint8_t flags       = tcp_bytes[13];

printf("TCP Header Length: %d bytes\n", data_offset);
printf("Flags: 0x%02x\n", flags);
if (flags & 0x10) printf("ACK flag is set\n");
if (flags & 0x08) printf("PSH flag is set\n");
```


