## Establishing Secure Communication Between Public and Private VPCs via VPC Peering

- Peer `Default VPC` ↔ `datacenter-private-vpc`
- Enable routing between CIDRs
- Allow `public EC2` → `private EC2` communication
- Verify via `SSH + ping`

### Step 1: Identify CIDRs

- Private VPC: `10.1.0.0/16` (given)
- Public VPC: `172.31.0.0/16` (default)

### Step 2: Create VPC Peering Connection

- VPC → Peering Connections → Create peering connection
- Configure:
  - Name: `datacenter-vpc-peering`
  - Requester VPC: `Default VPC`
  - Accepter VPC: `datacenter-private-vpc`

- Accept the Peering:
  - Peering connection → Click Actions → Accept request

✔ Status become Active

### Step 3: Update Route Tables

- **Default VPC Route Table**:
  - Route Tables → Route table associated with public subnet
  - Routes → Edit routes

```bash
Destination  Target
10.1.0.0/16  Peering connection
```

- **Private VPC Route Table**:
  - Route Tables → Route table associated with datacenter-private-subnet
  - Routes → Edit routes

```bash
Destination    Target
172.31.0.0/16  Peering connection
```

### Step 4: Update Security Groups

- **Private EC2 Security Group**: Allow traffic from public VPC
- Add inbound rules:

```bash
Type  Protocol  Source
ICMP  All       Default VPC CIDR (e.g., 172.31.0.0/16)
SSH   TCP 22    Default VPC CIDR
```

- **Public EC2 Security Group**: (for SSH from aws-client)

```bash
Type  Port  Source
SSH   22    My IP (or 0.0.0.0/0 required by lab)
```

### Step 5: Setup SSH Access (aws-client → public EC2)

- On aws-client:

```bash
cat /root/.ssh/id_rsa.pub
```

- On datacenter-public-ec2:

```bash
mkdir -p /home/ec2-user/.ssh
vim /home/ec2-user/.ssh/authorized_keys (paste public key)

chmod 700 /home/ec2-user/.ssh
chmod 600 /home/ec2-user/.ssh/authorized_keys
chown -R ec2-user:ec2-user /home/ec2-user/.ssh
```

### Step 6: Test Connectivity

- SSH into public EC2:

```bash
ssh ec2-user@54.232.200.77
```

- Ping private EC2:

```bash
ping 10.0.2.17
```

### Final Architecture

```bash
aws-client
    ↓ SSH
datacenter-public-ec2 (Default VPC)
    ↓ (via VPC Peering)
datacenter-private-ec2 (Private VPC)

```

**VPC Peering = private communication over AWS network (no internet)**
