## Enable internet access for private EC2 using NAT instance

AWS requires a NAT instance to be in a **public subnet**, have a route to an **Internet Gateway**, have **source/destination check disabled**, and have the private subnet route `0.0.0.0/0` pointing to the NAT instance. A public subnet is public because its route table sends internet-bound traffic to an Internet Gateway.

**Workflow of the NAT Instance Solution**:

```bash

Private EC2
(datacenter-priv-ec2)
        |
        | wants internet/S3 access
        v
Private Subnet
(datacenter-priv-subnet)
        |
        | Route table sends 0.0.0.0/0 traffic
        | to NAT Instance
        v
NAT Instance
(datacenter-nat-instance)
in Public Subnet
(datacenter-pub-subnet)
        |
        | NAT translates private IP traffic
        | into NAT instance public IP traffic
        v
Internet Gateway
        |
        v
Internet / Public S3 Bucket
(datacenter-nat-26886)
```

### Step 1: Create the public subnet

**VPC → Subnets → Create subnet**

| Field             | Value                               |
| ----------------- | ----------------------------------- |
| VPC               | `datacenter-priv-vpc`               |
| Subnet name       | `datacenter-pub-subnet`             |
| Availability Zone | Same AZ as `datacenter-priv-subnet` |
| IPv4 subnet CIDR  | `10.1.2.0/24`                       |

**Create subnet**:
`datacenter-pub-subnet`:

**Actions → Edit subnet settings**

Enable:

```text
Auto-assign public IPv4 address
```

---

### Step 2: Create or verify Internet Gateway

**VPC → Internet gateways**

1. **Create internet gateway**
2. Name: `datacenter-igw`
3. Click **Create**
4. Select it
5. **Actions → Attach to VPC**
6. Select `datacenter-priv-vpc`
7. Attached it

---

### Step 3: Create public route table

**VPC → Route tables → Create route table**

| Field | Value                 |
| ----- | --------------------- |
| Name  | `datacenter-pub-rt`   |
| VPC   | `datacenter-priv-vpc` |

Now select `datacenter-pub-rt` -

**Routes → Edit routes → Add route**

```text
Destination: 0.0.0.0/0
Target: Internet Gateway
Select: datacenter-igw
```

**Subnet associations → Edit subnet associations**

```text
datacenter-pub-subnet
```

---

### Step 4: Create NAT instance security group

**EC2 → Security Groups → Create security group**

| Field               | Value                             |
| ------------------- | --------------------------------- |
| Security group name | `datacenter-nat-sg`               |
| Description         | `Security group for NAT instance` |
| VPC                 | `datacenter-priv-vpc`             |

Inbound rule:

```text
Type: All traffic
Source: private subnet CIDR
```

Example:

```text
Source: 10.1.1.0/24
```

Outbound rule:

```text
All traffic: 0.0.0.0/0
```

Created security group.

---

### Step 5: Launch NAT instance

**EC2 → Instances → Launch instance**

| Field                 | Value                                            |
| --------------------- | ------------------------------------------------ |
| Name                  | `datacenter-nat-instance`                        |
| AMI                   | Amazon Linux 2023                                |
| Instance type         | `t3.micro` or available free/small type          |
| Key pair              | Proceed without key pair, or select existing key |
| VPC                   | `datacenter-priv-vpc`                            |
| Subnet                | `datacenter-pub-subnet`                          |
| Auto-assign public IP | Enable                                           |
| Security group        | `datacenter-nat-sg`                              |

In the **Advanced details** -

In **User data**:

```bash
#!/bin/bash
set -eux

dnf install -y iptables-services

systemctl enable --now iptables

cat > /etc/sysctl.d/99-nat.conf <<EOF
net.ipv4.ip_forward=1
EOF

sysctl --system

IFACE=$(ip route | awk '/default/ {print $5; exit}')

iptables -t nat -A POSTROUTING -o "$IFACE" -j MASQUERADE
iptables -F FORWARD
iptables -A FORWARD -i "$IFACE" -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -o "$IFACE" -j ACCEPT

service iptables save
```

Amazon Linux 2023 does not include old NAT instance behavior by default, so installing/enabling `iptables-services`, enabling IP forwarding, and adding a MASQUERADE rule is needed for NAT behavior. AWS’s NAT instance guide also notes disabling source/destination check for NAT instances.

Finally, **Launch instance**.

---

### Step 6: Disable source/destination check

After the NAT instance is running:

**EC2 → Instances → Select `datacenter-nat-instance`**

Then:

**Actions → Networking → Change source/destination check**

then:

```text
Stop / Disable
```

This is very important. Without this, AWS will block the instance from forwarding traffic for the private EC2.

---

### Step 7: Update private subnet route table

**VPC → Subnets**

```text
datacenter-priv-subnet
```

**Routes → Edit routes → Add route**

```text
Destination: 0.0.0.0/0
Target: Instance
Select: datacenter-nat-instance
```

---

### Step 8: Verify S3 upload

Wait around 1–2 minutes because the cron job runs every minute.

**S3 → Buckets → datacenter-nat-26886**

```text
datacenter-test.txt
```
