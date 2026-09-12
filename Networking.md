# Networking — Linux + AWS Cloud Engineer

## 1. Networking Fundamentals

* Network Basics
* LAN, MAN & WAN — Basic
* Client–Server Model
* IP Address vs MAC Address
* Port & Socket
* Switch vs Router
* OSI Model
* TCP/IP Model

---

## 2. IP Addressing & Subnetting

* IPv4
* IPv6 — Basic Understanding
* Public vs Private IP
* Static vs Dynamic IP
* Subnet Mask
* CIDR
* Network Address
* Broadcast Address
* Usable Host Range
* Usable IP Calculation
* Basic Subnetting

---

## 3. TCP & UDP

* TCP vs UDP
* TCP 3-Way Handshake
* TCP Connection Termination
* TCP Flags — SYN, ACK, FIN, RST
* Common Ports
* Connection Refused vs Connection Timeout

---

## 4. DNS

* DNS Basics
* DNS Resolution Process
* DNS Records

  * A
  * AAAA
  * CNAME
  * MX
  * PTR
* Forward vs Reverse DNS
* `/etc/hosts`
* `/etc/resolv.conf`
* DNS Troubleshooting

---

## 5. Routing & NAT

* What is Routing?
* Routing Table
* Local Routes
* Default Route
* Default Gateway
* Next Hop
* Static vs Dynamic Routing — Basic Understanding
* NAT
* PAT
* Basic Linux Routing
* Routing Troubleshooting

---

## 6. Important Network Protocols

* HTTP / HTTPS
* SSH
* FTP / SFTP
* DNS
* DHCP
* ICMP
* ARP
* NTP

> Focus on what each protocol does, its common port, and basic troubleshooting.

---

## 7. Linux Networking Commands

* Network Interfaces
* `ip addr`
* `ip route`
* `ss`
* `ping`
* `traceroute`
* `dig`
* `nslookup`
* `curl`
* `nc`
* `tcpdump`
* Checking Listening Ports
* Finding Which Process Is Using a Port
* Checking IP Configuration
* Checking Routing
* Testing Port Connectivity

---

## 8. Network Performance & Firewall Basics

### Network Performance

* Bandwidth
* Throughput
* Latency
* Jitter — Basic
* Packet Loss
* MTU
* Network Bottleneck

### Linux Firewall

* Firewall Basics
* Stateful vs Stateless — Basic
* `iptables` — Basic
* `firewalld` — Basic

---

## 9. Network Troubleshooting — Very Important

- Host Cannot Reach Another Host
- Ping Fails
- DNS Issues
- DNS Resolution Fails
- DNS Works but Application Doesn't
- Port Is Not Reachable
- Connection Timeout vs Connection Refused
- Network Interface Issues
- Routing Issues
- High Network Latency
- Packet Loss
- Using `tcpdump` to Troubleshoot

## 10. Practical Interview Scenarios

- Server Can Ping IP but Cannot Resolve Hostname
- Server Can Resolve Hostname but Cannot Connect to Port
- SSH Connection Is Timing Out
- SSH Gives `Connection Refused`
- Website Is Reachable by IP but Not Hostname
- Application Is Listening but Remote Clients Cannot Connect
- High Network Latency Between Servers
- Packet Loss Investigation
- Finding Which Process Is Using a Port
- Troubleshooting a Linux Server With No Network Connectivity

---
---


````md
# Networking Fundamentals

For a **4-year Linux + AWS Cloud Engineer interview**, focus on networking concepts useful for troubleshooting **EC2, VPC, Load Balancers, Security Groups, ports, routing, and Linux networking**.

---

## 1. Network Basics

A **network** is a group of devices connected together so they can communicate and exchange data.

### Important Terms

| Term | Simple Meaning |
|---|---|
| IP Address | Identifies a device/interface on a network |
| MAC Address | Hardware/interface address |
| Port | Identifies a service/application |
| Protocol | Rules used for communication |
| Packet | Small unit of data transmitted over a network |
| DNS | Converts domain name → IP address |
| Gateway | Device/path used to reach another network |

### Example

When you access:

```text
https://google.com
````

Basic flow:

```text
Your Laptop
    ↓
DNS → Finds Google's IP
    ↓
Router/Gateway
    ↓
Internet
    ↓
Google Server
```

---

# 2. LAN, MAN & WAN

These classify networks mainly by geographical coverage.

## LAN — Local Area Network

Covers a small geographical area.

Examples:

* Home
* Office
* Data Center

```text
PC ── Switch ── Server
      │
      └── Printer
```

## MAN — Metropolitan Area Network

Covers a city or large metropolitan area.

```text
Office A ───── Network ───── Office B
       \                   /
        ─── Same City ─────
```

## WAN — Wide Area Network

Covers large geographical areas such as countries or continents.

```text
India Office ───── Internet/WAN ───── US Data Center
```

**Interview Point:** The Internet is the largest example of a WAN.

---

# 3. Client–Server Model

A **client** requests a service.

A **server** provides the service.

```text
Client
  |
  | Request
  ↓
Server
  |
  | Response
  ↓
Client
```

### Example

When you open a website:

```text
Browser = Client
Web Server = Server
```

The client sends:

```text
GET /index.html
```

The server responds:

```text
HTTP 200 OK
```

### AWS Example

```text
User
 ↓
Load Balancer
 ↓
EC2 Web Server
 ↓
Database
```

---

# 4. IP Address vs MAC Address

## IP Address

An IP address is a **logical address** used for network communication and routing.

Example:

```text
192.168.1.10
```

IPv6 example:

```text
2001:db8::1
```

Think:

> **IP = Address used to identify a device/interface on a network**

## MAC Address

A MAC address is a **Layer-2 hardware/interface address**.

Example:

```text
00:1A:2B:3C:4D:5E
```

Think:

> **MAC = Network interface address used primarily for local network communication**

### Simple Difference

```text
MAC → Local network communication
IP  → Network-to-network communication
```

### Interview Answer

> An IP address is a logical address used for network communication and routing, while a MAC address is a Layer-2 hardware/interface address used primarily for communication within the local network.

---

# 5. Port & Socket

This is **very important for Linux and AWS interviews**.

## Port

A port identifies a particular service/application on a machine.

### Common Ports

| Port | Service               |
| ---: | --------------------- |
|   22 | SSH                   |
|   23 | Telnet                |
|   25 | SMTP                  |
|   53 | DNS                   |
|   80 | HTTP                  |
|  443 | HTTPS                 |
| 3306 | MySQL                 |
| 5432 | PostgreSQL            |
| 8080 | Common alternate HTTP |

Example:

```text
192.168.1.10:22
```

Here:

```text
IP   = 192.168.1.10
Port = 22
```

## Socket

A socket is an **endpoint of network communication**.

For TCP communication:

```text
Source IP:Source Port
        ↓
Destination IP:Destination Port
```

Example:

```text
Client
10.0.1.10:54321
       |
       | TCP
       ↓
10.0.2.20:3306
Server
```

Here:

```text
Client Socket = 10.0.1.10:54321
Server Socket = 10.0.2.20:3306
```

---

# 6. Switch vs Router

## Switch

A switch primarily connects devices **within the same network/LAN**.

```text
PC1 ─┐
PC2 ─┼── Switch
PC3 ─┘
```

A switch primarily works with **MAC addresses**.

## Router

A router connects **different networks**.

```text
LAN 1
  |
Router
  |
LAN 2
```

A router uses **IP addresses** to determine where traffic should go.

### Easy Memory Trick

```text
Switch → Connects devices
Router → Connects networks
```

---

# 7. OSI Model

The OSI model has **7 layers**.

```text
7  Application
6  Presentation
5  Session
4  Transport
3  Network
2  Data Link
1  Physical
```

### Mnemonic

> **All People Seem To Need Data Processing**

| Layer | Name         | Examples             |
| ----: | ------------ | -------------------- |
|     7 | Application  | HTTP, DNS, SSH       |
|     6 | Presentation | Encryption, Encoding |
|     5 | Session      | Session Management   |
|     4 | Transport    | TCP, UDP             |
|     3 | Network      | IP, Router           |
|     2 | Data Link    | MAC, Switch          |
|     1 | Physical     | Cable, Fiber, Signal |

### Important Layers for Interviews

```text
Layer 7 → Application → HTTP/HTTPS/DNS
Layer 4 → Transport   → TCP/UDP/Ports
Layer 3 → Network     → IP/Router
Layer 2 → Data Link   → MAC/Switch
Layer 1 → Physical    → Cable
```

---

# 8. TCP/IP Model

The TCP/IP model is commonly represented using **4 layers**:

```text
Application
Transport
Internet
Network Access
```

### Comparison with OSI

| TCP/IP Model   | OSI Model                            |
| -------------- | ------------------------------------ |
| Application    | Application + Presentation + Session |
| Transport      | Transport                            |
| Internet       | Network                              |
| Network Access | Data Link + Physical                 |

### Examples

```text
Application → HTTP, HTTPS, DNS, SSH
Transport   → TCP, UDP
Internet    → IP
Network     → Ethernet, MAC
```

---

# ⭐ What to Focus on for AWS/Linux Interviews

Don't spend too much time memorizing all 7 OSI layers.

Be strong in these concepts:

```text
IP Address
     ↓
Subnet
     ↓
Gateway
     ↓
Routing
     ↓
TCP/UDP
     ↓
Ports
     ↓
DNS
     ↓
HTTP/HTTPS
     ↓
Security Group / Firewall
```

## Basic Network Troubleshooting Flow

```text
Can I reach the server?
        ↓
      Ping?
        ↓
Is the port reachable?
        ↓
   nc / telnet
        ↓
Is the service running?
        ↓
systemctl status
        ↓
Is firewall blocking it?
        ↓
Firewall / Security Group
        ↓
Is routing correct?
        ↓
Route Table / Gateway
```

---

# Important Linux Networking Commands

### 1. ping

Test basic network connectivity.

```bash
ping <IP>
```

### 2. nc

Test whether a particular port is reachable.

```bash
nc -zv <IP> <PORT>
```

Example:

```bash
nc -zv 10.0.1.20 443
```

### 3. ss

Check listening ports and network connections.

```bash
ss -tulnp
```

### 4. ip addr

Check IP addresses and network interfaces.

```bash
ip addr
```

### 5. ip route

Check the routing table.

```bash
ip route
```

### 6. nslookup

Check DNS resolution.

```bash
nslookup google.com
```

### 7. traceroute

Trace the network path to a destination.

```bash
traceroute <IP>
```

---

# Interview Priority

For a **4-year Linux + AWS Cloud Engineer**, prioritize:

1. **IP Address & Subnet**
2. **TCP vs UDP**
3. **Ports & Sockets**
4. **DNS**
5. **Routing & Gateway**
6. **OSI Model**
7. **Security Groups & Firewalls**
8. **Switch vs Router**
9. **Network Troubleshooting Commands**
10. **AWS VPC Networking**

```
```
