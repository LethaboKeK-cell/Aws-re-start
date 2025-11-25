

#  AWS EBS Volume Lifecycle Demo

Welcome! This repository documents a hands-on walkthrough of managing Amazon Elastic Block Store (EBS) volumes on EC2. Whether you're learning AWS or building repeatable infrastructure, this guide walks you through:

- Creating an EBS volume
- Attaching and mounting it to an EC2 instance
- Creating a snapshot
- Restoring a volume from a snapshot

Each step includes terminal commands and AWS Console actions, making it beginner-friendly and reproducible.

---

##  Prerequisites

Before you begin, ensure you have:

- An active AWS account
- A running EC2 instance (Amazon Linux preferred)
- IAM permissions to manage EC2 and EBS resources
- Basic familiarity with the AWS Console and Linux terminal

---

##  Create an EBS Volume

In the AWS Console:

- Navigate to **EC2 > Elastic Block Store > Volumes**
- Click **Create Volume**
- Choose:
  - **Volume type**: General Purpose SSD (gp3)
  - **Size**: 1 GiB
  - **IOPS**: 3000
  - **Throughput**: 125 MiB/s
  - **Availability Zone**: Match your EC2 instance (e.g., `us-west-2a`)
- (Optional) Add a tag like `Name = My Volume`
- Click **Create Volume**

 *Tip: gp3 volumes allow you to customize performance independently of size.*

---

##  Attach and Mount the Volume to EC2

In the AWS Console:

- Go to **Volumes**, select your volume
- Click **Actions > Attach Volume**
- Choose your EC2 instance
- Set **Device name** (e.g., `/dev/sdc`)
- Click **Attach Volume**

In your EC2 terminal:

```bash
# Create a mount point
sudo mkdir /mnt/data-store

# Format the volume (ext3 used here)
sudo mkfs -t ext3 /dev/sdc

# Mount the volume
sudo mount /dev/sdc /mnt/data-store

# Write and verify a file
sudo sh -c "echo some text has been written > /mnt/data-store/file.txt"
cat /mnt/data-store/file.txt
```

 You should see: `some text has been written`

---

##  Create a Snapshot of the Volume

In the AWS Console:

- Go to **Volumes**, select your volume
- Click **Actions > Create Snapshot**
- Add a description (e.g., `My Snapshot`)
- (Optional) Tag it: `Name = My Snapshot`
- Click **Create Snapshot**

 *Snapshots are stored in S3 and can be used for backup or replication.*

---

##  Create a New Volume from Snapshot

In the AWS Console:

- Go to **Snapshots**, select your snapshot
- Click **Actions > Create Volume**
- Choose:
  - **Volume type**: gp3
  - **Size**: 1 GiB
  - **IOPS**: 3000
  - **Throughput**: 125 MiB/s
  - **Availability Zone**: Match your EC2 instance
- Tag it: `Name = Restored Volume`
- Click **Create Volume**

Then:

- Attach the restored volume to your EC2 instance (e.g., `/dev/sdc`)
- Mount it to a new directory:

```bash
sudo mkdir /mnt/data-store2
sudo mount /dev/sdc /mnt/data-store2
ls /mnt/data-store2/file.txt
```

 You should see the original file restored!

---

## 📚 Summary

This repo demonstrates the full lifecycle of an EBS volume:

| Step                     | AWS Console | Terminal |
|--------------------------|-------------|----------|
| Create volume            | ✅           | ❌        |
| Attach volume            | ✅           | ❌        |
| Format & mount volume    | ❌           | ✅        |
| Create snapshot          | ✅           | ❌        |
| Restore from snapshot    | ✅           | ✅        |
