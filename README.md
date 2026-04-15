# HCL_HACKATHON_OmniChannel
OmniChannel_Sales Consolidator


📂 Data Description

1. Online Sales (Online_Sales.csv)

Contains online transaction data:

TRANS_ID – Transaction identifier
PROD_ID – Product reference
QTY – Quantity sold
UNIT_PRICE – Price per unit
TAX – Tax applied
TRANS_DATE – Transaction date

2. Warehouse Sales (Warehouse_Sales.csv)

Contains offline/warehouse transaction data:

TRANS_ID – Transaction identifier
PROD_ID – Product reference
QTY – Quantity sold
UNIT_PRICE – Price per unit
TAX – Tax applied
TRANS_DATE – Transaction date

3. Product Master (Product_Master.csv)

Contains product-related details:

PROD_ID – Unique product identifier
PROD_NAME – Name of product
SUPPLIER_NAME – Supplier of product
CATEGORY – Product category
🎯 Objective

The goal is to:

Combine sales data from multiple channels (Online + Warehouse)
Standardize and clean transaction data
Enrich data with product and supplier details
Filter invalid records
Calculate Total Line Item Cost
Generate a final summarized dataset

⚙️ Transformations Performed

✅ Mapping 1: Data Consolidation (m_Data_Consolidation)
Source: Online_Sales + Warehouse_Sales
Union Transformation: Combines both sources
Expression Transformation:
Standardizes data
Converts TRANS_DATE into proper date format
Sorter Transformation:
Sorts data by TRANS_DATE and PROD_ID
Target: tgt_result_m1

✅ Mapping 2: Data Enrichment & Calculation (m_Data_Enrichment)
Source: tgt_result_m1

Lookup Transformation:

Lookup File: Product_Master
Condition: PROD_ID = PROD_ID
Adds:
PROD_NAME
SUPPLIER_NAME
CATEGORY

Filter Transformation:

Condition: NOT ISNULL(SUPPLIER_NAME)
Removes records with missing supplier details

Expression Transformation:

TOTAL_LINE_ITEM_COST = (UNIT_PRICE * QTY) + TAX
Target: tgt_result_m2


🔁 Taskflow Design

Execution Flow:

Step 1: Run m_Data_Consolidation (DT1)
Step 2: Decision Task
Condition: Task Status (DT1) = 1 (Success)
Step 3: Run m_Data_Enrichment (DT2) only if DT1 succeeds
Step 4: End execution if DT1 fails
📊 Final Output Fields (tgt_result_m2)
TRANS_ID
TRANS_DATE
SUPPLIER_NAME
TOTAL_LINE_ITEM_COST
