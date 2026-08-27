# AWS VPC — Part 3: Route Tables & Routing

> **Goal:** Understand how traffic moves inside and outside a VPC using route tables, routes, gateways, and targets.

---

## 1. What Is Routing?

Routing is the process of deciding:

> **Where should network traffic go?**

A route contains:

```text
Destination CIDR → Target
```

Example:

```text
0.0.0.0/0 → Internet Gateway
```

Basic flow:

```text
EC2
 ↓
Route Table
 ↓
Target
```

---

## 2. What Is a Route Table?

A **route table** contains rules that determine where traffic from a subnet should go.

Example:

```text
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         igw-xxxxx
```

Meaning:

```text
10.0.0.0/16 → Stay inside the VPC
0.0.0.0/0    → Send to Internet Gateway
```

### Important

A subnet is associated with a route table:

```text
Subnet
  ↓
Route Table
  ↓
Route
  ↓
Target
```

---

## 3. The `local` Route

When you create a VPC, AWS automatically adds a local route.

For:

```text
VPC:
10.0.0.0/16
```

the route table contains:

```text
Destination       Target
10.0.0.0/16       local
```

This allows communication within the VPC CIDR.

Example:

```text
EC2-A
10.0.1.10
   |
   | local route
   |
EC2-B
10.0.2.10
```

---

## 4. What Is `0.0.0.0/0`?

`0.0.0.0/0` means:

> **All IPv4 destinations**

It is called the **default route**.

Example:

```text
Destination       Target
0.0.0.0/0         Internet Gateway
```

Traffic that does not match a more specific route uses this route.

---

## 5. Longest Prefix Match

Suppose a route table has:

```text
10.0.0.0/16  → local
10.0.1.0/24  → some-target
0.0.0.0/0    → internet
```

For destination:

```text
10.0.1.50
```

AWS chooses:

```text
10.0.1.0/24
```

because it is more specific.

### Rule

> **The most specific matching route wins.**

---

## 6. Public Subnet Route Table

A typical public subnet route table:

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

Flow:

```text
EC2
 ↓
Public Subnet
 ↓
Route Table
 ↓
Internet Gateway
 ↓
Internet
```

A subnet is generally considered **public** when its route table has a route to an Internet Gateway.

For an EC2 instance to communicate directly with the internet, it also needs appropriate public addressing and security configuration.

---

## 7. Private Subnet Route Table

A private subnet normally does not have a direct route to an Internet Gateway.

For outbound internet access:

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         NAT Gateway
```

Flow:

```text
Private EC2
     ↓
Private Route Table
     ↓
NAT Gateway
     ↓
Internet Gateway
     ↓
Internet
```

This allows private resources to initiate outbound internet connections without being directly reachable from the internet.

---

## 8. Public vs Private Route Table

| Feature | Public Subnet | Private Subnet |
|---|---|---|
| Local route | Yes | Yes |
| Direct IGW route | Yes | No |
| NAT Gateway route | Usually no | Often yes |
| Direct internet access | Possible | No direct access |
| Common resources | ALB, bastion | App servers, databases |

---

## 9. Internet Gateway (IGW)

An **Internet Gateway** connects a VPC to the internet.

Typical path:

```text
Internet
   ↓
Internet Gateway
   ↓
Public Subnet
   ↓
EC2
```

The route table needs:

```text
0.0.0.0/0 → IGW
```

### Important

An Internet Gateway is:

- Attached to a VPC
- Managed by AWS
- Required for direct internet connectivity from a public subnet
- Horizontally scalable

---

## 10. NAT Gateway

A **NAT Gateway** allows resources in private subnets to initiate outbound connections to the internet.

Example:

```text
Private EC2
    ↓
Private Route Table
    ↓
NAT Gateway
    ↓
Internet Gateway
    ↓
Internet
```

Typical route:

```text
0.0.0.0/0 → NAT Gateway
```

### Important

NAT Gateway provides:

```text
Private → Internet
```

It does not provide:

```text
Internet → Private EC2
```

---

## 11. Route Table Association

A route table does not automatically apply to every subnet.

You associate a route table with a subnet.

Example:

```text
Public Route Table
        |
        +---- Public Subnet A
        |
        +---- Public Subnet B
```

Another:

```text
Private Route Table
        |
        +---- Private Subnet A
        |
        +---- Private Subnet B
```

### Main idea

> **The subnet determines which route table controls its traffic.**

---

## 12. Main Route Table

Every VPC has a **main route table**.

If a subnet is not explicitly associated with another route table, it uses the VPC's main route table.

For production architectures, explicitly associating subnets with appropriate route tables makes the design easier to understand and manage.

---

## 13. Route Tables for a 2-Tier Application

Suppose:

```text
VPC:
10.0.0.0/16
```

### Public Route Table

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

Associated with:

```text
10.0.1.0/24
10.0.2.0/24
```

### Private Route Table

```text
Destination       Target
10.0.0.0/16       local
0.0.0.0/0         NAT Gateway
```

Associated with:

```text
10.0.11.0/24
10.0.12.0/24
```

---

## 14. Database Subnets

Database subnets normally should not have internet access.

Example:

```text
Database Route Table

Destination       Target
10.0.0.0/16       local
```

Normally there should be no:

```text
0.0.0.0/0 → Internet Gateway
```

for a database subnet.

This helps keep databases isolated.

---

## 15. Traffic Flow Example

Suppose:

```text
EC2:
10.0.11.10

Destination:
8.8.8.8
```

Private route table:

```text
10.0.0.0/16 → local
0.0.0.0/0   → NAT Gateway
```

`8.8.8.8` does not belong to `10.0.0.0/16`.

Therefore:

```text
8.8.8.8
   ↓
0.0.0.0/0
   ↓
NAT Gateway
   ↓
Internet Gateway
   ↓
Internet
```

---

## 16. Route Table Troubleshooting

If an EC2 instance cannot reach the internet, check:

### 1. Subnet association

Is the correct route table associated with the subnet?

### 2. Route

For public:

```text
0.0.0.0/0 → IGW
```

For private:

```text
0.0.0.0/0 → NAT Gateway
```

### 3. Internet Gateway

Is the IGW attached to the VPC?

### 4. NAT Gateway

For private subnet internet access:

- NAT Gateway must exist
- NAT Gateway should be in a public subnet
- Public subnet must have an IGW route
- Private subnet must route to the NAT Gateway

### 5. Security Group

Check outbound rules.

### 6. Network ACL

Check subnet-level inbound/outbound rules.

### 7. EC2 addressing

For direct public internet access, verify that the instance has appropriate public addressing.

---

## 17. Common Mistakes

### Mistake 1

Putting:

```text
0.0.0.0/0 → IGW
```

on a database subnet when internet access is not required.

### Mistake 2

Creating a NAT Gateway but forgetting:

```text
Private subnet
    ↓
0.0.0.0/0 → NAT Gateway
```

### Mistake 3

Putting a NAT Gateway in a private subnet.

A NAT Gateway intended for internet egress should be placed in a public subnet with a route to the IGW.

### Mistake 4

Forgetting subnet-to-route-table association.

### Mistake 5

Assuming a route table controls an entire VPC.

A route table is associated with subnets.

---

## 18. DevOps Interview Questions

### Q1. What is a route table?

A route table contains rules that determine where network traffic from associated subnets should go.

### Q2. What is the `local` route?

It allows communication within the VPC CIDR.

### Q3. What does `0.0.0.0/0` mean?

It represents all IPv4 destinations and is commonly used as the default route.

### Q4. What makes a subnet public?

A route from the subnet's route table to an Internet Gateway makes it internet-routable; resources also need appropriate addressing and security configuration.

### Q5. Can a private subnet access the internet?

Yes, commonly through a NAT Gateway.

### Q6. Can the internet directly initiate a connection to an instance through a NAT Gateway?

No. NAT Gateway is used for outbound connections initiated from private resources.

### Q7. What is longest prefix match?

When multiple routes match a destination, the most specific route is selected.

Example:

```text
10.0.0.0/16
10.0.1.0/24
```

For `10.0.1.50`, `/24` wins.

---

## 19. Quick Revision

Remember:

```text
Route Table
    ↓
Destination
    ↓
Target
```

Important routes:

```text
10.0.0.0/16 → local
0.0.0.0/0   → IGW / NAT / other target
```

### Public

```text
Public Subnet
      ↓
Route Table
      ↓
0.0.0.0/0 → IGW
```

### Private

```text
Private Subnet
      ↓
Route Table
      ↓
0.0.0.0/0 → NAT Gateway
```

### Database

```text
Database Subnet
      ↓
Route Table
      ↓
Usually only internal/local routes
```

---

## 20. Before Part 4

You should be able to explain:

1. What is a route table?
2. What is the `local` route?
3. What does `0.0.0.0/0` mean?
4. What makes a subnet public?
5. How does a private subnet access the internet?
6. What is the difference between an Internet Gateway and NAT Gateway?
7. What is longest prefix match?
8. Why should database subnets normally avoid direct internet routes?
9. How would you troubleshoot an EC2 instance with no internet connectivity?

If you can answer these, you're ready for:

> **Part 4 — Internet Gateway, NAT Gateway & Internet Connectivity**
