# week-6-assigmnet


# 📘 ETL Project using Azure Data Factory – Week 6

This project demonstrates how to build an **end-to-end ETL workflow using Azure Data Factory (ADF)** that connects to:

- A **local SQL Server (on-premises)**
- An **external SFTP server**

The extracted data is loaded into:

- **Azure SQL Database**
- **Azure Data Lake Storage (ADLS Gen2)**

The solution includes **incremental loading**, and a **custom trigger** scheduled to run automatically on the **last Saturday of every month**.

## 📂 Data Sources

We used the **NYC Restaurant Inspection** dataset, which is updated monthly — making it ideal for testing incremental load logic and periodic automation.

## 🧱 Architecture Overview

<p align="center">
  <img src="./images/diagram.png" alt="Architecture Diagram" />
</p>

## 🔄 ETL Pipelines Built

### 🟢 Pipeline 1: From On-Prem SQL Server to Azure SQL DB
- Transfers records from a local SQL table to an Azure-hosted SQL Database.

### 🔵 Pipeline 2: From SFTP Server to ADLS
- Fetches CSV files from an SFTP location and stores them in ADLS Gen2.

### 🟣 Master Pipeline
- Chains both pipelines and includes a trigger to automate execution on the **last Saturday of every month**.

## 🛠️ Source Configurations

### Local SQL Server:
- Setup through **SQL Server Management Studio (SSMS)**
- Hosted the NYC restaurant data locally.

### SFTP:
- Configured access using credentials via tools like **WinSCP**
- Contained multiple CSVs partitioned by year.

## 🎯 Targets

### Azure SQL Database:
- Hosted in Azure within the resource group `week6`
- Used to store cleaned restaurant inspection records.

### Azure Data Lake (ADLS Gen2):
- Organized into folders:
  - `SFTP-DATA`
  - `Restaurant_Inspection`

## 🔧 Pipeline 1 Details: Local SQL Server ➝ Azure SQL DB

### ✅ Linked Services
- **Local SQL Server** via SHIR
- **Azure SQL Database**

### 📦 Datasets
- Source: `sqlservertable1`
- Destination: `azuresqltable1`

### 🔁 Incremental Logic with Watermark

1. **Lookup latest inspection date** from watermark table
2. **Filter** new rows using this date
3. **Update watermark** post successful load

```sql
SELECT MAX([INSPECTION DATE]) FROM dbo.NYC_Restaurant
```

## 🌐 Pipeline 2: SFTP ➝ Azure Data Lake

### ✅ Linked Services
- **SFTP server**
- **Azure Data Lake**

### 📦 Datasets
- Source: CSV files from SFTP
- Destination: ADLS folders

💡 *This can be extended using watermark logic for incremental SFTP processing.*

## 🔁 Master Pipeline & Trigger

Combines both pipelines and adds a **recurring trigger**:
- Runs at **7:00 AM** on the **last Saturday of every month**

### 📅 Trigger Setup:
- Type: Monthly
- Day: Last Saturday
- Time: 07:00 AM

## ✅ Project Status

| Component                    | Status     |
|-----------------------------|------------|
| SHIR setup for on-prem data | ✅ Complete |
| SFTP to ADLS pipeline        | ✅ Complete |
| Incremental loading logic    | ✅ Complete |
| Trigger configuration        | ✅ Complete |

This project helped me implement a real-world ETL architecture using Azure services. It covers **multiple data sources**, **automated scheduling**, and **optimized incremental loading** — essential for any modern data pipeline.
