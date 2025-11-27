## Task requirements

For Day 2 – creating a security group under default VPC in AWS with the following requirements:
1. Security Group Name: `datacenter-sg`
2. Description: Security group for Nautilus App Servers
3. Inbound Rules:
    - Rule 1:
        - Type: HTTP
        - Protocol: TCP
        - Port: 80
        - Source: 0.0.0.0/0

    - Rule 2:
        - Type: SSH
        - Protocol: TCP
        - Port: 22
        - Source: 0.0.0.0/0

The security group must be created inside the `default VPC` only.

---

## Purpose

A security group controls the traffic that is allowed to reach and leave the resources that it is associated with. For example, after we associate a security group with an EC2 instance, it controls the inbound and outbound traffic for the instance.

When we create a VPC, it comes with a default security group. We can create additional security groups for a VPC, each with their own inbound and outbound rules. We can specify the source, port range, and protocol for each inbound rule. We can specify the destination, port range, and protocol for each outbound rule.


---

## Using AWS CLI

- Get the Default VPC ID:

```bash
aws ec2 describe-vpcs \
  --filters "Name=isDefault,Values=true" \
  --query "Vpcs[0].VpcId" \
  --output text
```
- Save the VPC ID:

```bash
DEFAULT_VPC_ID=$(aws ec2 describe-vpcs \
  --filters "Name=isDefault,Values=true" \
  --query "Vpcs[0].VpcId" \
  --output text)
```

- Create the Security Group:

```bash
aws ec2 create-security-group \
  --group-name datacenter-sg \
  --description "Security group for Nautilus App Servers" \
  --vpc-id $DEFAULT_VPC_ID
```

- Save the Security Group ID:

```bash
SG_ID=$(aws ec2 describe-security-groups \
  --filters "Name=group-name,Values=datacenter-sg" \
  --query "SecurityGroups[0].GroupId" \
  --output text)
```

- Add Inbound Rule – HTTP (Port 80)

```bash
aws ec2 authorize-security-group-ingress \
  --group-id $SG_ID \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0
```

- Add Inbound Rule – SSH (Port 22)

```bash
aws ec2 authorize-security-group-ingress \
  --group-id $SG_ID \
  --protocol tcp \
  --port 22 \
  --cidr 0.0.0.0/0
```

---

## Verification

```bash
aws ec2 describe-security-groups \
  --group-ids $SG_ID \
  --output table
```

| Field                | Value                                                                  |
| -------------------- | ---------------------------------------------------------------------- |
| **Description**      | Security group for Nautilus App Servers                                |
| **GroupId**          | sg-0563d99ba9542c062                                                   |
| **GroupName**        | datacenter-sg                                                          |
| **OwnerId**          | 332675590143                                                           |
| **SecurityGroupArn** | arn:aws:ec2:us-east-1:332675590143:security-group/sg-0563d99ba9542c062 |
| **VpcId**            | vpc-0c1f38b110c9a5ce2                                                  |

- IpPermissions inbound:

| Field          | Value |
| -------------- | ----- |
| **FromPort**   | 80    |
| **IpProtocol** | tcp   |
| **ToPort**     | 80    |
|
| **FromPort**   | 22    |
| **IpProtocol** | tcp   |
| **ToPort**     | 22    |

- IpPermissions default outbound:

| Field          | Value |
| -------------- | ----- |
| **IpProtocol** | -1    |
