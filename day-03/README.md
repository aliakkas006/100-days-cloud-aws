## Task requirements

For Day 2 – creating a new subnet inside the default VPC with the following requirement:

- Subnet Name: `xfusion-subnet`
- No other parameters (like CIDR, AZ, etc.) are specified in the task.

---

## Purpose

- Subnets allow for the logical division of a VPC's IP address range into smaller, manageable segments.
- Subnets facilitate the implementation of granular security controls.
- Each subnet is scoped to a single Availability Zone (AZ) within a region.
- Subnets provide the specific network locations where we launch and manage our AWS resources, such as EC2 instances, RDS databases, and Lambda functions.

---

## Using AWS CLI

- Check the default VPC CIDR:

```bash
aws ec2 describe-vpcs \
  --filters "Name=isDefault,Values=true" \
  --query "Vpcs[0].CidrBlock" \
  --output text
```

```bash
172.31.0.0/16
```

- Check existing subnets to avoid overlap:

```bash
aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=$DEFAULT_VPC_ID" \
  --query "Subnets[*].CidrBlock" \
  --output table
```

| Subnet CIDR Block |
| ----------------- |
| 172.31.0.0/20     |
| 172.31.16.0/20    |
| 172.31.32.0/20    |
| 172.31.48.0/20    |
| 172.31.64.0/20    |
| 172.31.80.0/20    |

- Find out the free CIDR:

Now, free CIDR which we can use for creating the subnet -

```bash
172.31.96.0/20
```

- Create the subnet:

```bash
aws ec2 create-subnet \
  --vpc-id $DEFAULT_VPC_ID \
  --cidr-block 172.31.96.0/20 \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=xfusion-subnet}]'
```

---

## Verification

```bash
{
    "Subnet": {
        "AvailabilityZoneId": "use1-az4",
        "MapCustomerOwnedIpOnLaunch": false,
        "OwnerId": "405306763660",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "Tags": [
            {
                "Key": "Name",
                "Value": "xfusion-subnet"
            }
        ],
        "SubnetArn": "arn:aws:ec2:us-east-1:405306763660:subnet/subnet-0cd131d129b451948",
        "EnableDns64": false,
        "Ipv6Native": false,
        "PrivateDnsNameOptionsOnLaunch": {
            "HostnameType": "ip-name",
            "EnableResourceNameDnsARecord": false,
            "EnableResourceNameDnsAAAARecord": false
        },
        "SubnetId": "subnet-0cd131d129b451948",
        "State": "available",
        "VpcId": "vpc-004b952a923eb9b60",
        "CidrBlock": "172.31.96.0/20",
        "AvailableIpAddressCount": 4091,
        "AvailabilityZone": "us-east-1d",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false
    }
}
```

| Subnet CIDR Block |
| ----------------- |
| 172.31.0.0/20     |
| 172.31.16.0/20    |
| 172.31.32.0/20    |
| 172.31.48.0/20    |
| 172.31.64.0/20    |
| 172.31.80.0/20    |
| 172.31.96.0/20    |
