## Configure a public VPC with an EC2 instance for internet access

- VPC → `devops-pub-vpc`
- Subnet → `devops-pub-subnet` (auto-assign public IP enabled)
- EC2 → `devops-pub-ec2` (t2.micro)
- Internet access → SSH (port 22 open)

### Step 1: Create VPC

- Name: devops-pub-vpc
- IPv4 CIDR: 10.0.0.0/16
- Leave others default

### Step 2: Create Public Subnet

- VPC: devops-pub-vpc
- Subnet name: devops-pub-subnet
- AZ: any (e.g., us-east-1a)
- CIDR: 10.0.1.0/24

- Enable Auto Public IP:

```bash
Select the subnet → Click Actions → Edit subnet settings → Enable: `Auto-assign public IPv4 address`
```

### Step 3: Create Internet Gateway

- Internet Gateways → Create
- Name: devops-igw

Attach IGW to VPC:

```bash
devops-igw → Actions → Attach to VPC → devops-pub-vpc
```

### Step 4: Create Route Table (Make Subnet Public)

- Route Tables → Create route table
- Name: devops-rt
- VPC: devops-pub-vpc

- Add Route to Internet:

```bash
Select route table → Routes → Edit
```

- Add:

```bash Destination  Target
0.0.0.0/0	Internet Gateway (devops-igw)
```

- Associate Subnet:

```bash
Subnet associations → Edit → Select devops-pub-subnet
```

## Step 5: Create Security Group

- EC2 → Security Groups → Create
- Name: devops-pub-sg
- VPC: devops-pub-vpc

- Inbound:

```bash
Type	Port	Source
SSH	    22	    0.0.0.0/0
```

## Step 6: Launch EC2 Instance

- EC2 → Instances → Launch Instance
- Name: devops-pub-ec2
- AMI: any Linux (Ubuntu / Amazon Linux)
- Instance type: t2.micro
- Key pair: select/create

- Network Settings:
  - VPC: devops-pub-vpc
  - Subnet: devops-pub-subnet
  - Auto-assign public IP: Enabled
  - Security group: devops-pub-sg

### Final Architecture

```bash
Internet
   ↓
Internet Gateway
   ↓
Public Subnet (Auto Public IP)
   ↓
EC2 (devops-pub-ec2)
```
