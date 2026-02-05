# Amazon Product Analytics – End-to-End Data Engineering & BI Project

## 1. Project Overview

### Objective

The objective of this project is to design and implement an **end-to-end analytics solution** using open e-commerce data to demonstrate practical understanding of:

* Lakehouse architecture (Bronze–Silver–Gold)
* Data ingestion and transformation using Databricks
* Delta Lake concepts
* Analytics-ready data modeling
* Business-focused dashboarding in Power BI
* Security design (Row-Level Security)

The solution simulates how an enterprise data team delivers **decision-ready dashboards** to business stakeholders.

---

## 2. Business Problem Statement

E-commerce platforms host thousands of products with varying prices, discounts, and customer feedback. Business stakeholders require a consolidated analytics view to:

* Understand **overall product quality** through customer ratings
* Analyze **discount strategies** and their effectiveness
* Identify **high-performing and underperforming product categories**
* Measure **customer engagement** using review volume

The goal is to build a scalable analytics pipeline that transforms raw product and review data into **actionable business insights**.

---

## 3. Dataset Description

### Source

Open Amazon product and review dataset (CSV format).

### Key Attributes

* Product details (name, category, pricing)
* Discount information
* Customer ratings and rating counts
* Review and user metadata

The dataset is provided as a **single flat file**, representing a common real-world ingestion scenario where raw source systems are not normalized.

---

## 4. Architecture Overview

### High-Level Architecture

```
Source (CSV)
   ↓
Databricks Volume
   ↓
Bronze Layer (Raw)
   ↓
Silver Layer (Cleaned & Modeled)
   ↓
Gold Layer (Analytics-Ready)
   ↓
Power BI Dashboards
```

### Tooling Assumptions

* Databricks Community Edition for data engineering
* Delta Lake for table storage
* Power BI for visualization and security

---

## 5. Data Lake Design (Bronze–Silver–Gold)

### Bronze Layer (Raw)

**Purpose:**

* Store raw data as received from the source
* Enable reprocessing and auditability

**Characteristics:**

* No transformations
* Schema inferred
* Append-only

**Table:**

* `bronze_amazon_sales`

---

### Silver Layer (Cleansed & Modeled)

**Purpose:**

* Clean and standardize raw data
* Separate business entities

**Key Transformations:**

* Standardized column names
* Converted pricing and rating fields to numeric types
* Removed invalid or null records

**Silver Tables:**

* `silver_dim_products`
* `silver_dim_users`
* `silver_dim_reviews`
* `silver_amazon_base`

---

### Gold Layer (Analytics-Ready)

**Purpose:**

* Provide curated tables optimized for BI tools
* Implement a star schema

**Fact Table:**

* `gold_fact_product_performance`

**Measures Included:**

* Rating
* Rating count
* Discount percentage
* Discount amount

**Dimension Tables:**

* `gold_dim_products`
* `gold_dim_users`
* `gold_dim_reviews`

---

## 6. Data Modeling Approach

### Grain Definition

The fact table grain is defined at:

> **Product × User × Review**

This allows accurate measurement of:

* Product-level performance
* Customer engagement
* Review-level analysis

### Star Schema

```
          gold_dim_products
                  |
 gold_dim_users — gold_fact_product_performance — gold_dim_reviews
```

This schema ensures:

* Simple relationships
* High query performance
* Clear business logic

---

## 7. Semantic Model & Measures

A semantic layer was created in Power BI to ensure consistent metric definitions.

### Core Measures

* Average Product Rating
* Total Products
* Average Discount Percentage
* Average Discount Amount
* Total Rating Count

### Aggregation Strategy

To avoid over-counting (due to review-level grain), **product-level aggregation** was applied using iterator-based DAX logic.

This ensures that KPIs reflect true business meaning rather than raw row-level calculations.

---

## 8. Dashboard Design & Insights

### Target Audience

* Product Managers
* Category Managers
* Business Leadership

---

### Dashboard Page 1: Executive Overview

**KPIs:**

* Average Product Rating
* Total Products
* Average Discount Percentage

**Insights:**

* Overall customer satisfaction
* Pricing strategy overview

---

### Dashboard Page 2: Category Performance

**Visuals:**

* Average Rating by Category (Bar Chart)
* Customer Engagement by Category (Bar Chart)

**Insights:**

* Identify top and bottom performing categories
* Understand engagement concentration

---

### Dashboard Page 3: Discount Effectiveness

**Visuals:**

* Discount % vs Average Rating (Scatter Plot)

**Insights:**

* Detect over-discounted but poorly rated products
* Identify high-rated products without heavy discounts

---

### Dashboard Page 4: Product-Level Analysis

**Visuals:**

* Product performance table

**Insights:**

* Enable detailed drill-down for operational decisions

---

## 9. Visualization Best Practices Applied

* KPI cards for executive scanning
* Bar charts for category comparison
* Scatter plots for correlation analysis
* Tables for operational detail
* Slicers for interactivity

The dashboard layout follows a **top-down storytelling approach**, moving from summary to detail.

---

## 10. Security Implementation

### Row-Level Security (RLS)

Example roles:

* Category Manager – restricted to assigned category
* Leadership – full access

RLS ensures that users only see data relevant to their responsibility.

---

## 11. Data Refresh Strategy

### Refresh Cadence

| Layer            | Frequency |
| ---------------- | --------- |
| Bronze → Silver  | Daily     |
| Silver → Gold    | Daily     |
| Power BI Dataset | Daily     |

This cadence balances data freshness with system efficiency and aligns with standard enterprise BI practices.

---

## 12. Key Learnings

* Single flat files can be transformed into scalable analytical models
* Correct grain definition is critical for meaningful KPIs
* Gold-layer design directly impacts dashboard quality
* Business-aligned visual design is as important as technical correctness

---

## 13. Conclusion

This project demonstrates a complete analytics lifecycle—from raw data ingestion to secure, insight-driven dashboards. The solution reflects real-world enterprise data engineering and BI practices, emphasizing clarity, scalability, and business relevance.

The architecture, transformations, and dashboards together form a robust foundation for data-driven decision-making.
