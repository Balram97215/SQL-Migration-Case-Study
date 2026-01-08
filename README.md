# Automated Data Validation Framework for Cloud Migration

![SQL](https://img.shields.io/badge/Language-SQL-blue) ![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-336791) ![Focus](https://img.shields.io/badge/Focus-Data_Quality_%26_Migration-green)

## 📌 Project Overview
**Role:** SQL Developer (Intern) | **Company:** NICE Actimize (Professional Services)  
**Tech Stack:** SQL (PostgreSQL, T-SQL), Excel (Data Mapping), pgAdmin  
**Domain:** Financial Crime Compliance (AML/Fraud Detection)

### Executive Summary
This project addressed a critical infrastructure gap during the strategic migration of Actimize's Anti-Money Laundering (AML) solutions from legacy on-premise databases (Microsoft SQL Server/Oracle) to a cloud-native **PostgreSQL** environment.

The primary objective was to transition the existing library of validation scripts which were written in incompatible T-SQL into a standardized, PostgreSQL compliant framework. This automation ensured data accuracy for critical compliance entities like Customer Due Diligence (CDD), Watch List Filtering (WLF) and Suspicious Activity Monitiring (SAM).

---

## ⚠️ The Challenge: The "Translation" Gap
The existing Data Validation (DV) library was heavily dependent on proprietary Microsoft SQL Server functions, making it incompatible with the new open-source PostgreSQL infrastructure. The migration faced three specific technical hurdles:

1.  **Syntactical Incompatibility:** Critical functions used in legacy scripts (e.g., `CHARINDEX`, `GETDATE`) did not exist in PostgreSQL, causing immediate syntax errors during testing.
2.  **Procedural Divergence:** Legacy scripts used unstructured `GOTO` statements for flow control, which PostgreSQL strictly does not support, requiring a complete logic restructure.
3.  **Data Type Mismatches:** Differences in handling boolean logic (`BIT` vs `BOOLEAN`) and currency (`MONEY` vs `NUMERIC`) threatened the precision of financial transaction data.

---

## 🛠️ The Solution: PostgreSQL Native Validation Engine
I developed a comprehensive validation framework using a rigorous Waterfall methodology (Discovery, Design, Development, Testing).

### 1. The Validation Taxonomy
I implemented a standard taxonomy of checks to ensure 100% coverage of the Data Requirements Document (DRD):

| Validation Type | SQL Logic Implemented | Business Purpose |
| :--- | :--- | :--- |
| **Null Checks** | `WHERE col IS NULL` | Attributes like `Account_Key` cannot be NULL or the detection engine fails. |
| **Referential Integrity** | `LEFT JOIN ... WHERE parent.Key IS NULL` | Verifies relationships (e.g., an Account cannot exist without an owner). |
| **Domain Integrity** | `NOT IN (SELECT Code FROM Ref_Table)` | Ensures codes (e.g., Currency = 'USD') match allowed reference values. |
| **Uniqueness** | `GROUP BY key HAVING COUNT(*) > 1` | Prevents duplicate Primary Keys which cause double-counting of risk. |

### 2. Technical Refactoring (Code Case Study)
*Note: The code below demonstrates the logic pattern used. No proprietary client data is shown.*

#### **A. Solving String Manipulation Conflicts**
Legacy scripts used `CHARINDEX` to parse data formats, which is invalid in Postgres. I refactored these queries to use `POSITION`.

**Legacy (T-SQL):**
```sql
-- Fails in Postgres
SELECT * FROM Transactions 
WHERE CHARINDEX('AML', Reference_Text) > 0;
```
**Refactored (PostgreSQL):**
```sql
-- Optimized for Cloud Environment
SELECT * FROM Transactions 
WHERE POSITION('AML' IN Reference_Text) > 0;
```
#### **B. Handling Temporal Logic**
AML detection is time-bound (e.g., "Transactions in the last 30 days"). The migration required shifting from GETDATE() to NOW() to prevent syntax errors in "Future Dated Transaction" checks.

#### **C. Restructuring Control Flow**
I replaced legacy spaghetti code (GOTO jumps) with structured control blocks, modernizing the procedural flow:
```sql
-- Legacy: GOTO Error_Handler
-- New: Structured Exception Handling
DO
BEGIN 
    -- Validation Logic 
EXCEPTION WHEN OTHERS THEN 
    RAISE NOTICE 'Error in validation block'; 
END;
```

## 📚 Training Artifacts Catalog
To ensure long-term sustainability, I designed a Training Artifacts Catalog to bridge the knowledge gap for future analysts:

1. **Syntax Translation Matrix:** A comprehensive mapping document outlining rules for T-SQL to PL/pgSQL conversion (e.g., Map MONEY -> NUMERIC).

2. **Standardized Script Library:** A centralized repository of validated, clean SQL scripts for CDD, WLF, and SAM modules.

## 🚀 Impact & Results

1. **80% Efficiency Gain:** Automated the error detection process, slashing the data validation cycle time by ~80% and saving hundreds of hours of manual analysis per client implementation.

2. **Standardization:** Established a new global standard for PostgreSQL based data validation, ensuring consistency across AMER, EMEA, and APAC regions.

3. **Executive Buy-In:** Final project findings and efficiency gains were presented directly to the C-suite.


##### **Note: This repository contains a summary of the logic and methodology used based on the internship report. Proprietary code and client data have been omitted for confidentiality.**

