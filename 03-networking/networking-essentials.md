# Networking Essentials — Notes

## DNS — Domain Name System

DNS translates human-readable domain names into IP addresses
that computers use to communicate. It is often called the
**internet's phonebook.**

Humans remember names like `google.com` but computers
communicate using IP addresses like `142.250.74.46`.
DNS bridges that gap automatically.

---

### How DNS Works — Step By Step

When you type `google.com` in your browser:

```
1. Browser checks local DNS cache
   → Has this IP been looked up recently?

2. If not cached → asks your configured DNS server
   → Usually provided by your ISP or set manually (e.g. 8.8.8.8)

3. DNS server searches for the IP address of google.com
   → If it doesn't know, it asks other DNS servers up the chain

4. DNS returns the correct IP address to your browser

5. Browser connects to Google's server using that IP

6. Webpage loads
```

---

### DNS In Penetration Testing

DNS is a goldmine during reconnaissance:

- **DNS enumeration** — querying DNS records to map out a target's
infrastructure before attacking
- **Subdomain enumeration** — finding hidden subdomains using tools
like Gobuster or DNSrecon that may expose admin panels or dev servers
- **DNS zone transfer** — misconfigured DNS servers may leak their
entire zone file, revealing all subdomains and internal IPs
- **DNS poisoning** — injecting fake DNS records to redirect traffic
to a malicious server

```bash
# Basic DNS lookup
nslookup google.com

# Detailed DNS query
dig google.com

# Find all DNS records
dig google.com ANY
```

---

## DHCP — Dynamic Host Configuration Protocol

DHCP automatically assigns network configuration to devices
when they join a network. Without it, every device would need
to be manually configured with an IP address.

**What DHCP assigns to each device:**
- IP address
- Subnet mask
- Default gateway
- DNS server address

**Example:**
When you connect your phone to Wi-Fi, DHCP automatically
gives your phone everything it needs to communicate on the
network in seconds — no manual setup required.

---

### DHCP In Penetration Testing

- **Rogue DHCP server attack** — an attacker sets up a fake DHCP
server on the network. When devices request network settings,
the fake server responds first and assigns a malicious gateway
or DNS server — allowing the attacker to intercept all traffic
- **DHCP starvation** — flooding a DHCP server with fake requests
to exhaust all available IP addresses, causing a denial of service
for legitimate devices trying to join the network

---

## Routers vs Switches

### Router

A router connects different networks together and forwards
data between them based on IP addresses.

**Main functions:**
- Routes internet traffic between your LAN and the internet (WAN)
- Assigns local IP addresses (often via built-in DHCP)
- Connects your local network to external networks
- Performs NAT (Network Address Translation)

### Switch

A switch connects multiple devices within the same local network
and forwards data between them based on MAC addresses.

**Main functions:**
- Connects computers, printers, servers inside a LAN
- Forwards data only to the specific device it is addressed to
- Operates at Layer 2 (Data Link) of the OSI model

### Router vs Switch Comparison

| | Router | Switch |
|--|--------|--------|
| Operates at | Layer 3 (Network) | Layer 2 (Data Link) |
| Uses | IP addresses | MAC addresses |
| Connects | Different networks | Devices within same network |
| Example | Home Wi-Fi router | Office network switch |

---

## NAT — Network Address Translation

NAT converts private IP addresses into a single public IP address
when traffic leaves your local network to reach the internet.

**Example:**
Your home has five devices — each with a private IP:
```
Phone      → 192.168.1.2
Laptop     → 192.168.1.3
TV         → 192.168.1.4
Tablet     → 192.168.1.5
PC         → 192.168.1.6
```
All five share one public IP assigned by your ISP:
```
Public IP → 102.45.67.89
```

Your router handles the translation — tracking which device
made which request and routing the responses back correctly.

**Why NAT exists:**
- Conserves IPv4 addresses — billions of devices share a much
smaller pool of public IPs
- Hides internal device IPs from the public internet
- Adds a basic layer of security by obscuring internal structure

**NAT in penetration testing:**
When attacking from outside a network, you only see the public IP.
NAT hides all internal devices behind it. This is why internal
network pentests require physical or VPN access — to get past NAT
and see the real internal IP addresses.

---

## Firewalls

A firewall monitors and filters incoming and outgoing network
traffic based on predefined security rules. It decides what
traffic to allow and what to block.

**What a firewall can do:**
- Allow legitimate traffic
- Block suspicious or unauthorized connections
- Prevent unauthorized access to internal systems
- Log traffic for analysis

---

### Stateless Firewall

Examines each packet individually without any memory of
previous packets or connections.

- Faster and simpler
- Makes decisions based only on source IP, destination IP,
port, and protocol
- Less intelligent — cannot detect attacks that span
multiple packets
- Easier to bypass

---

### Stateful Firewall

Tracks the state of active network connections and understands
the context of each packet.

- Knows whether a packet belongs to an established, legitimate
connection or is an unsolicited incoming packet
- Much harder to bypass
- Can detect and block port scanning, SYN floods, and other
multi-packet attacks
- Standard in modern network security

---

### Firewalls In Penetration Testing

Bypassing firewalls is a core penetration testing skill:

```bash
# Nmap firewall evasion — fragment packets
nmap -f target_ip

# Use decoys to hide your real IP
nmap -D RND:10 target_ip

# Slow scan to avoid detection
nmap -T1 target_ip

# Scan from a specific source port (may bypass rules)
nmap --source-port 53 target_ip
```

---

## VPN — Virtual Private Network

A VPN creates an encrypted tunnel between your device and
a VPN server, routing all your traffic through it.

**How it works:**
```
Your Device → [Encrypted Tunnel] → VPN Server → Internet
```

- Your traffic is encrypted end-to-end inside the tunnel
- Your real IP address is hidden — websites see the VPN
server's IP instead
- Traffic is much harder to intercept or analyze

**Uses:**
- Privacy — hide browsing activity from ISP or surveillance
- Security — protect traffic on public Wi-Fi
- Remote access — connect securely to a company network from anywhere
- Bypass geo-restrictions — appear to be in a different country

**VPN in penetration testing:**
TryHackMe and HackTheBox both use VPN connections to connect
you to their lab networks. When you run `openvpn` and connect
to their VPN, your machine becomes part of their private lab
network — allowing you to reach target machines directly.

```bash
# Connect to a VPN configuration file
sudo openvpn your-config.ovpn
```

---

## ARP — Address Resolution Protocol

ARP maps IP addresses to MAC addresses inside a local network.
It answers the question: *"I know the IP address — but what is
the MAC address of that device so I can deliver the data locally?"*

**How ARP works:**
```
Device A wants to send data to 192.168.1.5

1. Device A broadcasts: "Who has 192.168.1.5? Tell 192.168.1.2"
2. Every device on the LAN receives this broadcast
3. Device with IP 192.168.1.5 replies: "I have it. My MAC is 00:1A:2B:3C:4D:5E"
4. Device A stores this in its ARP cache
5. Data is delivered using the MAC address
```

---

### ARP Poisoning — A Critical Attack

ARP has no authentication — any device can reply to an ARP
request, even with false information. This makes it vulnerable
to **ARP poisoning (ARP spoofing):**

An attacker sends fake ARP replies telling other devices that
the attacker's MAC address corresponds to the router's IP.
All traffic meant for the router now goes to the attacker first
— a classic **Man-in-the-Middle (MITM) attack.**

```bash
# View ARP cache on Linux
arp -a

# ARP poisoning with arpspoof
arpspoof -i eth0 -t victim_ip router_ip
```

---

## ICMP — Internet Control Message Protocol

ICMP is used for network diagnostics and error reporting.
It helps devices communicate network status information —
not actual data.

**What ICMP is used for:**
- Testing connectivity between devices
- Checking if a host is reachable
- Reporting network errors (destination unreachable, TTL expired)
- Network troubleshooting

---

### Ping — The Main ICMP Tool

Ping sends ICMP echo requests to a target and waits for
echo replies. If replies come back, the host is alive and reachable.

```bash
# Basic ping
ping google.com

# Ping a specific number of times
ping -c 4 google.com

# Ping with a specific packet size
ping -s 1000 google.com
```

**Output example:**
```
PING google.com (142.250.74.46)
64 bytes from 142.250.74.46: icmp_seq=1 ttl=118 time=12.4 ms
64 bytes from 142.250.74.46: icmp_seq=2 ttl=118 time=11.9 ms
```

---

### Traceroute — Mapping The Path

Traceroute uses ICMP to map every hop a packet takes from
your machine to the destination:

```bash
traceroute google.com
```

This reveals every router the packet passes through — useful
for understanding network topology during reconnaissance.

---

### ICMP In Penetration Testing

- **Ping sweep** — pinging a range of IPs to discover which
hosts are alive on a network
- **ICMP tunneling** — hiding data inside ICMP packets to
bypass firewalls that block other protocols
- Many firewalls block ICMP — if ping fails it doesn't always
mean the host is down. Nmap has techniques to discover hosts
even when ICMP is blocked

```bash
# Nmap ping sweep to discover live hosts
nmap -sn 192.168.1.0/24
```

---

## Key Takeaways

1. DNS is the internet's phonebook — and a major reconnaissance
target. Always enumerate DNS records during the recon phase
2. DHCP automates network configuration — rogue DHCP servers
are a powerful man-in-the-middle attack vector
3. Routers use IP addresses to connect networks — switches use
MAC addresses to connect devices within a network
4. NAT hides internal devices behind one public IP — understanding
this is essential when planning external vs internal pentests
5. Stateful firewalls are far harder to bypass than stateless ones —
know the difference and know your evasion techniques
6. ARP has no authentication — ARP poisoning is one of the most
effective LAN-based attacks and requires no special privileges
7. ICMP is for diagnostics — ping and traceroute are your first
tools when mapping a network

---

## Resources

- [TryHackMe — Networking Essentials](https://tryhackme.com/room/networkingessentials)
- [DNS Explained](https://www.cloudflare.com/learning/dns/what-is-dns/)
- [ARP Poisoning Explained](https://www.imperva.com/learn/application-security/arp-spoofing/)
- [How NAT Works](https://www.cisco.com/c/en/us/support/docs/ip/network-address-translation-nat/26704-nat-faq-00.html)
- [VPN Explained](https://www.kaspersky.com/resource-center/definitions/what-is-a-vpn)

---

*Notes by Ibrahim Babarinde | [GitHub](https://github.com/ibrahim-babarinde) | [TryHackMe](https://tryhackme.com/p/Anzai)*
