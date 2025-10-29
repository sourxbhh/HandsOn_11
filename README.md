# Hands-on L11: AWS Core Services (S3, Glue, CloudWatch, Athena)

This repository contains the SQL scripts and query results for the **Hands-on L11 assignment**, which involves leveraging **AWS Core Services** — S3, Glue, CloudWatch, and Athena — to build a **serverless data catalog** and perform analytical queries on a large CSV dataset.

---

## Dataset

The dataset used for this hands-on is the **Amazon Sale Report**.  

This dataset contains various **e-commerce sales records**, including:
- Order details  
- Transaction dates  
- Sales amounts  
- Product information  
- Shipping locations  

---

## Objective

To set up a **serverless data infrastructure** on AWS and use it to execute complex SQL queries for **business intelligence** insights.

---

## Approach & Workflow

### **1. Configure Amazon S3 Buckets**
Two S3 buckets were created to separate raw and processed data:

- **Raw Data Bucket:**  
  `s3://handson11cloudbucket/raw/` — stores the original `Amazon Sale Report.csv` file.  
- **Processed Data Bucket:**  
  `s3://handson11cloudbucket/processed/` — used by AWS Athena to store temporary data and query results.

The CSV file was uploaded to the **raw data** bucket.

---

### **2. Create an IAM Role for Glue Crawler**

A new IAM role was created:  
**`GlueCrawlerRole-handsonglueroles`**

#### Role Configuration:
- **Trust Relationship:**  
  Allows the `glue.amazonaws.com` service to assume the role.
- **Permissions:**  
  - `AmazonS3FullAccess` or a custom policy for read/write access to both S3 buckets.  
  - `AWSGlueServiceRole` and `AWSGlueConsoleFullAccess` for Glue operations, including CloudWatch Logs.

---

### **3. Create and Run a Glue Crawler**

A Glue crawler named **`ecom_sales_crawler`** was created and configured as follows:

- **Data Source:** S3 → `s3://handson11cloudbucket/raw/`  
- **IAM Role:** `GlueCrawlerRole-handsonglueroles`  
- **Output Database:** `ecom_sales_db`

After execution, the crawler:
- Identified the CSV file  
- Inferred the schema (column names and data types)  
- Created a table named **`amazon_sale_report`** in the `ecom_sales_db` database.

---

### **4. Monitor Crawler Status with CloudWatch**

The execution logs were monitored via **AWS CloudWatch**, confirming that:
- The crawler **started** successfully  
- It **completed** without errors  
- It **added one table** to the Glue database  

---

### **5. Query the Database using Athena**

Queries were executed using **AWS Athena Query Editor**, targeting:
- **Database:** `ecom_sales_db`
- **Table:** `amazon_sale_report`

Athena was configured to output query results to the **processed S3 bucket**.

---

## SQL Query Adaptations

The provided dataset lacked some fields (e.g., *Profit* and *Discount*) used in the original queries. Therefore, modifications were made:

- Replaced **Profit/Discount** references with available columns (e.g., **Amount** for sales/revenue).  
- Substituted **Sub-Category** references with **Category**.  
- Used `DATE_PARSE("Date", '%m-%d-%y')` for explicit date conversion to comply with Athena’s date parsing requirements.  
- The table name was assumed to be **`amazon_sale_report`**, consistent with Glue’s default naming convention for the file.

---

## Tools and Services Used

- **Amazon S3** — Data storage for raw and processed datasets  
- **AWS Glue** — Schema inference and data catalog creation  
- **AWS CloudWatch** — Crawler monitoring and logging  
- **Amazon Athena** — Serverless SQL query execution  

---

## Outcome

A fully **serverless ETL and analytics pipeline** was successfully implemented using AWS Core Services.  
The pipeline:
- Ingests and catalogs raw CSV data  
- Enables real-time SQL-based analytics  
- Demonstrates seamless integration across S3, Glue, CloudWatch, and Athena  

---

## Screenshots

### CloudWatch Logs

<img width="1919" height="808" alt="1ww" src="https://github.com/user-attachments/assets/82fc7a62-2b59-4d45-bc0e-fb68ce49abd6" />


### IAM Role Configuration

<img width="1920" height="804" alt="2ww" src="https://github.com/user-attachments/assets/4a225d4a-fe8d-47a9-af7b-b7270f3b33bf" />


### S3 Buckets Configuration

<img width="1920" height="797" alt="3ww" src="https://github.com/user-attachments/assets/d386b445-e80f-4ad5-933f-eacf65d604d8" />
