# Zero-Downtime-Cross-Region-Database-Migration-using-AWS-DMS

## 📌 Overview

This project demonstrates how to perform a **zero-downtime database migration** across AWS regions using:

- AWS Database Migration Service (DMS)
- Amazon RDS (MySQL)
- AWS CloudFormation

The system uses **Change Data Capture (CDC)** to continuously replicate database changes, ensuring **minimal downtime and real-time synchronization**.

---

## 🎯 Key Features

- ✅ Zero/near-zero downtime migration  
- 🔄 Real-time data replication using CDC  
- 🌍 Cross-region migration (e.g., Mumbai → Singapore)  
- ⚡ Automated infrastructure setup  
- 🔍 Data validation and consistency  

---

## 🏗️ Architecture

<img width="1536" height="1024" alt="ChatGPT Image Apr 21, 2026, 07_26_03 AM" src="https://github.com/user-attachments/assets/e5e4483d-d814-4773-b52f-d891cf3a2652" />


<img width="1550" height="765" alt="image" src="https://github.com/user-attachments/assets/f6738c1f-092c-4647-a4c9-17ab5090e172" />




---

## 🔁 Workflow

1. Full data load from source to target  
2. CDC captures real-time changes  
3. DMS applies changes continuously  
4. Target DB stays in sync  
5. Cutover to target database  

---

## 🧰 Tech Stack

- AWS DMS  
- Amazon RDS (MySQL)  
- AWS CloudFormation  
- MySQL Workbench  

---

# ⚙️ Step-by-Step Implementation

---

## 🟦 Step 1: Create Source Database (RDS - Region A)

1. Go to AWS Console → RDS  
2. Click **Create Database**  
3. Select:
   - Engine: MySQL  
   - Instance: `db.t3.micro`  
4. Set username & password  
5. Enable public access (for testing)

or (Use cloud formation code)

1. download the source-db.yaml from cloudformaton/ folder
2. open cloudformation in aws cli
3. click on create stack
4. select - choose an existing template
5. select - upload a template file
6. create stack

   <img width="1919" height="966" alt="Screenshot 2026-04-20 174353" src="https://github.com/user-attachments/assets/4256d545-ac4c-4ad5-b349-0ba00a8069c8" />


---

## 🟦 Step 2: Create Target Database (RDS - Region B)

1. Switch AWS region  
2. Repeat same steps as source  

or (Use cloud formation code)

1. use target-db.yaml file from cloudformation/ folder

---

## 🟦 Step 3: Enable CDC (IMPORTANT)

### Create Parameter Group

1. Go to RDS → Parameter Groups  
2. Create new parameter group  
3. Set:

```sql
binlog_format = ROW
binlog_row_image = FULL
```

---


- Attach to source DB
- Reboot database

## Step 4: Create DMS Replication Instance
Go to DMS → Replication Instances
Click Create
Select:
Instance: dms.t3.medium

---

## Step 5: Create Endpoints

Source Endpoint

Enter source DB details (endpoint of source RDS)


<img width="1918" height="1009" alt="Screenshot 2026-04-20 180528" src="https://github.com/user-attachments/assets/c1aa84a0-d30e-45a5-a83b-1b0303b2887d" />

Test connection

<img width="1910" height="915" alt="Screenshot 2026-04-20 181107" src="https://github.com/user-attachments/assets/936006d2-0803-4c68-a30c-f2b67f041291" />

Target Endpoint

Enter target DB details (endpoint of destination RDS)

<img width="1919" height="1061" alt="Screenshot 2026-04-20 181116" src="https://github.com/user-attachments/assets/0eee28b3-5023-4dff-892d-fe4a72c020ee" />

Test connection 

<img width="1919" height="1016" alt="Screenshot 2026-04-20 181853" src="https://github.com/user-attachments/assets/729f1465-9a6b-40c9-a2ad-dc7a22e92447" />

---

## Step 6: Create Migration Task

Go to DMS → Tasks → Create Task
Configure:
Migration Type:
Full Load + CDC (change data capture)
Table Mapping:
Schema: demo_db
Table: users

---

## Step 7: Start Migration

Start the task
Monitor:
Full load
CDC status

<img width="1919" height="999" alt="Screenshot 2026-04-20 190715" src="https://github.com/user-attachments/assets/67ea2724-1f51-43f5-93f2-47991fa9b8f7" />


---

## Step 8: Validate Data

- connect source-db RDS database to mysql workbeanch
- username admin password - Password123!
- endpoint is RDS database endpoint

do the same for target RDS database

Source:
INSERT INTO users VALUES (1, 'test_user');

Source database:
<img width="1918" height="1038" alt="Screenshot 2026-04-20 190801" src="https://github.com/user-attachments/assets/8429cfdb-aacc-4ba8-8726-eb9551df4ce2" />

---

## Step 9: Test CDC

UPDATE users SET name = 'updated' WHERE id = 1;

👉 Changes should reflect in target DB instantly
Target database :
<img width="1919" height="1066" alt="Screenshot 2026-04-20 190910" src="https://github.com/user-attachments/assets/c285a260-beca-421d-bb70-5f3ccee27d39" />

---

## Step 10: Cutover

Stop writes to source
Ensure replication lag = 0
Switch application to target DB


---


## Key Concepts
🔹 What is CDC?

CDC captures changes (INSERT, UPDATE, DELETE) from database logs and replicates them in real time.

🔹 What is Replication Instance?

It is the compute engine that:

- Reads source DB
- Processes data
- Writes to target DB

🔹 What is DMS Task?

Defines:

- What to migrate
- How to migrate
- When to migrate
- Common Issues & Fixes

---
## possible errors
  
 Error 1236 (Binlog Not Found)
 
Cause: Old logs deleted

Fix:

Recreate task
Start from beginning

CDC Not Working
Fix:
binlog_format = ROW
binlog_row_image = FULL

 Data Not Syncing
Check task status
Ensure CDC enabled

---

## Cost Estimation
Service	Cost
RDS (2 instances)	~$1.6
DMS	~$2.4
Data Transfer	~$0.2
Total	~$4–5

---

## Results
- ✅ Zero downtime migration achieved
- 🔄 Real-time replication working
- 📈 Data consistency maintained
- 🏁 Conclusion

This project demonstrates how AWS DMS with CDC enables seamless and reliable database migration with minimal downtime.

---

##  References

1.AWS DMS Documentation
2.AWS RDS Documentation
3.MySQL Binlog Documentation


---

## Author

Darshan B

