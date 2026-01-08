# Automated Data Validation Framework for Cloud Migration

![SQL](https://img.shields.io/badge/Language-SQL-blue) ![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-336791) ![Focus](https://img.shields.io/badge/Focus-Data_Quality_%26_Migration-green)

## 📌 Project Overview
**Role:** SQL Developer (Intern) | **Company:** NICE Actimize (Professional Services)  
**Tech Stack:** SQL (PostgreSQL, T-SQL), Excel (Data Mapping), pgAdmin  
**Domain:** Financial Crime Compliance (AML/Fraud Detection)

### Executive Summary
[cite_start]This project addressed a critical infrastructure gap during the migration of Anti-Money Laundering (AML) solutions from legacy on-premise databases (SQL Server/Oracle) to a cloud-native **PostgreSQL** environment[cite: 4, 6].

[cite_start]I engineered a standardized **Data Validation Framework**—a library of optimized SQL scripts designed to validate complex banking data schemas against strict regulatory requirements[cite: 9, 96]. [cite_start]This framework replaced manual, error-prone checks, ensuring data integrity for high-stakes compliance systems[cite: 126].

---

## ⚠️ The Challenge
Financial Crime Compliance (FCC) systems require absolute data precision. [cite_start]A single `NULL` value in a key attribute or a dropped `Sector_Code` can cause regulatory detection engines to fail[cite: 101, 108]. The migration process faced three specific technical hurdles:

1.  **Syntactical Incompatibility:** The existing validation library was written in T-SQL. [cite_start]Critical functions (e.g., `CHARINDEX`, `GETDATE`) did not exist in the target PostgreSQL environment, causing immediate script failures during the "Lift and Shift" process[cite: 7, 63, 70].
2.  [cite_start]**Procedural Divergence:** Legacy scripts relied on unstructured `GOTO` statements for error handling, which PostgreSQL strictly does not support[cite: 82, 83].
3.  [cite_start]**Data Type Mismatches:** Differences in handling boolean logic (`BIT` vs `BOOLEAN`) and currency (`MONEY` vs `NUMERIC`) threatened financial precision[cite: 89, 93].

---

## 🛠️ The Solution
[cite_start]I developed a **PostgreSQL-Native Validation Engine** utilizing a rigorous Waterfall methodology[cite: 11, 14]. The solution focused on refactoring logic to be cloud-compatible while establishing a permanent knowledge base for the team.

### 1. The Validation Taxonomy
[cite_start]I implemented a structured taxonomy of checks to ensure 100% coverage of the Data Requirements Document (DRD)[cite: 16, 99]:

| Validation Type | SQL Logic Implemented | Business "Why" |
| :--- | :--- | :--- |
| **Null Checks** | `WHERE attribute IS NULL` | [cite_start]Critical attributes (e.g., `Account_Key`) cannot be missing, or the risk model fails[cite: 101]. |
| **Referential Integrity** | `LEFT JOIN ... WHERE parent.Key IS NULL` | [cite_start]Ensuring no "Orphaned Accounts" exist without an owner in the Master Data[cite: 102]. |
| **Domain Integrity** | `NOT IN (SELECT Code FROM Ref_Table)` | [cite_start]Validating that codes (e.g., Currency = 'USD') match the allowed reference values[cite: 102]. |
| **Uniqueness** | `GROUP BY key HAVING COUNT(*) > 1` | [cite_start]Preventing duplicate Primary Keys which cause double-counting of risk exposure[cite: 102]. |

### 2. Technical Refactoring (Code Case Study)
*Note: The code below demonstrates the logic pattern used. No proprietary client data is shown.*

#### **A. Solving String Manipulation Conflicts**
Legacy scripts used `CHARINDEX` to parse data formats, which is invalid in Postgres. [cite_start]I refactored these queries to use `POSITION` or `STRPOS`[cite: 66, 71].

**Legacy (T-SQL):**
```sql
-- Fails in Postgres
SELECT * FROM Transactions 
WHERE CHARINDEX('AML', Reference_Text) > 0;
