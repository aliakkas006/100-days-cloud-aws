## Task requirements

For Day 5 – Creating an EBS volume with the following specifications:

- Name: `devops-volume`
- Volume Type: `gp3`
- Size: `2 GiB`

---

## Purpose

- An Amazon EBS volume is a durable, block-level storage device that we can attach to our instances.
- After we attach a volume to an instance, we can use it as we would use a physical hard drive.
- EBS volumes are flexible.
- For current-generation volumes attached to current-generation instance types, we can dynamically increase size, modify the provisioned IOPS capacity, and change volume type on live production volumes.
- The volume and instance must be in the same Availability Zone
- Amazon EBS provides the following volume types: General Purpose SSD (`gp2` and `gp3`), Provisioned IOPS SSD (`io1` and `io2`), Throughput Optimized HDD (`st1`), Cold HDD (`sc1`), and Magnetic (`standard`)

---

## Using AWS CLI

- Pick an Availability Zone:
  - EBS volumes must be created in a specific Availability Zone (AZ), not just a region.

```bash
aws ec2 describe-availability-zones \
  --query "AvailabilityZones[0].ZoneName" \
  --output text
```

- Store it:

```bash
AZ=$(aws ec2 describe-availability-zones \
  --query "AvailabilityZones[0].ZoneName" \
  --output text)
```

- Create the EBS Volume:

```bash
aws ec2 create-volume \
  --availability-zone $AZ \
  --volume-type gp3 \
  --size 2 \
  --tag-specifications 'ResourceType=volume,Tags=[{Key=Name,Value=devops-volume}]'
```

- Verify the Volume:

```bash
aws ec2 describe-volumes \
  --filters "Name=tag:Name,Values=devops-volume" \
  --output table
```

| Section  | Field              | Value                    |
| -------- | ------------------ | ------------------------ |
| Volume   | AvailabilityZone   | us-east-1a               |
| Volume   | CreateTime         | 2025-11-30T05:54:36.194Z |
| Volume   | Encrypted          | False                    |
| Volume   | Iops               | 3000                     |
| Volume   | MultiAttachEnabled | False                    |
| Volume   | Size               | 2                        |
| Volume   | SnapshotId         |                          |
| Volume   | State              | available                |
| Volume   | Throughput         | 125                      |
| Volume   | VolumeId           | vol-08d7d64d021a3cdf6    |
| Volume   | VolumeType         | gp3                      |
| Operator | Managed            | False                    |
| Tags     | Name               | devops-volume            |
