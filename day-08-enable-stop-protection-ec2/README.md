## Task requirements

For Day 8 - Enable stop protection on the `EC2` instance named `xfusion-ec2` in the `us-east-1` region.

---

## Purpose

- The purpose of enabling stop protection on an AWS EC2 instance is to prevent accidental termination, which is particularly important for instances running critical workloads or containing important data on their instance store volumes.
- Prevents accidental downtime
- Protects against automation mistakes
- Adds a safety layer for critical workloads

---

## Using AWS CLI

- Get the Instance ID of xfusion-ec2:

```bash
INSTANCE_ID=$(aws ec2 describe-instances \
  --region us-east-1 \
  --filters "Name=tag:Name,Values=xfusion-ec2" \
  --query "Reservations[0].Instances[0].InstanceId" \
  --output text)
```

- Enable Stop Protection

```bash
aws ec2 modify-instance-attribute \
  --region us-east-1 \
  --instance-id $INSTANCE_ID \
  --disable-api-stop
```

This enables termination/stop protection, preventing the instance from being stopped through API calls unless the protection is disabled.

- Verify:

```bash
aws ec2 describe-instance-attribute \
  --region us-east-1 \
  --instance-id $INSTANCE_ID \
  --attribute disableApiStop
```

```bash
{
    "InstanceId": "i-0c728c1a1fda07d20",
    "DisableApiStop": {
        "Value": true
    }
}
```
