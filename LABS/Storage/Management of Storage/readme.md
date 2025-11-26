
# ☁️ EC2 Snapshot & S3 Sync Workflow Guide

Welcome! This guide walks you through how to:

1. Create and maintain snapshots for Amazon EC2 instances  
2. Use Amazon S3 sync to copy files from an EBS volume to an S3 bucket  
3. Use Amazon S3 versioning to recover deleted files  

Whether you're backing up critical data or teaching others how to manage AWS resources, this workflow is reproducible, beginner-friendly, and CLI-driven.

---

##  Step 1: Create and Maintain EC2 Snapshots

###  Stop the EC2 Instance
```bash
aws ec2 stop-instances --instance-ids i-xxxxxxxxxxxxxxxxx
```

###  Wait for the instance to stop
```bash
aws ec2 wait instance-stopped --instance-ids i-xxxxxxxxxxxxxxxxx
```

### Create a snapshot of the attached EBS volume
```bash
aws ec2 create-snapshot --volume-id vol-xxxxxxxxxxxxxxxxx --description "Backup snapshot"
```

###  (Optional) Wait for snapshot to complete
```bash
aws ec2 wait snapshot-completed --snapshot-id snap-xxxxxxxxxxxxxxxxx
```

###  Start the EC2 instance again
```bash
aws ec2 start-instances --instance-ids i-xxxxxxxxxxxxxxxxx
```

---

##  Step 2: Sync EBS Volume Files to Amazon S3

###  Sync your folder to S3
```bash
aws s3 sync /home/ec2-user/files s3://your-bucket-name
```

###  Enable deletion sync (removes files from S3 if deleted locally)
```bash
aws s3 sync /home/ec2-user/files s3://your-bucket-name --delete
```

> Tip: This is safe when versioning is enabled — deleted files are preserved as older versions.

---

##  Step 3: Enable Versioning on Your S3 Bucket

###  Activate versioning
```bash
aws s3api put-bucket-versioning \
  --bucket your-bucket-name \
  --versioning-configuration Status=Enabled
```

###  Confirm versioning is active
```bash
aws s3api get-bucket-versioning --bucket your-bucket-name
```

---

##  Step 4: Recover Deleted Files Using Versioning

###  List all versions of a file
```bash
aws s3api list-object-versions \
  --bucket your-bucket-name \
  --prefix files/file2.txt
```

###  Download a previous version
```bash
aws s3api get-object \
  --bucket your-bucket-name \
  --key files/file2.txt \
  --version-id <VERSION_ID> \
  restored-file2.txt
```

###  (Optional) Re-upload to S3
```bash
aws s3 cp restored-file2.txt s3://your-bucket-name/files/file2.txt
```

---

##  Final Notes

- Always verify snapshot creation and S3 sync with `describe` and `list` commands.
- Use IAM roles to securely grant EC2 access to S3.
