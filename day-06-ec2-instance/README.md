## Task requirements

For Day 6 – Creating a new EC2 instance with the following requirements:

- Instance Name: `datacenter-ec2`
- AMI: `Amazon Linux`
- Instance Type: `t2.micro`
- Key Pair: Create a new RSA key pair named `datacenter-kp`
- Security Group: Use the `default` security group

---

## Purpose

- An Amazon EC2 instance is a virtual server in the AWS cloud environment.
- We have full control over our instance, from the time that we first start it (referred to as launching an instance) until we delete it (referred to as terminating an instance).
- We can choose from a variety of operating systems when we launch our instance.
- Ee can connect to our instance and customize it to meet our needs. For example, you can configure the operating system, install operating system updates, and install applications on our instance.

---

## Using AWS CLI

- Create the RSA Key Pair:

```bash
aws ec2 create-key-pair \
  --key-name datacenter-kp \
  --key-type rsa \
  --query "KeyMaterial" \
  --output text > datacenter-kp.pem
```

- Secure the key:

```bash
chmod 400 datacenter-kp.pem
```

- Get Amazon Linux AMI ID:

```bash
AMI_ID=$(aws ec2 describe-images \
  --owners amazon \
  --filters "Name=name,Values=amzn2-ami-hvm-*-x86_64-gp2" "Name=state,Values=available" \
  --query "sort_by(Images, &CreationDate)[-1].ImageId" \
  --output text)
```

- Get Default Security Group ID:

```bash
DEFAULT_SG=$(aws ec2 describe-security-groups \
  --filters "Name=group-name,Values=default" \
  --query "SecurityGroups[0].GroupId" \
  --output text)
```

- Launch the EC2 Instance:

```bash
aws ec2 run-instances \
  --image-id $AMI_ID \
  --instance-type t2.micro \
  --key-name datacenter-kp \
  --security-group-ids $DEFAULT_SG \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=datacenter-ec2}]'
```

- Verify the EC2 Instance:

```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=datacenter-ec2" \
  --output table
```

## DescribeInstances

### Reservations

| Field         | Value               |
| ------------- | ------------------- |
| OwnerId       | 059254148810        |
| ReservationId | r-011792eb6aa1a1ee5 |

### Instances

| Field                    | Value                                    |
| ------------------------ | ---------------------------------------- |
| AmiLaunchIndex           | 0                                        |
| Architecture             | x86_64                                   |
| BootMode                 | uefi-preferred                           |
| ClientToken              | 2329681f-685e-4a85-8f2b-f9d8c0df92da     |
| CurrentInstanceBootMode  | legacy-bios                              |
| EbsOptimized             | False                                    |
| EnaSupport               | True                                     |
| Hypervisor               | xen                                      |
| ImageId                  | ami-0fa3fe0fa7920f68e                    |
| InstanceId               | i-023a18d91fa708831                      |
| InstanceType             | t2.micro                                 |
| KeyName                  | datacenter-kp                            |
| LaunchTime               | 2025-11-30T06:48:23.000Z                 |
| PlatformDetails          | Linux/UNIX                               |
| PrivateDnsName           | ip-172-31-31-131.ec2.internal            |
| PrivateIpAddress         | 172.31.31.131                            |
| PublicDnsName            | ec2-3-90-221-137.compute-1.amazonaws.com |
| PublicIpAddress          | 3.90.221.137                             |
| RootDeviceName           | /dev/xvda                                |
| RootDeviceType           | ebs                                      |
| SourceDestCheck          | True                                     |
| StateTransitionReason    |                                          |
| SubnetId                 | subnet-0dc06d93b68cb19d8                 |
| UsageOperation           | RunInstances                             |
| UsageOperationUpdateTime | 2025-11-30T06:48:23.000Z                 |
| VirtualizationType       | hvm                                      |
| VpcId                    | vpc-00c379bb87280d318                    |

### BlockDeviceMappings

| DeviceName | VolumeId              | AttachTime               | DeleteOnTermination | Status   |
| ---------- | --------------------- | ------------------------ | ------------------- | -------- |
| /dev/xvda  | vol-0ad3812dee9082ed6 | 2025-11-30T06:48:23.000Z | True                | attached |

### CapacityReservationSpecification

| Field                         | Value |
| ----------------------------- | ----- |
| CapacityReservationPreference | open  |

### CpuOptions

| Field          | Value |
| -------------- | ----- |
| CoreCount      | 1     |
| ThreadsPerCore | 1     |

### EnclaveOptions

| Field   | Value |
| ------- | ----- |
| Enabled | False |

### HibernationOptions

| Field      | Value |
| ---------- | ----- |
| Configured | False |

### MaintenanceOptions

| Field           | Value   |
| --------------- | ------- |
| AutoRecovery    | default |
| RebootMigration | default |

### MetadataOptions

| Field                   | Value    |
| ----------------------- | -------- |
| HttpEndpoint            | enabled  |
| HttpProtocolIpv6        | disabled |
| HttpPutResponseHopLimit | 2        |
| HttpTokens              | required |
| InstanceMetadataTags    | disabled |
| State                   | applied  |

### Monitoring

| Field | Value    |
| ----- | -------- |
| State | disabled |

### NetworkInterfaces

| Field              | Value                         |
| ------------------ | ----------------------------- |
| Description        |                               |
| InterfaceType      | interface                     |
| MacAddress         | 0a:ff:c9:af:49:a3             |
| NetworkInterfaceId | eni-09e431664c7c55089         |
| OwnerId            | 059254148810                  |
| PrivateDnsName     | ip-172-31-31-131.ec2.internal |
| PrivateIpAddress   | 172.31.31.131                 |
| SourceDestCheck    | True                          |
| Status             | in-use                        |
| SubnetId           | subnet-0dc06d93b68cb19d8      |
| VpcId              | vpc-00c379bb87280d318         |

#### Association (Network Interface)

| Field         | Value                                    |
| ------------- | ---------------------------------------- |
| IpOwnerId     | amazon                                   |
| PublicDnsName | ec2-3-90-221-137.compute-1.amazonaws.com |
| PublicIp      | 3.90.221.137                             |

### Groups

| Field     | Value                |
| --------- | -------------------- |
| GroupId   | sg-03487b55f4a41e624 |
| GroupName | launch-wizard-1      |

### Operator

| Field   | Value |
| ------- | ----- |
| Managed | False |

### Placement

| Field            | Value      |
| ---------------- | ---------- |
| AvailabilityZone | us-east-1c |
| GroupName        |            |
| Tenancy          | default    |

### SecurityGroups

| Field     | Value                |
| --------- | -------------------- |
| GroupId   | sg-03487b55f4a41e624 |
| GroupName | launch-wizard-1      |

### State

| Field | Value   |
| ----- | ------- |
| Code  | 16      |
| Name  | running |

### Tags

| Key  | Value          |
| ---- | -------------- |
| Name | datacenter-ec2 |
