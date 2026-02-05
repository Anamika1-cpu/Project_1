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
<img width="1326" height="796" alt="Screenshot 2026-02-05 at 12 23 16 PM" src="https://github.com/user-attachments/assets/7e344c99-7013-4824-a44c-b70791b3e9bf" />
This schema ensures:

* Simple relationships
* High query performance
* Clear business logic

---

## 7. Semantic Model & Measures

### Semantic Model Overview

The Gold layer tables were exposed to Power BI and organized into a star schema–based semantic model. Relationships were defined between the fact and dimension tables to enable category-wise and product-wise analysis.

Given the review-level granularity of the data and challenges with rating aggregation, the semantic model prioritizes **stable, engagement- and pricing-based KPIs** over raw average ratings.

### Final KPIs Used

The following KPIs were finalized for the dashboard:

* **Total Users** – distinct count of users who have provided reviews
* **Total Products** – distinct count of products available in the dataset
* **Average Discount Percentage** – average of discount percentages across products
* **Total Rating Count** – total volume of customer ratings (used as a proxy for engagement)

These KPIs are robust, easy to interpret, and well-aligned with business decision-making.

---

## 8. Dashboard Design & Insights

### Target Audience

* Product Managers
* Category Managers
* Business Leadership

The dashboard is designed to answer the following high-level questions:

* Which categories contribute the most in terms of pricing and engagement?
* Where is customer engagement concentrated?
* How do discounts vary across products and categories?

---

### Dashboard KPIs (Top Section)

The top section of the dashboard provides a quick summary of platform-wide metrics:

* **Total Users** – number of unique reviewers
* **Total Products** – size of the product catalog
* **Average Discount %** – overall discounting strategy intensity

These KPIs allow stakeholders to quickly assess the scale, reach, and pricing posture of the platform.

---

### Pricing Analysis

**Visual:** Sum of Actual Price by Category (Bar Chart)

**Insight:**

* Highlights categories contributing the highest overall product value
* Helps identify premium-heavy versus low-value categories

---

### Customer Engagement Analysis

**Visual:** Sum of Rating Count by Category (Bar Chart)

**Insight:**

* Measures customer engagement through review volume
* Identifies categories with high customer interaction and visibility

---

### Discount Analysis

**Visuals:**

* Sum of Discounted Price by Category
* Sum of Actual Price by Discount Percentage

**Insights:**

* Shows how discounted pricing is distributed across categories
* Helps understand whether high discounts are applied to high-value or low-value products

---

### Product Feature & Rating Distribution

**Visual:** Average Rating by Category / Product Attributes (Bar Chart)

**Insight:**

* Provides a relative comparison of customer perception across product attributes and categories
* Used as a supporting analytical view rather than a primary KPI

---

### Dashboard Summary

The dashboard emphasizes **pricing strength, customer engagement, and discount behavior** rather than volatile average ratings. This approach ensures the insights remain reliable despite review-level data granularity.

---
<img width="1549" height="881" alt="Screenshot 2026-02-05 at 9 34 59 AM" src="https://github.com/user-attachments/assets/964c7aa4-bc6f-4a1c-bb53-e33338500c4d" />
<img width="1557" height="875" alt="Screenshot 2026-02-05 at 9 34 43 AM" src="https://github.com/user-attachments/assets/4d42521b-ab72-42b7-92aa-91c1722eaf67" />

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

The architecture, transformations, and dashboards together form a robust foundation for data-driven decision-making.
