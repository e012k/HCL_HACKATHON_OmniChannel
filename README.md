# HCL_HACKATHON_OmniChannel
OmniChannel_Sales Consolidator


🛒 OmniChannel Sales Consolidator
📌 Project Overview

The OmniChannel Sales Consolidator is a data integration project built using Informatica Intelligent Cloud Services (IICS).
It automates the process of combining sales data from multiple sources, enriching it with product and supplier details, and generating a clean Daily Revenue Summary.

📂 Data Sources
📄 File 1: Online_Sales.csv (Source A)

Contains online transaction data.

📄 File 2: Warehouse_Sales.csv (Source B)
Trans_ID, Prod_ID, Qty, Unit_Price, Tax, Trans_Date
📄 File 3: Product_Master.csv (Lookup Source)
Prod_ID, Prod_Name, Supplier_Name, Category
⚙️ Solution Architecture

The solution is divided into two mappings:

🔹 Mapping 1: Data Consolidation --

Flow:
. Sources
       Online_Sales
       Warehouse_Sales
. Union Transformation
       Combines both sources
. Expression Transformation
      Converts Trans_Date into proper date format
      Standardizes data
. Sorter Transformation
      Sorts data by:
      Trans_Date
      Prod_ID
. Target Load
      Loads cleaned data into tgt_result_m1
      
      
🔹 Mapping 2: Data Enrichment, Filtering & Calculation --

Flow:
. Source 
     Tgt_result_m1
. Lookup Transformation
     Lookup File: Product_Master.csv
     Lookup Condition : SRC.Prod_ID = LKP.Prod_ID
     Adds:
     Product Name
     Supplier Name
     Category
. Filter Transformation
     NOT ISNULL(Supplier_Name)
     Removes invalid records (missing supplier info)
. Expression Transformation
     Calculates:
     TotalLineItemCost = (Unit_Price * Qty) + Tax
. Target Load
     Final data stored in tgt_result_m2

     
🔄 Taskflow Design --

📌 Control Flow Logic
. Data Task 1 (DT1)
    Executes Mapping 1
. Decision Task
    Condition:
    Task Status (DT1) = Succeeded
    Implementation:
    Select DT1 field

Apply condition:

content = 1
(1 represents Success in IICS)
Data Task 2 (DT2)
Executes Mapping 2 only if DT1 is successful
End
Stops execution if Mapping 1 fails
📊 Final Output Fields (tgt_result_m2)
Trans_ID
Trans_Date
Supplier_Name
TotalLineItemCost

🧠 Data Modeling (Fact & Dimension)
📌 Fact Table → Fact_Sales (tgt_result_m2)

Contains measurable business data:

TotalLineItemCost
(Derived from Qty, Unit_Price, Tax)
📌 Dimension Table → Dim_Product (Product_Master)

Contains descriptive attributes:

Prod_ID
Prod_Name
Trans_date
Supplier_Name
Total_line_item_cost
