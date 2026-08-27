# Private IPv4 Ranges & Home Wi-Fi Router Notes

## 1. The Three Private IPv4 Ranges (RFC 1918)

RFC 1918 is a 1996 standard that reserves certain IP address blocks for private networks (home Wi-Fi, offices, cloud VPCs). These addresses:

- **Cannot be routed over the public internet** — ISPs and public routers drop this traffic.
- **Can be reused by millions of networks** at the same time without conflict, since they're only used privately.
- Require **NAT (Network Address Translation)** to actually reach the internet — your router swaps your private IP for its one public IP.

| Range | CIDR | Total Addresses | Typical Use |
|---|---|---|---|
| 10.0.0.0 – 10.255.255.255 | `10.0.0.0/8` | 16,777,216 | Large corporate / cloud networks |
| 172.16.0.0 – 172.31.255.255 | `172.16.0.0/12` | 1,048,576 | Medium-sized networks |
| 192.168.0.0 – 192.168.255.255 | `192.168.0.0/16` | 65,536 | Home & small office routers |

---

## 2. Does a Home Router Really Support 65,536 Devices?

**Short answer: mathematically yes, practically no.**

The `/16` block gives 65,536 total addresses, but a real home router will never actually let that many devices connect. Here's why:

### a) Hardware Limits
A home router is a small, cheap computer. It has limited RAM/CPU to track every connected device (its routing table). Most consumer routers realistically handle **30–250 devices** before running out of memory and freezing.

### b) Wi-Fi Airwave Bottleneck
Wi-Fi is a shared medium — only one device can transmit on a channel at a time. Thousands of wireless devices trying to talk at once causes constant collisions, packet loss, and the network effectively stops working.

### c) Broadcast Storms

**Beginner explanation (classroom analogy):**

Think of your router as a **teacher**, and every device (phone, laptop, smart TV) as a **student** in a classroom.

- With `192.168.0.0/16`, the classroom technically has **65,536 desks**.
- But devices are constantly "shouting" small announcements to the whole network — things like *"Hey, where's the printer?"* or *"I'm online!"* This is called **broadcast traffic**.
- With 20–50 students (devices), one shout now and then is no big deal.
- With 65,000 students all shouting constantly, the noise drowns out everything. Nobody can hear real conversations (real data) — this is a **broadcast storm**, and it paralyzes the network.
- Meanwhile, the teacher (router) also can't remember 65,000 names — their "brain" (CPU/RAM) overloads and crashes.

### How Home Routers Actually Work

To avoid this chaos, home routers don't use the full `/16` range. Instead they use a **`/24` subnet** (e.g., `192.168.1.0/24` or `192.168.0.0/24`):

- This shrinks the "classroom" to **254 usable desks** (`192.168.1.1` to `192.168.1.254`).
- Small enough for the router's hardware to manage comfortably.
- Matches what a typical home actually needs (phones, laptops, TVs, IoT devices, etc.).

---

## 3. AWS VPC Context (for comparison)

Cloud networks apply the same RFC 1918 ranges, just at a bigger, more controlled scale. A typical AWS VPC design:

- Uses a `/16` block (e.g., `10.0.0.0/16` → 65,536 IPs) as the overall VPC range.
- Splits it into smaller `/24` subnets across Availability Zones (public web tier, private app tier, private DB tier, etc.).
- Reserves **5 IPs per subnet** automatically (network address, VPC router, DNS server, future use, broadcast address) — so a `/24` subnet (256 IPs) only has **251 usable** IPs.

| Subnet Purpose | CIDR Block | Total IPs | Usable IPs | AZ |
|---|---|---|---|---|
| Public Web Tier (AZ 1) | 10.0.1.0/24 | 256 | 251 | us-east-1a |
| Public Web Tier (AZ 2) | 10.0.2.0/24 | 256 | 251 | us-east-1b |
| Private App Tier (AZ 1) | 10.0.10.0/24 | 256 | 251 | us-east-1a |
| Private App Tier (AZ 2) | 10.0.11.0/24 | 256 | 251 | us-east-1b |
| Private DB Tier (AZ 1) | 10.0.20.0/24 | 256 | 251 | us-east-1a |
| Private DB Tier (AZ 2) | 10.0.21.0/24 | 256 | 251 | us-east-1b |
| Reserved / Unassigned | 10.0.30.0/24+ | — | — | Spare room for growth |

**Best practices:** avoid overlapping with on-prem networks (for VPNs/Direct Connect), leave numbering gaps for future subnets, and size subnets appropriately for the workload.

---

## Key Takeaway

The size of an IP range (`/16`, `/24`, etc.) tells you the **theoretical maximum address space** — not how many devices real hardware can actually support. Home routers deliberately restrict themselves to a small `/24` slice of the much bigger `192.168.0.0/16` space to stay stable.
