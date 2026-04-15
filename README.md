# 🛒 HCL_HACKATHON_OmniChannel – Data Pipeline Project  

## 🚀 Project Overview  

This project demonstrates the design and implementation of a **data pipeline using Informatica Intelligent Cloud Services (IICS)**.  

The pipeline extracts sales data from multiple channels, consolidates it, enriches it with product details, and generates a clean **Daily Revenue Summary dataset**.  

---

## 📂 Data Description  

### 1. Online Sales (`Online_Sales.csv`)  

Contains online transaction data:  

* `TRANS_ID` – Transaction identifier  
* `PROD_ID` – Product reference  
* `QTY` – Quantity sold  
* `UNIT_PRICE` – Price per unit  
* `TAX` – Tax applied  
* `TRANS_DATE` – Transaction date  

---

### 2. Warehouse Sales (`Warehouse_Sales.csv`)  

Contains offline/warehouse transaction data:  

* `TRANS_ID` – Transaction identifier  
* `PROD_ID` – Product reference  
* `QTY` – Quantity sold  
* `UNIT_PRICE` – Price per unit  
* `TAX` – Tax applied  
* `TRANS_DATE` – Transaction date  

---

### 3. Product Master (`Product_Master.csv`)  

Contains product-related details:  

* `PROD_ID` – Unique product identifier  
* `PROD_NAME` – Name of product  
* `SUPPLIER_NAME` – Supplier of product  
* `CATEGORY` – Product category  

---

## 🎯 Objective  

The goal is to:  

* Combine sales data from multiple channels (**Online + Warehouse**)  
* Standardize and clean transaction data  
* Enrich data with product and supplier details  
* Filter invalid records  
* Calculate **TOTAL_LINE_ITEM_COST**  
* Generate a final summarized dataset  

---

## 🏗️ Data Model  

### 🔹 Dimension Table  

* `DIM_PRODUCT`  

Contains:  

* `PROD_ID`  
* `PROD_NAME`  
* `SUPPLIER_NAME`  
* `CATEGORY`  

---

### 🔹 Fact Table  

* `FACT_SALES (tgt_result_m2)`  

The fact table contains:  

* `TRANS_ID`  
* `TRANS_DATE`  
* `SUPPLIER_NAME`  

**Measure:**  

* `TOTAL_LINE_ITEM_COST`  

---

## ⚙️ Transformations Performed  

### ✅ Data Consolidation Mapping (`m_Data_Consolidation`)  

* Source: Online_Sales + Warehouse_Sales  
* Used **Union Transformation** to combine both sources  
* Applied **Expression Transformation**:  
  * Standardized data  
  * Converted `TRANS_DATE` into proper date format  
* Applied **Sorter Transformation**:  
  * Sorted data by `TRANS_DATE` and `PROD_ID`  
* Target: `tgt_result_m1`  

---

### ✅ Data Enrichment & Calculation Mapping (`m_Data_Enrichment`)  

* Source: `tgt_result_m1`  

* Used **Lookup Transformation**:  

  * Lookup file: Product_Master  
  * Condition:  
    ```
    PROD_ID = PROD_ID
    ```
  * Added fields:  
    * `PROD_NAME`  
    * `SUPPLIER_NAME`  
    * `CATEGORY`  

* Applied **Filter Transformation**:  

  ```
  NOT ISNULL(SUPPLIER_NAME)
  ```  

  * Removes records with missing supplier details  

* Applied **Expression Transformation**:  

  ```
  TOTAL_LINE_ITEM_COST = (UNIT_PRICE * QTY) + TAX
  ```  

* Target: `tgt_result_m2`  

---

## 🔁 Taskflow Design  

Execution flow:  

```
m_Data_Consolidation → Decision → m_Data_Enrichment
```  

### Flow Logic:  

1. Run `m_Data_Consolidation` (DT1)  
2. Decision Task:  
   * Condition → Task Status (DT1) = 1 (Success)  
3. Run `m_Data_Enrichment` (DT2) only if DT1 succeeds  
4. Stop execution if DT1 fails  

---

## 📊 Final Output Fields (`tgt_result_m2`)  

* `TRANS_ID`  
* `TRANS_DATE`  
* `SUPPLIER_NAME`  
* `TOTAL_LINE_ITEM_COST`  
