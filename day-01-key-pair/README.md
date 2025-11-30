## Task requirements

For Day 1 – creating a key pair in AWS with name `devops-kp` and type `rsa`, we can do it in multiple ways: AWS Management Console, AWS CLI, or Terraform. Since it’s a practice task, the CLI approach is fastest and most common.

## Purpose

A key pair, consisting of a public key and a private key, is a set of security credentials that we use to prove our identity when connecting to an Amazon EC2 instance. For Linux instances, the private key allows us to securely SSH into our instance.

## Using AWS CLI

- Open the terminal where AWS CLI is configured with the credentials.
- Then, Run this command:

```bash
aws ec2 create-key-pair \
    --key-name devops-kp \
    --key-type rsa \
    --query 'KeyMaterial' \
    --output text > devops-kp.pem
```

- Secure the key file:

```bash
chmod 400 devops-kp.pem
```

## Verify the key pair exists in the AWS account

```bash
aws ec2 describe-key-pairs --key-names devops-kp
```

```bash
{
    "KeyPairs": [
        {
            "KeyPairId": "key-04a851adfb85a1178",
            "KeyType": "rsa",
            "Tags": [],
            "CreateTime": "2025-11-25T08:49:17.648Z",
            "KeyName": "devops-kp",
            "KeyFingerprint": "21:80:d2:54:8f:3e:ee:2a:a7:85:49:55:02:99:41:de:1b:37:a4:ed"
        }
    ]
}
```
