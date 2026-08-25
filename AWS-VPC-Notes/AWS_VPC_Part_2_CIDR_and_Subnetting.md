# AWS VPC — Part 2: CIDR & Subnetting

> **Goal:** Understand IPv4, CIDR, subnet sizing, AWS-reserved IPs, and practical subnet design for DevOps.

---

## 1. IPv4 Basics

IPv4 uses **32 bits**.

Example:

```text
192.168.1.10
```

It contains four 8-bit octets.

```text
IPv4 = 32 bits
Each octet = 8 bits
Each octet = 0–255
```

---

## 2. CIDR Notation

CIDR = **Classless Inter-Domain Routing**.

Example:

```text
10.0.0.0/16
```

`/16` means 16 bits are used for the network portion.

```text
Network bits = 16
Host bits    = 32 - 16 = 16
```

Total addresses:

```text
2^16 = 65,536
```

### Important rule

```text
Larger prefix number → Smaller network
Smaller prefix number → Larger network
```

Therefore:

```text
/16 > /24 > /26 > /28
```

in network size.

---

## 3. CIDR Cheat Sheet

| CIDR | Total IPv4 Addresses |
|---|---:|
| `/16` | 65,536 |
| `/20` | 4,096 |
| `/22` | 1,024 |
| `/24` | 256 |
| `/25` | 128 |
| `/26` | 64 |
| `/27` | 32 |
| `/28` | 16 |

### Formula

```text
Total addresses = 2^(32 - prefix)
```

Example:

```text
/26

2^(32-26)
= 2^6
= 64
```

---

# 4. Subnetting

Subnetting means dividing a larger network into smaller networks.

Example:

```text
VPC:
10.0.0.0/16

        |
        +-- 10.0.1.0/24
        +-- 10.0.2.0/24
        +-- 10.0.3.0/24
        +-- 10.0.4.0/24
```

This allows us to separate resources into different network tiers.

For example:

```text
Public Subnets
Application Subnets
Database Subnets
```

---

# 5. How Many /24 Subnets Fit in /16?

Start:

```text
10.0.0.0/16
```

Target:

```text
/24
```

Difference:

```text
24 - 16 = 8
```

Number of subnets:

```text
2^8 = 256
```

Therefore:

```text
1 × /16
=
256 × /24
```

---

# 6. Example: /24 → /26

Start:

```text
192.168.1.0/24
```

Create `/26` networks.

Difference:

```text
26 - 24 = 2
```

Number of subnets:

```text
2^2 = 4
```

The four networks are:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Each `/26` contains:

```text
64 total addresses
```

---

# 7. AWS Reserves 5 IPv4 Addresses

This is **very important for AWS interviews**.

For:

```text
10.0.1.0/24
```

AWS reserves:

```text
10.0.1.0
10.0.1.1
10.0.1.2
10.0.1.3
10.0.1.255
```

Therefore:

```text
256 total
- 5 reserved
----------------
251 usable
```

### AWS Reserved IPs

| Address | Purpose |
|---|---|
| `.0` | Network address |
| `.1` | VPC router |
| `.2` | DNS |
| `.3` | Reserved by AWS |
| Last address | Reserved by AWS |

> **AWS IPv4 rule:** Total addresses − 5 = usable addresses.

---

# 8. AWS Usable IP Cheat Sheet

| CIDR | Total | Usable |
|---|---:|---:|
| `/24` | 256 | 251 |
| `/25` | 128 | 123 |
| `/26` | 64 | 59 |
| `/27` | 32 | 27 |
| `/28` | 16 | 11 |

Example:

```text
/26

64 total
- 5 reserved
= 59 usable
```

---

# 9. Private IPv4 Ranges

The three main private IPv4 ranges are:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

A common AWS VPC design:

```text
10.0.0.0/16
```

with smaller subnet ranges inside it.

---

# 10. Practical VPC Subnet Design

Suppose:

```text
VPC:
10.0.0.0/16
```

### Public Subnets

```text
10.0.1.0/24   AZ-a
10.0.2.0/24   AZ-b
```

### Application Subnets

```text
10.0.11.0/24  AZ-a
10.0.12.0/24  AZ-b
```

### Database Subnets

```text
10.0.21.0/24  AZ-a
10.0.22.0/24  AZ-b
```

Architecture:

```text
              VPC
        10.0.0.0/16
              |
       +------+------+
       |             |
      AZ-a          AZ-b
       |             |
    +--+--+       +--+--+
    |  |  |       |  |  |
   Pub App DB    Pub App DB
```

### Benefits

- Network segmentation
- Easier routing
- Easier security
- Multi-AZ architecture
- Easier troubleshooting
- Room for growth

---

# 11. Choosing Subnet Size

Don't automatically use `/24` everywhere.

Ask:

> How many IP addresses do I need now and how many might I need later?

### Example: 50 IPs

```text
/26 = 64 total

64 - 5 = 59 usable
```

So `/26` can be sufficient.

### Example: 100 IPs

```text
/25 = 128 total

128 - 5 = 123 usable
```

So `/25` can be sufficient.

Always consider future growth.

---

# 12. CIDR Overlap

Avoid overlapping CIDRs when networks may need to communicate.

### Bad

```text
VPC-A:
10.0.0.0/16

VPC-B:
10.0.0.0/16
```

### Also Bad

```text
VPC-A:
10.0.0.0/16

VPC-B:
10.0.1.0/24
```

The second range exists inside the first range.

CIDR overlap can cause problems with:

- VPC Peering
- Transit Gateway
- VPN
- Hybrid networking
- On-premises connectivity

### Better

```text
Production:
10.0.0.0/16

Development:
10.1.0.0/16

Testing:
10.2.0.0/16
```

### DevOps Rule

> Plan your CIDR ranges before building a large AWS environment.

---

# 13. Useful Linux Networking Commands

As a DevOps engineer, these commands are useful for troubleshooting EC2 networking.

### Show IP addresses

```bash
ip addr
```

### Show routing table

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0
```

---

# 14. Important Interview Questions

## Q1. What does `/24` mean?

24 of the 32 IPv4 bits represent the network prefix.

```text
32 - 24 = 8 host bits

2^8 = 256 addresses
```

---

## Q2. How many usable IPv4 addresses are in an AWS `/24`?

```text
256 - 5 = 251
```

Answer:

```text
251 usable IPv4 addresses
```

---

## Q3. How many usable IPv4 addresses are in an AWS `/26`?

```text
2^(32-26)
= 64

64 - 5
= 59
```

Answer:

```text
59 usable addresses
```

---

## Q4. How many `/24` subnets fit inside `/16`?

```text
24 - 16 = 8

2^8 = 256
```

Answer:

```text
256
```

---

## Q5. Why should VPC CIDRs not overlap?

Because overlapping IP ranges can create routing problems when connecting VPCs, VPNs, Transit Gateways, or on-premises networks.

---

## Q6. Which is larger: `/24` or `/28`?

```text
/24 = 256 addresses
/28 = 16 addresses
```

Therefore:

```text
/24 is larger
```

---

# 15. DevOps Scenario

Suppose you need:

- Load Balancer
- Application servers
- Database
- Two Availability Zones

A simple design:

```text
VPC:
10.0.0.0/16

Public:
10.0.1.0/24
10.0.2.0/24

App:
10.0.11.0/24
10.0.12.0/24

DB:
10.0.21.0/24
10.0.22.0/24
```

This provides:

```text
Non-overlapping CIDRs
        ↓
Network segmentation
        ↓
Multi-AZ capability
        ↓
Simple routing
        ↓
Easier security
        ↓
Future expansion
```

---

# 16. Quick Revision

Memorize these:

```text
IPv4 = 32 bits

Total addresses:
2^(32 - prefix)

AWS usable IPv4:
Total - 5

/16 = 65,536
/24 = 256
/26 = 64
/28 = 16
```

And:

```text
Bigger prefix = Smaller network
Smaller prefix = Larger network
```

### Mental Model

```text
VPC CIDR
    ↓
Subnet CIDR
    ↓
IP Capacity
    ↓
Routing
    ↓
Security
```

---

# 17. Before Moving to Part 3

You should be able to answer these without looking at the notes:

1. What does `/16` mean?
2. How many addresses are in `/24`?
3. How many usable addresses are in an AWS `/24`?
4. How many `/24` networks fit inside `/16`?
5. Why does AWS reserve 5 IP addresses?
6. Why should VPC CIDRs not overlap?
7. How would you divide `10.0.0.0/16` into public, application, and database subnets?

If you can answer these, you're ready for:

> **Part 3 — Route Tables & Routing**
