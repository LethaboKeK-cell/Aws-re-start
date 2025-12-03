


#  Amazon Aurora Lab: EC2 Connectivity & SQL Interaction

This guide walks through the steps to:
1. Create an Amazon Aurora DB cluster
2. Connect to a pre-created EC2 instance
3. Configure EC2 to connect to Aurora
4. Query the Aurora database

---

##  1. Create an Amazon Aurora DB Cluster

### Step 1: Launch Aurora via RDS Console
- Go to **Amazon RDS Console** → **Databases** → **Create database**
- Select:
  - **Standard create**
  - **Engine type**: Aurora (MySQL-compatible)
  - **Edition**: Aurora MySQL

### Step 2: Configure Cluster
- **DB cluster identifier**: `aurora`
- **Instance class**: `db.t3.medium`
- **Multi-AZ deployment**: Enabled (default for Aurora)
- **Storage type**: General Purpose SSD (gp2)

### Step 3: Connectivity
- **VPC**: Select your custom VPC
- **DB Subnet Group**: Must span at least 2 Availability Zones
- **Public access**: `No`
- **Security group**: Choose or create one that allows inbound MySQL (port 3306)

### Step 4: Credentials
- **Master username**: `main`
- **Master password**: `your-secure-password`

### Step 5: Monitoring & Encryption
- Enable **Performance Insights**
- Enable **Encryption** with default AWS KMS key

 Wait for status to change from `Configuring` to `Available` before proceeding

---

##  2. Connect to a Pre-Created EC2 Instance

### Step 1: SSH into EC2
```bash
ssh -i your-key.pem ec2-user@your-ec2-public-ip
```

### Step 2: Install MariaDB/MySQL Client
```bash
sudo yum install mariadb -y
```

 Result: CLI tool installed for querying Aurora

---

##  3. Configure EC2 to Connect to Aurora

### Step 1: Security Group Rules
- Ensure EC2 instance’s security group allows **outbound traffic**
- Ensure Aurora DB’s security group allows **inbound MySQL (TCP 3306)** from EC2’s security group

### Step 2: Retrieve Aurora Endpoint
- Go to **RDS Console** → **Databases** → Select Aurora cluster
- Copy **Writer endpoint** (e.g., `aurora-instance-1.cluster-xxxxxxxxxx.us-west-2.rds.amazonaws.com`)

---

##  4. Query the Aurora Database

### Step 1: Connect via CLI
```bash
mysql -h aurora-endpoint -u main -p
```

### Step 2: Create & Populate Table
```sql
CREATE DATABASE world;
USE world;

CREATE TABLE country (
  Code CHAR(3) PRIMARY KEY,
  Name CHAR(52),
  Continent ENUM('Asia','Europe','North America','Africa','Oceania','Antarctica','South America'),
  Region CHAR(26),
  SurfaceArea FLOAT(10,2),
  IndepYear SMALLINT,
  Population INT,
  LifeExpectancy FLOAT(3,1),
  GNP FLOAT(10,2),
  GNPOld FLOAT(10,2),
  LocalName CHAR(45),
  GovernmentForm CHAR(45),
  HeadOfState CHAR(60),
  Capital INT,
  Code2 CHAR(2)
);
```

### Step 3: Insert Sample Data
```sql
INSERT INTO country VALUES ('AUS','Australia','Oceania','Australia and New Zealand',7741220.00,1901,18886000,79.8,39201.00,40300.00,'Australia','Constitutional Monarchy, Federation','',135,'AU');
INSERT INTO country VALUES ('THA','Thailand','Asia','Southeast Asia',513115.00,1350,61399000,68.2,116416.00,130000.00,'Prathet Thai','Constitutional Monarchy','',3320,'TH');
```

### Step 4: Run Queries
```sql
SELECT * FROM country WHERE GNP > 35000 AND Population > 1000000;
```

 Result: Aurora responds with matching rows

---

##  Final Checklist

- [x] Aurora DB cluster created and available
- [x] EC2 instance connected and configured
- [x] Security groups allow DB access
- [x] SQL queries executed successfully

---

##  Tags

`AWS`, `Aurora`, `RDS`, `EC2`, `MySQL`, `MariaDB`, `Cloud Lab`, `Database Connectivity`, `Multi-AZ`, `SQL`

