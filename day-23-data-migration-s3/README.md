## Data Migration Between S3 Buckets Using AWS CLI

### Step 1: Create the New Private Bucket

```bash
aws s3api create-bucket \
  --bucket xfusion-sync-6818 \
  --region us-east-1
```

O/P:

```bash
{
    "Location": "/xfusion-sync-6818"
}
```

Ensure bucket is private (remove public access):

```bash
aws s3api put-public-access-block \
  --bucket xfusion-sync-6818 \
  --public-access-block-configuration \
  BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

### Step 2: Migrate Data

```bash
aws s3 sync s3://xfusion-s3-10429 s3://xfusion-sync-6818
```

✔ Copies all objects
✔ Preserves folder structure
✔ Efficient for large datasets

### Step 3: Verify Data Consistency

1. Compare object count:

```bash
aws s3 ls s3://xfusion-s3-10429 --recursive | wc -l [Source Bucket]
aws s3 ls s3://xfusion-sync-6818 --recursive | wc -l [Destination Bucket]
```

2. Compare total size:

```bash
aws s3 ls s3://xfusion-s3-10429 --recursive --summarize | grep "Total Size" [Source Bucket]
aws s3 ls s3://xfusion-sync-6818 --recursive --summarize | grep "Total Size" [Destination Bucket]
```
