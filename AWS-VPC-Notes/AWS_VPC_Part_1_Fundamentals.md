# AWS VPC — Part 1: Fundamentals and Core Concepts

> **Learning goal:** Build a strong foundation in Amazon VPC before moving into subnets, routing, Internet Gateway, NAT Gateway, security groups, NACLs, VPC peering, Transit Gateway, VPC endpoints, DNS, flow logs, and production architectures.

---

## 1. What is a VPC?

**VPC (Virtual Private Cloud)** is a logically isolated virtual network that you create inside AWS.

Think of a VPC as your own **private network inside AWS**.

In a traditional data center, you might have:

- Network
- IP address ranges
- Routers
- Firewalls
- Public/private networks
- Servers
- Routing rules

AWS VPC provides the cloud equivalent of these networking concepts.

### Simple definition

> A VPC is an isolated virtual network in AWS where you can launch and control resources such as EC2 instances.

---

# 2. Why do we need a VPC?

Suppose you launch an EC2 server.

You need to answer questions such as:

- What IP address should the server have?
- Should it be reachable from the Internet?
- Which other servers can communicate with it?
- How should Internet traffic reach it?
- How can a private server access the Internet?
- Which ports should be allowed?
- Can two applications communicate privately?
- How can two VPCs communicate?

VPC provides the networking foundation for answering these questions.

---

# 3. Real-World Analogy

Imagine a large company campus.

The **VPC** is the entire campus.

Inside the campus you have different buildings:

- Public building
- Private office
- Database room
- Management office

These are similar to **subnets**.

The campus has roads connecting buildings.

These are similar to **routes and route tables**.

There is a main gate to the outside world.

This is similar to an **Internet Gateway**.

There may be a controlled exit for private offices.

This is similar to a **NAT Gateway**.

Security guards control who can enter or leave.

These are similar to **security controls such as Security Groups and Network ACLs**.

This analogy will help you understand VPC instead of memorizing it.

---

# 4. VPC Components — Big Picture

A typical VPC can contain:

```text
                         Internet
                            |
                            |
                    Internet Gateway
                            |
                     +------+------+
                     |     VPC     |
                     |             |
              +------+-------------+------+
              |                             |
        Public Subnet                 Private Subnet
              |                             |
           EC2/Web                    EC2/App Server
              |                             |
        Public IP                    Private IP
                                            |
                                      Database Subnet
                                            |
                                         RDS/DB
```

Important components include:

1. VPC
2. CIDR block
3. Subnet
4. Availability Zone
5. Route table
6. Internet Gateway
7. NAT Gateway
8. Elastic IP
9. Security Group
10. Network ACL
11. VPC endpoints
12. DNS
13. VPC peering
14. Transit Gateway
15. VPC Flow Logs

We will study each of these separately.

---

# 5. VPC and Region

A VPC is created within an **AWS Region**.

For example:

```text
AWS Region: ap-south-1

        VPC
         |
    +----+----+
    |         |
   AZ-a      AZ-b
    |         |
  Subnet    Subnet
```

A VPC belongs to one Region.

However, a VPC can span multiple Availability Zones within that Region.

### Example

If you create a VPC in:

```text
ap-south-1
```

you can create subnets across multiple Availability Zones in Mumbai.

You cannot create a subnet from that VPC in another AWS Region.

---

# 6. VPC vs Availability Zone

This is an important interview concept.

### VPC

A VPC is **Regional**.

### Subnet

A subnet is associated with **one Availability Zone**.

Therefore:

```text
Region
   |
   +---- VPC
          |
          +---- AZ-a
          |      |
          |    Subnet A
          |
          +---- AZ-b
                 |
               Subnet B
```

A single VPC can contain subnets in multiple Availability Zones.

---

# 7. CIDR — The Foundation of VPC Networking

CIDR stands for:

> **Classless Inter-Domain Routing**

CIDR defines an IP address range.

Example:

```text
10.0.0.0/16
```

This could be the CIDR block of your VPC.

---

# 8. Understanding 10.0.0.0/16

The `/16` tells us how many bits are used for the network portion.

IPv4 contains:

```text
32 bits
```

Therefore:

```text
32 - 16 = 16
```

bits are available for host addresses.

Total addresses:

```text
2^16 = 65,536
```

So:

```text
10.0.0.0/16
```

contains 65,536 IPv4 addresses.

---

# 9. Common Private IPv4 Ranges

For internal AWS networks, you will commonly use private IPv4 ranges defined by RFC 1918.

### Range 1

```text
10.0.0.0/8
```

Range:

```text
10.0.0.0
to
10.255.255.255
```

### Range 2

```text
172.16.0.0/12
```

Range:

```text
172.16.0.0
to
172.31.255.255
```

### Range 3

```text
192.168.0.0/16
```

Range:

```text
192.168.0.0
to
192.168.255.255
```

These are private IP ranges.

---

# 10. Example VPC CIDR

Suppose we create:

```text
VPC CIDR:
10.0.0.0/16
```

We can divide it into smaller networks.

For example:

```text
VPC: 10.0.0.0/16

Subnet 1:
10.0.1.0/24

Subnet 2:
10.0.2.0/24

Subnet 3:
10.0.3.0/24

Subnet 4:
10.0.4.0/24
```

Conceptually:

```text
                 VPC
             10.0.0.0/16
                    |
       +------------+------------+
       |            |            |
       |            |            |
  10.0.1.0/24  10.0.2.0/24  10.0.3.0/24
    Subnet A     Subnet B     Subnet C
```

---

# 11. What is a Subnet?

A subnet is a smaller IP network inside a VPC.

Example:

```text
VPC:
10.0.0.0/16

Subnet:
10.0.1.0/24
```

The subnet gets a portion of the VPC's IP address space.

### Important

A subnet cannot span multiple Availability Zones.

One subnet belongs to exactly one Availability Zone.

---

# 12. Public Subnet vs Private Subnet

AWS does not provide a checkbox called:

> "Make this subnet public."

A subnet is considered public or private based primarily on its **routing configuration**.

### Public subnet

A subnet whose route table has a route to an Internet Gateway.

Example:

```text
Destination      Target

10.0.0.0/16      local
0.0.0.0/0        igw-xxxx
```

### Private subnet

A subnet that does not have a direct route to an Internet Gateway.

Example:

```text
Destination      Target

10.0.0.0/16      local
0.0.0.0/0        nat-xxxx
```

This distinction is extremely important.

---

# 13. Internet Gateway

An **Internet Gateway (IGW)** allows communication between resources in a VPC and the Internet, when the required routing and public addressing are configured.

Think of it as:

> The VPC's connection point to the public Internet.

Example:

```text
                    Internet
                       |
                       |
                 Internet Gateway
                       |
                    VPC
                       |
                Public Subnet
                       |
                     EC2
```

A VPC can have an Internet Gateway attached to it.

---

# 14. Route Table

A route table determines where network traffic should go.

Example:

```text
Destination      Target

10.0.0.0/16      local
0.0.0.0/0        igw-12345
```

Meaning:

### Rule 1

```text
10.0.0.0/16 -> local
```

Traffic destined for the VPC's own network stays inside the VPC.

### Rule 2

```text
0.0.0.0/0 -> Internet Gateway
```

Traffic to destinations outside the VPC goes toward the Internet Gateway.

---

# 15. The Meaning of 0.0.0.0/0

This is another very important networking concept.

```text
0.0.0.0/0
```

means:

> Any IPv4 destination.

It is commonly called the **default route**.

Example:

```text
0.0.0.0/0 -> Internet Gateway
```

means:

> If no more specific route matches the destination, send the traffic to the Internet Gateway.

---

# 16. Local Route

When a VPC is created, AWS provides a local route for the VPC CIDR.

Example:

```text
Destination      Target

10.0.0.0/16      local
```

This allows resources within the VPC to communicate using the VPC's internal networking, subject to security controls.

---

# 17. Public IP vs Private IP

An EC2 instance may have:

```text
Private IP:
10.0.1.10

Public IP:
13.x.x.x
```

The private IP is used for internal communication.

The public IP allows Internet-facing communication when routing and security rules permit it.

Do not confuse:

```text
Public subnet
```

with:

```text
Public IP
```

They are related concepts, but they are not the same thing.

---

# 18. What Makes an EC2 Instance Internet Reachable?

Having an EC2 instance inside a public subnet does not automatically make it reachable from the Internet.

For typical IPv4 Internet access, you need the appropriate combination of:

1. Public subnet routing
2. Internet Gateway attached to the VPC
3. Route to the Internet Gateway
4. Public IPv4 address or Elastic IP
5. Security Group allowing the required traffic
6. Network ACLs not blocking the traffic
7. Application/service listening on the required port

Example:

```text
Internet
   |
   v
Internet Gateway
   |
   v
Route Table
   |
   v
Public Subnet
   |
   v
EC2
   |
Security Group
   |
Application
```

---

# 19. NAT Gateway

A NAT Gateway allows resources in a private subnet to initiate connections to external networks, commonly the Internet, without making those resources directly Internet-reachable.

Typical architecture:

```text
                         Internet
                            |
                            |
                     Internet Gateway
                            |
                       Public Subnet
                            |
                       NAT Gateway
                            |
                       Private Subnet
                            |
                           EC2
```

The private EC2 can initiate outbound connections through the NAT Gateway.

The Internet cannot directly initiate a connection to the private EC2 through the NAT Gateway.

---

# 20. Why Do We Need Private Subnets?

Suppose you have a web application:

```text
Internet
   |
   v
Load Balancer
   |
   v
Application Servers
   |
   v
Database
```

You usually don't want your database directly exposed to the Internet.

A common architecture is:

```text
                Internet
                   |
                   v
              Load Balancer
                   |
            Public Subnets
                   |
                   v
          Private App Subnets
                   |
                   v
          Private DB Subnets
```

This creates multiple layers.

---

# 21. Security Group

A Security Group acts as a **stateful virtual firewall** for AWS resources such as EC2.

Example:

```text
Inbound:

HTTP   TCP 80     0.0.0.0/0
HTTPS  TCP 443    0.0.0.0/0
SSH    TCP 22     Your-IP/32
```

You should avoid unnecessarily exposing SSH to:

```text
0.0.0.0/0
```

---

# 22. Security Group Is Stateful

This is a common interview question.

Suppose an EC2 instance sends an outbound request.

If the response comes back, the Security Group understands the connection state and allows the response traffic when appropriate.

You generally don't need to create a separate inbound rule just for the return traffic of an allowed connection.

Therefore:

> Security Groups are stateful.

---

# 23. Network ACL

A **Network Access Control List (NACL)** operates at the subnet level.

It is another network security layer.

NACLs are:

> **Stateless**

Therefore, inbound and outbound traffic must be considered separately.

---

# 24. Security Group vs NACL

| Feature | Security Group | Network ACL |
|---|---|---|
| Level | Resource/ENI | Subnet |
| Stateful | Yes | No |
| Rules | Allow rules | Allow and deny rules |
| Return traffic | Automatically allowed for established connections | Must be explicitly allowed |
| Typical use | Fine-grained resource security | Subnet-level network filtering |

Remember:

```text
Security Group = Stateful
NACL = Stateless
```

---

# 25. Elastic IP

An **Elastic IP (EIP)** is a static public IPv4 address that you can associate with an AWS resource such as a NAT Gateway or supported network interface.

A normal automatically assigned public IPv4 address may change when certain resource lifecycle events occur.

An Elastic IP is designed for a persistent public IPv4 address.

Example:

```text
EC2
 |
 +---- Private IP: 10.0.1.10
 |
 +---- Elastic IP: x.x.x.x
```

Use public IPv4 addresses thoughtfully because they can incur charges depending on AWS's current pricing rules.

---

# 26. VPC DNS

VPC provides DNS capabilities that help resources resolve domain names.

Two important VPC attributes are:

```text
enableDnsSupport
enableDnsHostnames
```

### enableDnsSupport

Controls whether DNS resolution is supported in the VPC.

### enableDnsHostnames

Controls whether instances launched into the VPC can receive DNS hostnames under the applicable conditions.

These settings are important for many AWS services and architectures.

---

# 27. Default VPC

AWS accounts commonly have a **default VPC** in supported Regions.

It is designed to make it easy to launch resources without first building a custom network.

A default VPC generally includes:

- VPC
- Subnets
- Route tables
- Internet Gateway
- Default security group
- Default network ACL

For learning, the default VPC is useful.

For production, organizations commonly create carefully designed VPC architectures rather than relying blindly on defaults.

---

# 28. Default VPC vs Custom VPC

### Default VPC

Good for:

- Learning
- Quick experiments
- Simple workloads

### Custom VPC

Preferred when you need:

- Controlled IP ranges
- Multiple environments
- Public/private subnet separation
- Multi-AZ architecture
- Centralized networking
- Hybrid connectivity
- Strong network segmentation

---

# 29. Example Production-Style VPC

A basic production architecture might look like this:

```text
                         Internet
                            |
                            v
                    Internet Gateway
                            |
          +-----------------+-----------------+
          |                                   |
     Public Subnet A                    Public Subnet B
        AZ-a                                AZ-b
          |                                   |
     Load Balancer                       Load Balancer
          |                                   |
          +-----------------+-----------------+
                            |
                            v
                 Private App Subnets
                    |           |
                  AZ-a         AZ-b
                    |           |
                  EC2          EC2
                    |           |
                    +-----+-----+
                          |
                          v
                   Private DB Subnets
                    |           |
                  AZ-a         AZ-b
                    |           |
                   DB          DB
```

The exact implementation depends on the workload and AWS service being used.

---

# 30. High Availability and VPC

For production workloads, don't design everything in one Availability Zone if the workload requires high availability.

A common pattern is:

```text
Region
 |
 +------------------------------+
 |                              |
AZ-a                           AZ-b
 |                              |
Public Subnet                  Public Subnet
Private Subnet                 Private Subnet
DB Subnet                      DB Subnet
```

This helps reduce dependence on a single Availability Zone.

---

# 31. Important VPC Terms

You should become comfortable with these terms:

### VPC

Logical isolated network.

### CIDR

Defines the IP address range.

### Subnet

Smaller IP network inside a VPC.

### Availability Zone

Isolated location within an AWS Region.

### Route Table

Controls where network traffic is sent.

### Internet Gateway

Provides a path between the VPC and the Internet for appropriately configured resources.

### NAT Gateway

Provides outbound connectivity from private subnets without requiring direct inbound Internet connectivity to those private resources.

### Security Group

Stateful resource-level virtual firewall.

### Network ACL

Stateless subnet-level traffic filter.

### Elastic IP

Static public IPv4 address.

---

# 32. Traffic Flow Example

Let's understand a request from the Internet to an EC2 web server.

Suppose:

```text
EC2 private IP:
10.0.1.10

EC2 public IP:
13.x.x.x
```

The request path can conceptually be:

```text
User
 |
 | HTTP/HTTPS
 v
Internet
 |
 v
Internet Gateway
 |
 v
Route Table
 |
 v
Public Subnet
 |
 v
EC2 Network Interface
 |
 v
Security Group
 |
 v
Web Server
```

Every layer matters.

If the route is wrong, traffic fails.

If the Security Group blocks it, traffic fails.

If the application is not listening, traffic fails.

---

# 33. Private EC2 Internet Access

Suppose an EC2 instance is in:

```text
Private Subnet
```

and it needs to download packages from the Internet.

A typical architecture is:

```text
Private EC2
    |
    v
Private Route Table
    |
    v
NAT Gateway
    |
    v
Public Subnet Route Table
    |
    v
Internet Gateway
    |
    v
Internet
```

The private instance does not need a public IPv4 address for this outbound path.

---

# 34. Why NAT Gateway Must Be in a Public Subnet

A common conceptual mistake is:

> "I'll put the NAT Gateway inside the private subnet."

A NAT Gateway used for Internet egress should be placed in a **public subnet**.

Why?

Because it needs a route toward an Internet Gateway for Internet access.

Typical structure:

```text
Public Subnet
    |
NAT Gateway
    |
Internet Gateway
    |
Internet
```

Private subnet:

```text
Private Subnet
    |
EC2
    |
Route Table
    |
NAT Gateway
```

---

# 35. VPC Design Example

Suppose you are building an application called:

```text
mycompany.com
```

You could design:

```text
VPC:
10.0.0.0/16
```

Public subnets:

```text
10.0.1.0/24   AZ-a
10.0.2.0/24   AZ-b
```

Private application subnets:

```text
10.0.11.0/24  AZ-a
10.0.12.0/24  AZ-b
```

Private database subnets:

```text
10.0.21.0/24  AZ-a
10.0.22.0/24  AZ-b
```

Conceptually:

```text
VPC 10.0.0.0/16
|
+-- Public
|    +-- 10.0.1.0/24 AZ-a
|    +-- 10.0.2.0/24 AZ-b
|
+-- Application
|    +-- 10.0.11.0/24 AZ-a
|    +-- 10.0.12.0/24 AZ-b
|
+-- Database
     +-- 10.0.21.0/24 AZ-a
     +-- 10.0.22.0/24 AZ-b
```

This is a common conceptual pattern.

---

# 36. VPC CIDR Planning

CIDR planning is important because changing network design later can be difficult.

Before creating a VPC, consider:

- Number of subnets
- Number of Availability Zones
- Number of resources
- Future growth
- Connectivity with other VPCs
- On-premises network ranges
- VPN/Direct Connect requirements
- Overlapping CIDRs

### Avoid overlapping CIDRs

Suppose:

```text
VPC-A:
10.0.0.0/16
```

and:

```text
VPC-B:
10.0.0.0/16
```

Connecting these networks later can create problems because their IP ranges overlap.

A better design might be:

```text
VPC-A:
10.0.0.0/16

VPC-B:
10.1.0.0/16

VPC-C:
10.2.0.0/16
```

Good IP planning makes future networking much easier.

---

# 37. VPC for DevOps Engineers

As a DevOps engineer, VPC knowledge is extremely important.

You will encounter VPC when working with:

- EC2
- ECS
- EKS
- RDS
- ElastiCache
- Load Balancers
- Lambda
- API Gateway private integrations
- VPC endpoints
- NAT Gateway
- VPN
- Direct Connect
- Terraform
- CloudFormation
- CI/CD infrastructure
- Monitoring and logging

For example, when deploying an application using Terraform, you may define:

```text
VPC
 |
 +-- Subnets
 |
 +-- Route Tables
 |
 +-- Internet Gateway
 |
 +-- NAT Gateway
 |
 +-- Security Groups
 |
 +-- Load Balancer
 |
 +-- EC2/ECS/EKS
 |
 +-- Database
```

Therefore, VPC is not just an AWS networking topic.

It is a **core DevOps skill**.

---

# 38. Interview Questions — Part 1

## Q1. What is a VPC?

**Answer:**

A VPC is a logically isolated virtual network in AWS where we can launch and control AWS resources and define networking such as IP ranges, subnets, routing, and security.

---

## Q2. Is a VPC Regional or Availability-Zone specific?

**Answer:**

A VPC is Regional. It can span multiple Availability Zones within the Region.

---

## Q3. Can a subnet span multiple Availability Zones?

**Answer:**

No. A subnet belongs to exactly one Availability Zone.

---

## Q4. What makes a subnet public?

**Answer:**

A subnet is considered public when its associated route table has a route to an Internet Gateway, assuming the resources also have the necessary public addressing and security configuration.

---

## Q5. Is a public subnet automatically Internet accessible?

**Answer:**

No. Internet accessibility also depends on things such as public IP addressing, route configuration, Security Groups, NACLs, and the application itself.

---

## Q6. What is the purpose of an Internet Gateway?

**Answer:**

It provides the VPC with a path for Internet communication when the appropriate routing and public addressing are configured.

---

## Q7. What is the purpose of a NAT Gateway?

**Answer:**

A NAT Gateway allows resources in private subnets to initiate outbound connections to external networks, commonly the Internet, without giving those resources direct inbound Internet connectivity.

---

## Q8. Are Security Groups stateful?

**Answer:**

Yes.

---

## Q9. Are Network ACLs stateful?

**Answer:**

No. NACLs are stateless.

---

## Q10. What does 0.0.0.0/0 mean?

**Answer:**

It represents all IPv4 destinations and is commonly used as the default route.

---

# 39. DevOps Mental Model

Don't memorize VPC as a list of AWS services.

Think about it as a network:

```text
                 INTERNET
                     |
                     v
              Internet Gateway
                     |
                     v
                Route Table
                     |
          +----------+----------+
          |                     |
          v                     v
     Public Subnet         Private Subnet
          |                     |
          |                 NAT Gateway
          |                     |
          |                 Internet
          |
       EC2 / ALB
          |
    Security Group
          |
       Application
```

Then ask:

> **Where is the traffic coming from?**

> **Where is it going?**

> **Which route does it follow?**

> **Which security control evaluates it?**

> **Does the destination have the required connectivity?**

This way of thinking is much more useful than memorizing definitions.

---

# 40. What We Will Study Next

This VPC series will be divided into multiple parts so that each topic can be learned deeply.

### Part 1 — VPC Fundamentals

Covered in this file:

- What is VPC?
- Why VPC?
- Region and VPC
- Availability Zones
- CIDR
- Private IP ranges
- Subnets
- Public vs private subnet
- Route tables
- Internet Gateway
- NAT Gateway
- Security Groups
- NACL
- Elastic IP
- DNS basics
- Default VPC
- High availability
- VPC architecture
- VPC CIDR planning
- Interview questions

### Part 2 — CIDR and Subnetting Deep Dive

We will cover:

- IPv4 fundamentals
- Binary conversion
- CIDR calculation
- `/16`, `/20`, `/24`, `/26`, `/28`
- Network address
- Broadcast address
- Host addresses
- AWS reserved IP addresses
- Subnet sizing
- Subnet planning
- Practical subnetting exercises
- Interview questions

### Part 3 — Route Tables and Routing

We will cover:

- Local routes
- Default routes
- Route-table associations
- Longest-prefix matching
- Public routing
- Private routing
- Routing failures
- Route troubleshooting
- Real traffic-flow examples

### Part 4 — Internet Gateway, NAT Gateway and Internet Access

We will cover:

- IGW
- NAT Gateway
- NAT vs IGW
- Public vs private traffic
- NAT Gateway architecture
- NAT Gateway high availability
- NAT Gateway costs
- Troubleshooting Internet connectivity

### Part 5 — Security Groups and Network ACLs

We will cover:

- Stateful vs stateless
- Inbound rules
- Outbound rules
- Rule evaluation
- SG referencing
- NACL rule numbering
- Ephemeral ports
- Common mistakes
- Security troubleshooting

### Part 6 — VPC DNS and DHCP

We will cover:

- DNS resolution
- DNS hostnames
- Route 53 Resolver
- DHCP option sets
- Internal DNS
- EC2 hostname behavior

### Part 7 — VPC Endpoints

We will cover:

- Gateway endpoints
- Interface endpoints
- PrivateLink
- S3 private access
- DynamoDB private access
- Endpoint policies
- Cost and architecture considerations

### Part 8 — VPC Peering

We will cover:

- Same-account peering
- Cross-account peering
- Cross-region peering
- Routing
- Limitations
- Overlapping CIDRs
- Real architecture

### Part 9 — Transit Gateway

We will cover:

- Why Transit Gateway?
- Hub-and-spoke architecture
- Route tables
- Attachments
- Multi-VPC architecture
- Enterprise networking

### Part 10 — Hybrid Networking

We will cover:

- Site-to-Site VPN
- Client VPN
- Direct Connect
- On-premises connectivity
- BGP basics
- Hybrid architecture

### Part 11 — VPC Flow Logs and Troubleshooting

We will cover:

- Flow Logs
- ACCEPT
- REJECT
- Traffic analysis
- CloudWatch Logs
- S3
- Troubleshooting methodology

### Part 12 — Production VPC Architecture

We will build complete architectures involving:

```text
Internet
   |
ALB
   |
Public Subnets
   |
Private App Subnets
   |
Private Database Subnets
```

with:

- Multi-AZ
- NAT
- Security Groups
- NACLs
- Route Tables
- VPC endpoints
- Logging
- High availability
- Security
- Cost considerations

### Part 13 — VPC with Terraform

We will build the same architecture using Terraform.

Example:

```text
Terraform
   |
   +-- VPC
   +-- Subnets
   +-- Route Tables
   +-- IGW
   +-- NAT Gateway
   +-- Security Groups
   +-- VPC Endpoints
   +-- EC2
   +-- Load Balancer
```

This part will be especially important for your DevOps career.

---

# 41. Recommended Learning Order

Don't try to learn all VPC services simultaneously.

Follow this order:

```text
1. IP Addressing
       ↓
2. CIDR
       ↓
3. VPC
       ↓
4. Subnets
       ↓
5. Route Tables
       ↓
6. Internet Gateway
       ↓
7. NAT Gateway
       ↓
8. Security Groups
       ↓
9. NACL
       ↓
10. DNS
       ↓
11. VPC Endpoints
       ↓
12. VPC Peering
       ↓
13. Transit Gateway
       ↓
14. VPN / Direct Connect
       ↓
15. VPC Flow Logs
       ↓
16. Production Architecture
       ↓
17. Terraform VPC
```

---

# 42. Key Takeaways

Before moving to Part 2, make sure you can explain these without looking at notes:

- What is a VPC?
- Why do we need a VPC?
- Is a VPC Regional?
- Can a subnet span multiple AZs?
- What is CIDR?
- What is `10.0.0.0/16`?
- What is `0.0.0.0/0`?
- What is a subnet?
- What makes a subnet public?
- What is a route table?
- What is an Internet Gateway?
- What is a NAT Gateway?
- Why is NAT Gateway generally placed in a public subnet?
- What is a Security Group?
- What is a NACL?
- Which one is stateful?
- Which one is stateless?
- Why do we use private subnets?
- Why should VPC CIDRs be planned carefully?

If these concepts are clear, you have a solid foundation for deeper AWS networking.

---

## Final DevOps Advice

As an aspiring DevOps engineer, don't aim to simply remember:

> "NAT Gateway is used for private subnet."

Instead, train yourself to reason:

> "This EC2 is in a private subnet. It needs outbound Internet access. The subnet's route table sends `0.0.0.0/0` to the NAT Gateway. The NAT Gateway is in a public subnet and has a route toward the Internet Gateway. Therefore, the private EC2 can initiate outbound Internet traffic without having a public IP."

**That reasoning ability is what you should develop for DevOps interviews and real-world troubleshooting.**
