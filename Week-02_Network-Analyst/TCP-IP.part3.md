# HTTP and ICMP Protocols

## HTTP Protocol

The HTTP protocol works by exchanging requests between client and server.

### Client (Browser)
- Firefox
- Google Chrome
- Opera
- Others

### Server (Web Server)
- Apache
- Microsoft IIS
- Others

## HTTP Structure

The HTTP structure is composed of a **Header** and a **Body**.

The protocol supports several methods, such as:
- `GET`
- `POST`
- `HEAD`
- `PUT`
- `DELETE`
- `OPTIONS`

The most commonly used are:
- **GET**: request resources
- **POST**: send data (e.g., in web forms)

## HTTP Request vs HTTP Response

The HTTP protocol operates with requests and responses:
- **HTTP Request**: sent from client to server
- **HTTP Response**: sent from server back to client

### Example Status Codes

Every HTTP request receives a response code (status):

- `200 OK` - Request succeeded
- `301 Moved Permanently` - Resource moved (redirect)
- `403 Forbidden` - Client does not have permission
- `404 Not Found` - Resource not found
- `500 Internal Server Error` - Server-side error


## ICMP Protocol

The ICMP protocol is a messaging protocol used to send alerts about network conditions.

### ICMP Basic Structure

```
+--------+--------+--------+--------+
|  Type  |  Code  |     Checksum    |
+--------+--------+--------+--------+
```

### ICMP Types and Codes

There are many ICMP types and codes. Here are the main ones:

| TYPE                      | Code                      | Meaning                          |
|----------------------------|--------------------------|----------------------------------|
| 0 - echo reply             | 0 – ping echo reply      | Ping response                    |
| 8 - echo request           | 0 – ping echo request    | Ping request                     |
| 11 - time exceeded         | 0 – TTL exceeded         | TTL reached zero                 |
| 3 - destination unreachable | 0 – network unreachable | Network not reachable            |
|                            | 1 – host unreachable     | Host not reachable               |
|                            | 2 – protocol unreachable | Protocol not found               |
|                            | 3 – port unreachable     | Port not found                   |
|                            | 4 – fragmentation needed | Packet needs fragmentation       |

## ICMP in Practice

### Importance of ICMP

In the example below, a client host uses UDP to communicate with a closed port on the server.  
Since UDP has no built-in control, ICMP is used to notify about unreachable destinations.

### Understanding PING

The `ping` command is commonly used to test network communication between hosts.  
It helps check connectivity, measure response time, and detect latency problems.

### Understanding TTL

**Time To Live (TTL)** ensures that an IP packet does not circulate endlessly.

- Each router (hop) decreases the TTL by 1.
- When TTL reaches 0, the router discards the packet.

Typical default TTLs:
- Windows: `128`
- Linux: `64`
- Unix: `255`

Users can change these values.

### Traceroute with ICMP

**TTL Exceeded**:  
When TTL reaches 0, routers typically send an ICMP message with type `11` and code `0`.

This allows tools like `traceroute` to discover the path and hops taken by packets.
