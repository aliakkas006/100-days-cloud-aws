## Task requirements

For Day 4 – Allocate a new Elastic IP address and assign it the name tag:

- Name: `datacenter-eip`

---

## Purpose

- An Elastic IP address is a static IPv4 address designed for dynamic cloud computing.
- An Elastic IP address is allocated to our AWS account, and is ours until we release it.
- By using an Elastic IP address, we can mask the failure of an instance or software by rapidly remapping the address to another instance in our account.
- Alternatively, we can specify the Elastic IP address in a DNS record for our domain, so that our domain points to our instance.
---

## Using AWS CLI

- Allocate the Elastic IP:

```bash
aws ec2 allocate-address \
  --domain vpc
```

- Tag the EIP with the name `datacenter-eip`

```bash
aws ec2 create-tags \
  --resources eipalloc-0fbf5eb8da80490fb \
  --tags Key=Name,Value=datacenter-eip
```

- Verify the EIP

```bash
aws ec2 describe-addresses \
  --filters "Name=tag:Name,Values=datacenter-eip" \
  --output table
```

| **AllocationId**           | **Domain** | **NetworkBorderGroup** | **PublicIp** | **PublicIpv4Pool** |
| -------------------------- | ---------- | ---------------------- | ------------ | ------------------ |
| eipalloc-0fbf5eb8da80490fb | vpc        | us-east-1              | 34.228.46.71 | amazon             |

| **Key** | **Value**      |
| ------- | -------------- |
| Name    | datacenter-eip |
