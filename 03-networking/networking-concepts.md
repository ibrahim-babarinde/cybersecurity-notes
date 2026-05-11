# Networking Concepts — Notes

## What Is A Network?

A network is a group of devices connected together so they can
communicate and share data. Examples include the internet, home
Wi-Fi, and school or company networks.

**Why networking matters in cybersecurity:**
Attackers almost always exploit network communication to:
- Steal data in transit
- Spread malware across connected devices
- Intercept unencrypted traffic
- Gain unauthorized access to systems

Understanding networking is the foundation of cybersecurity.
Every attack travels through a network. Without this knowledge,
using tools like Nmap, Wireshark, or Metasploit becomes guesswork.

---

## LAN vs WAN

| | LAN | WAN |
|--|-----|-----|
| Full name | Local Area Network | Wide Area Network |
| Coverage | Small area — home, office, school | Large geographical area |
| Speed | Fast | Slower — depends on infrastructure |
| Management | Privately managed | Depends on ISP/telecom |
| Example | Your home Wi-Fi network | The Internet |

A WAN connects multiple LANs together. The Internet is the
largest WAN in existence.

---

## IP Addresses

An IP address is a unique address assigned to every device on a
network so it can send and receive data. Think of it like a postal
address — without it, data has no way of knowing where to go.

---

### IPv4

Uses **32 bits** written as four groups of numbers:

```
192.168.1.1
```

Supports approximately **4.3 billion** unique addresses. Due to
the explosion of internet-connected devices, IPv4 addresses are
nearly exhausted — which is why IPv6 was created.

---

### IPv6

Uses **128 bits** written in hexadecimal:

```
2001:0db8:85a3::8a2e:0370:7334
```

Provides an astronomically large number of addresses and includes
improvements in efficiency and built-in security features.

---

## Public IP vs Private IP

### Public IP
- Visible on the internet
- Assigned by your ISP (Internet Service Provider)
- Example: your router's external internet address
- Unique globally — no two devices on the internet share one

### Private IP
- Used inside local networks only
- Not directly reachable from the internet
- Reused across millions of private networks worldwide

**Private IP ranges:**

| Range | Class |
|-------|-------|
| `10.0.0.0 – 10.255.255.255` | Class A |
| `172.16.0.0 – 172.31.255.255` | Class B |
| `192.168.0.0 – 192.168.255.255` | Class C |

**Why this matters in penetration testing:**
When you scan a network internally you will see private IPs.
When attacking from outside you need to know the public IP.
NAT (Network Address Translation) is what maps private IPs
to the public IP at the router level.

---

## MAC Address

A MAC (Media Access Control) address is a physical hardware
address permanently assigned to a network interface card (NIC).

```
00:1A:2B:3C:4D:5E
```

### MAC vs IP

| | MAC Address | IP Address |
|--|-------------|------------|
| Type | Physical hardware address | Logical software address |
| Assigned by | Manufacturer | Network/ISP |
| Scope | Works inside LAN only | Works across networks |
| Changes | Rarely (can be spoofed) | Can change dynamically |
| Purpose | Local delivery of data | Routing across networks |

**Why this matters in penetration testing:**
MAC addresses can be **spoofed** — changed to impersonate another
device on a network. This is used in attacks like MAC spoofing
to bypass network access controls or ARP poisoning to intercept
traffic on a LAN.

---

## Subnetting

A subnet is a smaller division of a larger network. Subnetting
is the process of splitting one large network into multiple
smaller networks.

**Why organizations subnet:**
- Improve network performance by reducing congestion
- Increase security by isolating departments
- Organize devices logically
- Control traffic flow between groups

**Real world example:**
A company might separate:
- HR → `192.168.1.0/24`
- Finance → `192.168.2.0/24`
- IT → `192.168.3.0/24`

Each department is isolated — a compromise in HR doesn't
automatically give access to Finance.

**CIDR notation:**
The `/24` after an IP address is called CIDR notation and
represents the subnet mask — it tells you how many addresses
are in the network.

| CIDR | Subnet Mask | Usable Hosts |
|------|------------|--------------|
| /8 | 255.0.0.0 | ~16 million |
| /16 | 255.255.0.0 | ~65,000 |
| /24 | 255.255.255.0 | 254 |
| /32 | 255.255.255.255 | 1 (single host) |

---

## The OSI Model

The OSI (Open Systems Interconnection) model is a 7-layer
framework that explains how data moves across a network.
Every layer has a specific job and passes data to the layer
above or below it.

---

### All 7 Layers

**Layer 7 — Application**
The layer closest to the user. Where network applications operate.
- Protocols: HTTP, HTTPS, FTP, DNS, SMTP, SSH
- What you interact with directly in your browser or email client

**Layer 6 — Presentation**
Handles data formatting, encryption, and compression.
- Translates data between application format and network format
- SSL/TLS encryption happens here
- Examples: JPEG, MP4, SSL

**Layer 5 — Session**
Creates, manages, and terminates communication sessions
between two devices.
- Handles authentication and reconnection
- Keeps track of which data belongs to which session

**Layer 4 — Transport**
Ensures reliable or fast delivery of data between devices.
- Protocols: TCP (reliable) and UDP (fast)
- Handles segmentation, flow control, and error checking
- Port numbers operate at this layer

**Layer 3 — Network**
Handles logical addressing and routing — getting data from
one network to another.
- Protocol: IP
- Devices: Routers
- IP addresses operate at this layer

**Layer 2 — Data Link**
Handles physical addressing and local delivery of data
between devices on the same network.
- Protocol: Ethernet, Wi-Fi
- Devices: Switches
- MAC addresses operate at this layer

**Layer 1 — Physical**
The actual physical transmission of raw bits over a medium.
- Cables, fiber optics, radio signals, electrical pulses
- Devices: Hubs, cables, network cards

---

### OSI Quick Reference

| Layer | Name | Key Protocol/Device | Address Used |
|-------|------|-------------------|--------------|
| 7 | Application | HTTP, DNS, FTP | — |
| 6 | Presentation | SSL/TLS | — |
| 5 | Session | NetBIOS | — |
| 4 | Transport | TCP, UDP | Port numbers |
| 3 | Network | IP | IP addresses |
| 2 | Data Link | Ethernet | MAC addresses |
| 1 | Physical | Cables, Wi-Fi | — |

**Why the OSI model matters in penetration testing:**
Every attack targets a specific layer. Understanding which layer
an attack operates on helps you understand how it works and how
to defend against it:
- Phishing → Layer 7 (Application)
- SSL stripping → Layer 6 (Presentation)
- IP spoofing → Layer 3 (Network)
- MAC spoofing → Layer 2 (Data Link)
- Cable tapping → Layer 1 (Physical)

---

## TCP vs UDP

### TCP — Transmission Control Protocol

TCP is **connection-oriented** and **reliable.** Before sending
data, TCP performs a **three-way handshake** to establish a
connection:

```
Client → SYN      → Server
Client ← SYN-ACK  ← Server
Client → ACK       → Server
Connection established.
```

TCP guarantees:
- All packets arrive
- Packets arrive in the correct order
- Errors are detected and retransmitted

**Used for:** websites (HTTP/HTTPS), email, file transfers,
SSH — anything where accuracy matters more than speed.

---

### UDP — User Datagram Protocol

UDP is **connectionless** and **fast.** It sends data without
establishing a connection first and does not check whether
packets arrived.

UDP does not guarantee:
- Delivery of packets
- Order of packets
- Error correction

**Used for:** gaming, video streaming, VoIP, live broadcasts,
DNS — anything where speed matters more than perfection.

---

### TCP vs UDP Comparison

| | TCP | UDP |
|--|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed delivery | No guarantee |
| Speed | Slower | Faster |
| Order | Packets arrive in order | No ordering |
| Error checking | Yes | No |
| Use cases | Web, email, SSH, FTP | Gaming, streaming, DNS |

**Why this matters in penetration testing:**
Nmap uses TCP SYN scans by default to detect open ports because
TCP's handshake makes it easy to determine if a port is open,
closed, or filtered. UDP scanning is slower and harder because
there is no handshake to confirm a response.

---

## Ports

Ports are communication endpoints used by services and
applications on a device. While an IP address identifies
the device, a port number identifies the specific service
running on that device.

Think of it like an apartment building — the IP address is
the building, the port number is the specific apartment.

**Port ranges:**
- `0 – 1023` — Well-known ports (reserved for standard services)
- `1024 – 49151` — Registered ports (used by applications)
- `49152 – 65535` — Dynamic/private ports (temporary connections)

---

### Critical Port Numbers To Memorize

| Port | Protocol | Service |
|------|----------|---------|
| 20/21 | TCP | FTP (File Transfer) |
| 22 | TCP | SSH (Secure Shell) |
| 23 | TCP | Telnet (Unencrypted remote access) |
| 25 | TCP | SMTP (Email sending) |
| 53 | TCP/UDP | DNS (Domain Name System) |
| 67/68 | UDP | DHCP (IP assignment) |
| 69 | UDP | TFTP (Trivial File Transfer) |
| 80 | TCP | HTTP (Web traffic) |
| 110 | TCP | POP3 (Email retrieval) |
| 123 | UDP | NTP (Network Time Protocol) |
| 143 | TCP | IMAP (Email retrieval) |
| 161 | UDP | SNMP (Network management) |
| 389 | TCP | LDAP (Directory services) |
| 443 | TCP | HTTPS (Encrypted web traffic) |
| 445 | TCP | SMB (File sharing — EternalBlue) |
| 3306 | TCP | MySQL (Database) |
| 3389 | TCP | RDP (Remote Desktop Protocol) |
| 5432 | TCP | PostgreSQL (Database) |
| 5900 | TCP | VNC (Remote desktop) |
| 8080 | TCP | Alternative HTTP |

**Why ports matter in penetration testing:**
Every open port is a potential entry point. When Nmap finds
port 445 open, you know SMB is running — and SMB has a history
of critical vulnerabilities like EternalBlue (MS17-010).
Knowing what service runs on which port instantly tells you
where to look for vulnerabilities.

---

## Key Takeaways

1. Every attack travels through a network — networking is the
foundation of everything in cybersecurity
2. Know your private IP ranges by heart — `10.x`, `172.16-31.x`,
`192.168.x`
3. The OSI model tells you where an attack happens — use it to
understand and explain any attack at a technical level
4. TCP = reliable, UDP = fast — know when each is used and why
5. Memorize critical port numbers — in interviews and real
engagements, knowing that port 445 means SMB or port 22 means
SSH is expected knowledge
6. Subnetting isolates networks — both for security and for
understanding how targets are organized during a pentest

---

## Resources

- [TryHackMe — Networking Concepts](https://tryhackme.com/room/networkingconcepts)
- [Subnet Calculator](https://www.subnet-calculator.com/)
- [OSI Model Explained](https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/)
- [TCP vs UDP](https://www.cloudflare.com/learning/ddos/glossary/user-datagram-protocol-udp/)

---

*Notes by Ibrahim Babarinde | [GitHub](https://github.com/ibrahim-babarinde) | [TryHackMe](https://tryhackme.com/p/Anzai)*
