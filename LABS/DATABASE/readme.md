#  Amazon RDS Lab: High Availability, Connectivity & Web Integration

This guide walks through the steps to:
1. Launch a **highly available Amazon RDS MySQL instance**
2. Configure **security groups** to permit access from a web server
3. Connect and interact with the database via a **web application**

---

##  1. Launch a Highly Available Amazon RDS DB Instance

### Step 1: Choose Creation Method
- Go to **Amazon RDS Console**
- Select **Standard Create** for full control over configuration

### Step 2: Select Engine
- Choose **MySQL** as the database engine

### Step 3: Deployment Template
- Select **Production** for high availability and durability

### Step 4: Settings
- **DB instance identifier**: `lab-db`
- **Master username**: `main`
- **Master password**: Choose a strong password manually

### Step 5: Instance Configuration
- **Instance class**: `db.t3.medium` (2 vCPUs, 4 GiB RAM)
- **Storage type**: General Purpose SSD (gp2)
- **Allocated storage**: `200 GiB`

### Step 6: Connectivity
- **VPC**: Select your custom VPC (e.g., `Lab VPC`)
- **DB Subnet Group**: Use a group with **2 private subnets** in different AZs
- **Public access**: `No`
- **VPC security group**: Choose existing or create new

### Step 7: Monitoring
- Enable **Performance Insights**
  - Retention: `7 days`
  - KMS Key: `alias/aws/rds`

### Step 8: Additional Configuration
- **Initial database name**: `lab`
- **DB parameter group**: `default.mysql8.0`
- **Option group**: `default:mysql-8-0`
- **Enable encryption**: ✅
- **Enable automated backups**: Optional
- **Enable Enhanced Monitoring**: Optional
- **Log exports**: Enable `Error`, `General`, `Slow query` logs

✅ Result: RDS instance `lab-db` launched with high availability across multiple AZs

---

##  2. Configure DB Access from Web Server

### Step 1: Create Security Groups

#### Web Security Group
- Name: `Web Security Group`
- Description: Allow HTTP/HTTPS traffic
- Inbound Rules:
  - Type: HTTP (Port 80) → Source: `0.0.0.0/0`
  - Type: HTTPS (Port 443) → Source: `0.0.0.0/0`

#### DB Security Group
- Name: `DB Security Group`
- Description: Permit access from Web Security Group
- VPC: `Lab VPC`
- Inbound Rule:
  - Type: MySQL/Aurora
  - Protocol: TCP
  - Port: `3306`
  - Source: `Web Security Group` (sg-xxxxxxxx)

✅ Result: Web server can securely connect to RDS instance on port 3306

---

## 🌐 3. Connect Web Application to RDS

### Step 1: Retrieve RDS Endpoint
- Example: `lab-db.cmzreufuyjpp.us-west-2.rds.amazonaws.com`

### Step 2: Configure Web App
- Use the following credentials in your app’s DB config:
  - **Endpoint**: `lab-db.cmzreufuyjpp.us-west-2.rds.amazonaws.com`
  - **Database**: `lab`
  - **Username**: `main`
  - **Password**: `your-password`

### Step 3: Launch Web App
- Host your app on EC2 or Elastic Beanstalk
- Ensure it uses the correct security group (`Web Security Group`)
- Connect to RDS using a MySQL client or backend framework

### Step 4: Interact with DB
- Example: Address Book Web App
  - Add, edit, and remove contacts
  - Backend connects to RDS using credentials above

✅ Result: Web app successfully interacts with RDS database

---

##  Verification Checklist

- [x] RDS instance launched with Multi-AZ
- [x] Security groups configured for DB access
- [x] Web app connected and interacting with DB
- [x] Monitoring and encryption enabled

---

##  Tags

`AWS`, `RDS`, `MySQL`, `High Availability`, `Web App`, `Security Groups`, `Cloud Lab`, `Multi-AZ`, `Database Connectivity`
