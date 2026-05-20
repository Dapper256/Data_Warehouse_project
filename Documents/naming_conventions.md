#  Data Warehouse Naming Conventions

This document outlines the standardized naming conventions used for schemas, tables, views, columns, and other database objects within the data warehouse infrastructure.

---

##  Table of Contents
1. [General Principles](#-general-principles)
2. [Table Naming Conventions](#%EF%B8%8F-table-naming-conventions)
    * [Bronze Rules](#1-bronze-rules)
    * [Silver Rules](#2-silver-rules)
    * [Gold Rules](#3-gold-rules)
3. [Column Naming Conventions](#-column-naming-conventions)
    * [Surrogate Keys](#surrogate-keys)
    * [Technical Columns](#technical-columns)
4. [Stored Procedures](#-stored-procedures)

---

##  General Principles

* **Casing:** Always use `snake_case`. All letters must be lowercase, with underscores (`_`) used to separate words.
* **Language:** Use clear, professional English for all object names.
* **Avoid Reserved Words:** Do not use SQL reserved keywords (e.g., `select`, `table`, `where`, `date`) as object names.

---

##  Table Naming Conventions

The data warehouse follows the **Medallion Architecture** structure. Table names change patterns depending on the data layer.

### 1. Bronze Rules (Raw Data Layer)
All names must start with the source system name as a prefix, and the subsequent table names should closely match their original source names.
* **Pattern:** `[source_system]_[original_table_name]`
* **Components:**
  * `[source_system]`: Name of the source system (e.g., `crm`, `erp`).
  * `[original_table_name]`: Exact table name imported from the source system.
* **Example:** `crm_customer_info` → Raw customer information ingested directly from the CRM system.

### 2. Silver Rules (Cleaned & Conformed Layer)
Tables in this layer represent cleaned, deduplicated, and standardized data, while still retaining the foundational structure of the source tracking.
* **Pattern:** `silver_[source_system]_[standardized_name]`
* **Components:**
  * `silver_`: Indicates the conformed, cleaned layer.
  * `[source_system]`: Tracks data lineage back to its original platform.
* **Example:** `silver_crm_customers` → Cleaned and validated customer data originating from the CRM.

### 3. Gold Rules (Business / Analytics Layer)
All tables must use meaningful, business-aligned names optimized for reporting, starting with a functional category prefix.
* **Pattern:** `[category]_[business_domain_name]`
* **Components:**
  * `[category]`: Describes the architectural role of the table (e.g., `dim`, `fact`).
  * `[business_domain_name]`: Descriptive name aligned with business concepts.

####  Glossary of Category Patterns
| Pattern | Meaning | Example(s) |
| :--- | :--- | :--- |
| `dim_` | **Dimension Table:** Contains descriptive attributes (who, what, where). | `dim_customers`, `dim_products` |
| `fact_` | **Fact Table:** Contains quantitative metrics and foreign keys (transactions). | `fact_sales`, `fact_inventory` |
| `report_` | **Report Table:** Pre-aggregated datasets built for specific dashboards. | `report_sales_monthly` |

---

##  Column Naming Conventions

### Surrogate Keys
All primary keys in dimension tables must use a standardized surrogate key naming structure ending with the `_key` suffix.
* **Pattern:** `[entity_name]_key`
* **Components:**
  * `[entity_name]`: Refers to the business entity or table it represents.
  * `_key`: Dedicated suffix indicating the column is a system-generated surrogate key.
* **Example:** `customer_key` → Unique surrogate key in the `dim_customers` table.

### Technical Columns
All system-generated metadata and technical tracking columns must start with the prefix `dwh_`, followed by a descriptive name indicating its operational purpose.
* **Pattern:** `dwh_[column_name]`
* **Components:**
  * `dwh_`: Prefix reserved exclusively for system-generated data warehouse metadata.
  * `[column_name]`: Descriptive operational name.
* **Example:** `dwh_load_date` → Timestamp storing exactly when the record was appended into the warehouse.

---

##  Stored Procedures

All stored procedures utilized for processing, transforming, and loading data pipelines must follow a predictable, operational pattern:
* **Pattern:** `load_[target_layer]`
* **Components:**
  * `load_`: Action prefix identifying the procedure's main function.
  * `[target_layer]`: Represents the specific data tier being populated (e.g., `bronze`, `silver`, `gold`).

### Examples:
* `load_bronze` → Stored procedure orchestrating data ingestion into the Bronze layer.
* `load_silver` → Stored procedure executing cleaning and transformation into the Silver layer.
* `load_gold` → Stored procedure processing dimensional modeling rules into the Gold layer.
