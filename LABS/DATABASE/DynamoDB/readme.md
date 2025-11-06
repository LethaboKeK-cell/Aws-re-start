# 🎵 Amazon DynamoDB Lab: Table Creation, Data Entry & Querying

This guide walks through the steps to:
1. Create a **DynamoDB table** with partition and sort keys
2. Insert structured **music metadata** into the table
3. Query the table using **key conditions**

---

## 🚀 1. Create an Amazon DynamoDB Table

### Step 1: Navigate to DynamoDB Console
- Go to **AWS Console** → **DynamoDB** → **Tables** → **Create Table**

### Step 2: Define Table Schema
- **Table name**: `Music`
- **Partition key**: `Artist` (Type: String)
- **Sort key**: `Song` (Type: String)
- Settings: Select **Default settings** (On-demand capacity mode)

✅ Result: Table `Music` created with composite key schema

---

## ✍️ 2. Enter Data into the DynamoDB Table

### Step 1: Add Items via Console
- Go to **Tables** → Select `Music` → **Explore items** → **Create item**
- Use **Form view** to enter attributes

### Sample Entries

#### Entry 1
- **Artist**: Pink Floyd  
- **Song**: Money  
- **Album**: The Dark Side of the Moon  
- **Year**: 1973  
- **LengthSeconds**: 382  
- **Genre**: Progressive Rock

#### Entry 2
- **Artist**: John Lennon  
- **Song**: Imagine  
- **Album**: Imagine  
- **Year**: 1971  
- **LengthSeconds**: 183  
- **Genre**: Soft Rock

#### Entry 3
- **Artist**: Psy  
- **Song**: Gangnam Style  
- **Album**: Psy 6 (Six Rules), Part 1  
- **Year**: 2012  
- **LengthSeconds**: 219  
- **Genre**: K-pop

✅ Result: Items successfully saved and visible in table scan

---

## 🔍 3. Query the DynamoDB Table

### Step 1: Use Query Interface
- Go to **Tables** → Select `Music` → **Explore items** → **Query**
- Set conditions:
  - **Partition key**: `Artist` = `Psy`
  - **Sort key**: `Song` = `Gangnam Style`
  - **Projection**: All attributes

### Step 2: Run Query
- Click **Run**
- Result: One item returned with full metadata

### Sample Output
| Artist      | Song           | Album                          | Year | LengthSeconds |
|-------------|----------------|--------------------------------|------|----------------|
| Psy         | Gangnam Style  | Psy 6 (Six Rules), Part 1      | 2012 | 219            |

✅ Result: Query executed with 100% efficiency and minimal RCUs

---

## 🧪 Verification Checklist

- [x] Table `Music` created with composite key
- [x] Items inserted with rich metadata
- [x] Query returned expected results
- [x] Data editable via Form or JSON view

---

## 📁 Tags

`AWS`, `DynamoDB`, `NoSQL`, `Music Metadata`, `Partition Key`, `Sort Key`, `Cloud Lab`, `Query`, `Data Modeling`
