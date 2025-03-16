# Design & Build SQL Data Warehouse
Building a modern data warehouse with SQL Server, including ETL processes, data modelling and analytics.
---
### Theory:

### Process:
#### Extraction:
Extraction Method: Pull
Extract Type: Full
Extract technique: File parsing

#### Transform:
##### Transformation:
- Data Enrichment
- Data Integration
- Derived Columns
- Data Normalisation & Standardisation
- Business Rules & Logic
- Data Aggregation

##### Data Cleansing:
- Remove Duplicates
- Data Filtering
- Handling Missing Data
- Handling Invalid Values
- Handling Unwanted Spaces
- Outlier Detection
- Data Type Casting

#### Load:
Processing Type: Batch
Load Method: Full Load (Truncate & Import)
Slowly Changing Dimensions: Overwrite

### Project Steps
#### Requirements Analysis
##### Analyse & Understand the Requirements
insert requirements

#### Design Data Architecture
##### Choose Data Management Approach
Data Architecture Options:
- Data Warehouse (Y)
- DataLake
- Data Lakehouse
- Data Mesh

Design Approaches:
- Imson
- Kimball
- Data Vault
- Medallion Architecture (Y)

#### Design the Layers
Each layer has its own purpose and tasks, good data architecture practice means no mixing tasks within layers, for example: no business transformations in the silver layer & no cleansing in the gold layer.

##### Bronze Layer
Definition: Raw, unprocessed data as-is from sources
Objective: Traceability & Debugging
Object Type: Tables
Load Method: Full load (Truncate & Insert)
Data Transformation: None
Data Modelling: None (as-is)
Target Audience: Data engineer

##### Silver Layer
Definition: Clean & Standardised data
Objective: (Intermediate layer) Prepare data for analysis
Object Type: Tables
Load Method: Full load
Data Transformation: Data Cleansing, Standardisation, normalisation, derive columns, enrichment
Data Modelling: None (as-is)
Target Audience: Data engineer & analysts

##### Gold Layer
Definition: Business Ready Data
Objective: Provide data to be consumed for reporting & analytics
Object Type: Views
Load Method: None
Data Transformation: Data integration, aggregation, apply business rules & logic
Data Modelling: Star Schema, Aggregated objects, Flat tables
Target Audience: Data analysts & business users

![Data Architecture](https://github.com/user-attachments/assets/d47a6bc4-740b-4e61-aa79-cdc9a36ebea1)

#### Project Initialisation
##### Create Detailed Project Tasks in Notion
Link

##### Define Naming Conventions in Project
Case: snake_case
Language: English

###### Bronze Rules
<sourcesystem>_<entity> eg. crm_customer_info

###### Silver Rules
<sourcesystem>_<entity> eg. crm_customer_info

###### Gold Rules
<category>_<entity> eg. dim_customer, fact_sales, agg_customers, agg_sales_monthly

###### Column Naming Conventions
####### Surrugate Keys
<table_name>_key eg. customer_key is surrogate key in the customer table

####### Technical Columns
dwh_<column_name> eg. dwh_load_date

####### Stored Procedure
load_<layer> eg. load_bronze

##### Create GIT Repo & Prepare the Structure
Created database within SQL Server Management Studio, and then created the bronze, silver and gold schemas within the database. As can be seen in the script linked below:

![Crete database and schemas script]()

#### Building Bronze Layer
Analysiing Source Systems:
Interviwing source system experts:
Ask questions such as
- Who owns the data?
- What Business Process it supports?
- System & Data documentation
- Data Model & Data Catalog
- How is the data Stored?
- What are the integration capabilities? (Eg. API, File extraction, Direct DB connection)
- Can we do an incremental load or full load?
- Data scope & Historical needs
- What is the expected dize of the extracts?
- Are there any volume limitations?
- How to avoid impacting the source systems performance?
- Authentication and authorisation

Coding: Data Ingestion:
I wrioe script to create tables in DataWarehouse database, with IF statement that drops table before creating it again if it already exists'

I then wrote script to bulk instert data from source file, truncating beforehand so that the data is fully loaded in each time withiout duplicating

After validating the data that had come in by counting the number of rows of data in each table and comparing to the source file I turned this script into a stored procedure

