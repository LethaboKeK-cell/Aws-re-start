

## 📦 1. Launch an EC2 Instance via AWS Management Console

### 🔧 Step-by-Step Configuration

1. **Start the Launch Process**
   - Go to **EC2 Dashboard** → Click **Launch Instance**
   - Name your instance: `Bastion host`

2. **Choose an Amazon Machine Image (AMI)**
   - Select: `Amazon Linux 2023 kernel-6.1 AMI` (Free tier eligible)

3. **Choose Instance Type**
   - Select: `t3.micro` (2 vCPUs, 1 GiB RAM, Free tier eligible)

4. **Key Pair (Login)**
   - ⚠️ Recommended: Create or choose an existing key pair
   - For lab/demo: You may proceed without a key pair (not secure for production)

5. **Network Settings**
   - VPC: `Lab VPC`
   - Subnet: `Lab Subnet` (Availability Zone: `us-east-1a`)
   - Auto-assign Public IP: `Enabled`
   - Security Group:
     - Name: `bastion security group`
     - Description: `Permit SSH connections`
     - Inbound Rule: `TCP | Port 22 | Source: 0.0.0.0/0` (⚠️ Open to all IPs)

6. **Storage Configuration**
   - Root volume: `8 GiB`, `gp3`, `3000 IOPS`, not encrypted

7. **Advanced Details**
   - IAM Role: `Bastion-Role`
   - Hostname Type: `IP name`
   - DNS: Enable both IPv4 A record options
   - Shutdown behavior: `Stop`
   - Termination protection: Optional

8. **Launch**
   - Review and click **Launch Instance**

---

## 🔌 2. Connect via EC2 Instance Connect

Once the instance is running:

1. Go to **EC2 Dashboard** → **Instances**
2. Select your instance
3. Click **Connect**
4. Choose **EC2 Instance Connect (browser-based SSH)**
5. Click **Connect**

✅ You’ll be logged into the instance as `ec2-user`.

---

## 🖥️ 3. Launch an EC2 Instance via AWS CLI

### 📁 Prerequisites

- AWS CLI installed and configured (`aws configure`)
- `UserData.txt` script downloaded from S3:

```bash
curl -o UserData.txt https://aws-c2-labprojects.s3.us-west-2.amazonaws.com/CS-1307/2023/Lab-1/UserData.txt
```

### 🧠 Retrieve Required Parameters

```bash
# Get latest Amazon Linux AMI ID
aws ssm get-parameters \
  --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 \
  --query 'Parameters[0].Value' \
  --output text

# Get Subnet ID
aws ec2 describe-subnets \
  --filters "Name=tag:Name,Values=Public Subnet" \
  --query 'Subnets[].SubnetId' \
  --output text

# Get Security Group ID
aws ec2 describe-security-groups \
  --filters "Name=tag:Name,Values=SecurityGroup" \
  --query 'SecurityGroups[].GroupId' \
  --output text
```

### 🚀 Launch the Instance

```bash
INSTANCE=$( \
aws ec2 run-instances \
  --image-id $AMI \
  --subnet-id $SUBNET \
  --security-group-ids $SG \
  --user-data file:///home/ec2-user/UserData.txt \
  --instance-type t3.micro \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Web Server}]' \
  --query 'Instances[0].InstanceId' \
  --output text \
)

echo "Launched Instance ID: $INSTANCE"
```

### 🔍 Verify Instance Status & DNS

```bash
# Check if instance is running
aws ec2 describe-instances \
  --instance-ids $INSTANCE \
  --query 'Reservations[].Instances[].State.Name' \
  --output text

# Get public DNS
aws ec2 describe-instances \
  --instance-ids $INSTANCE \
  --query 'Reservations[].Instances[].PublicDnsName' \
  --output text
```

---

## 🧠 Notes

- Security group rule `0.0.0.0/0` allows access from any IP. For production, restrict to known IP ranges.
- Free tier includes up to 750 hours/month of t2.micro or t3.micro usage and 30 GB of EBS storage.
- Always tag your resources for easier tracking and billing.
