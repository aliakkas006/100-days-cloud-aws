## Task requirements

For Day 7 - Modify the existing EC2 instance named `devops-ec2` by:

- Changing its instance type from `t2.micro` to `t2.nano`.
- Ensuring that the instance is `running` after the change.

---

## Purpose

- Scaling and Performance Optimization
- Cost Efficiency
- Adapting to Evolving Workloads and Technology

---

## Using AWS CLI

- Get the Instance ID of devops-ec2:

```bash
INSTANCE_ID=$(aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=devops-ec2" \
  --query "Reservations[0].Instances[0].InstanceId" \
  --output text)
```

- Stop the Instance (required before changing type):

```bash
aws ec2 stop-instances --instance-ids $INSTANCE_ID
```

- Change the Instance Type to t2.nano :

```bash
aws ec2 modify-instance-attribute \
  --instance-id $INSTANCE_ID \
  --instance-type "{\"Value\": \"t2.nano\"}"
```

- Start the Instance Again:

```bash
aws ec2 start-instances --instance-ids $INSTANCE_ID
```

- Verify:

```bash
aws ec2 describe-instances \
  --instance-ids $INSTANCE_ID \
  --query "Reservations[0].Instances[0].[InstanceId,InstanceType,State.Name]" \
  --output table
```

---

## Describe Instances (Summary)

| InstanceId          | InstanceType | State   |
| ------------------- | ------------ | ------- |
| i-0319e43737fb02715 | t2.nano      | running |
