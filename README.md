# Design & Build SQL Data Warehouse
Building a modern data warehouse with SQL Server, including ETL processes, data modelling and analytics.
---
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

![Data Architecture](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/documents/SQL-DW-architecture.drawio.png)

![Tables Lineage Through Layers](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/documents/Layers%20Lineage.drawio)

#### Project Initialisation
##### Create Detailed Project Tasks in Notion
![Link to Notion Project Tracker](https://www.notion.so/Data-Warehouse-Project-1b65bf5c75d98091a8c8ffc55cb55c62)

##### Define Naming Conventions in Project
Case: snake_case
Language: English

**Bronze Rules**
<sourcesystem>_<entity> eg. crm_customer_info

**Silver Rules**
<sourcesystem>_<entity> eg. crm_customer_info

**Gold Rules**
<category>_<entity> eg. dim_customer, fact_sales, agg_customers, agg_sales_monthly

###### Column Naming Conventions
**Surrugate Keys**
<table_name>_key eg. customer_key is surrogate key in the customer table

**Technical Columns**
dwh_<column_name> eg. dwh_load_date

**Stored Procedure**
load_<layer> eg. load_bronze

##### Create GIT Repo & Prepare the Structure
Created database within SQL Server Management Studio, and then created the bronze, silver and gold schemas within the database. As can be seen in the script linked below:

![Crete database and schemas script](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/scripts/init_database.sql)

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
I wrote a script to create tables in DataWarehouse database, with IF statement that drops table before creating it again if it already exists.

I then wrote script to bulk instert data from source file, truncating beforehand so that the data is fully loaded in each time withiout duplicating

After validating the data that had come in by counting the number of rows of data in each table and comparing to the source file I turned this script into a stored procedure

#### Building Silver Layer

Analysing data & identifying required cleansing.

I loaded the data in from each of the tables in the bronze schema, and individually did checks on each of the columns to check for required cleansing. 

The checks I performed can be seen in the script ![Silver Quality Checks](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/tests/quality_checks_silver.sql)

To summarise, I checked for duplicates, NULLS, inconsistent data, and also normalised & made data consistent where required.

Wherever cleansing/normalisation was required in a table column, I made changes to the SELECT statement.

Once I had all of my SELECT statements for the tables pulling in the cleansed data & knew all of the new data types of the fields for each table, I created my silver DDL script which created the silver schema tables for me to insert the data into. 

![Silver DDL script](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/scripts/silver/ddl_silver.sql)

I created a stored procedure using all of the SELECT statements for each table including the transformations that would insert the data into the silver tables.

![Silver Stored Procedure](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/scripts/silver/proc_load_silver.sql)

Now I had completed building the Silver Layer of my data architecture.

### Building Gold Layer
The first step in building the gold layer was to identify the business objects - by this, I mean looking into what data was in each of the tables, and how they join together to create business objects. Below you can see how I've drawn this out to make 3 business objects from the 6 tables: Customers, Products & Sales.

![Business Objects Diagram](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/documents/Business%20Objects%20Diagram.png)

Now I had planned out the objects to build & how the silver tbales join together to create these objects I was able to start writing the script to build these objects.

For the gold layer, I decided to build these as Views instead of tables. Below is the script I used to build each of the views, which included loading the data from the silver tables, doing some final transformations of the fields (name changes, data integration & creating surrogate keys) & joining tables together.

![Building Gold Views Script](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/scripts/gold/ddl_gold.sql)

As part of building the views I ddi some quality checks on the data which can be seen in the script below:

![Gold Quality Checks](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/tests/quality_checks_gold.sql)

Once completed, I had finished building my data warehouse. 

I finished up by writing a data catalogue, describing the data in the gold layer as this is the data that bsuiness users would use - which can be seen here:

![Data Catalogue](https://github.com/jakebarr98/SQL-data-warehouse-data-engineering-project/blob/main/documents/data_catalogue.md)
