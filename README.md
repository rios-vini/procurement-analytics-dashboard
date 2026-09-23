# Procurement Analytics Dashboard — Supplier & Purchasing Performance

A Power BI procurement analytics project built using real public procurement data from the City of Austin.

The dashboard analyzes **procurement spend, supplier concentration, commodity concentration, purchasing patterns, and data quality** to demonstrate how public procurement data can be transformed into actionable analytical insights.

> **Project type:** Independent portfolio project
> **Data source:** City of Austin Open Data
> **Tool:** Microsoft Power BI
> **Focus:** Procurement Analytics · Spend Analysis · Supplier Analysis · Supply Chain · Business Intelligence

---

## Overview

Procurement teams need visibility into where money is being spent, which suppliers and commodities account for the largest share of expenditure, and where concentration may warrant further investigation.

This project uses a real public procurement dataset to build a structured analytical model and an interactive Power BI dashboard.

The analysis focuses on questions such as:

* How much is being spent?
* How has procurement spend evolved over time?
* Which suppliers account for the largest share of spend?
* How concentrated is spend across suppliers?
* Which commodities account for most procurement expenditure?
* Which commodities belong to the highest-spend ABC class?
* Where does supplier concentration vary significantly by commodity?
* What data-quality limitations affect the analysis?

The project deliberately avoids creating unsupported procurement KPIs when the source data does not contain the necessary information.

---

## Business Problem

A procurement dataset may contain millions or hundreds of thousands of transaction-level records, but raw transaction data alone does not provide an effective management view.

The objective of this project was to transform procurement transaction data into a dashboard that helps users:

1. Understand overall procurement expenditure.
2. Identify major suppliers and supplier concentration.
3. Identify high-spend commodities.
4. Prioritize commodities using ABC analysis.
5. Investigate supplier concentration within individual commodities.
6. Identify relevant data-quality limitations.
7. Distinguish between what the data supports and what it does not support.

This is an **analytical case study based on public data**, not an analysis performed as an employee or representative of the City of Austin.

---

## Dataset

The project uses the City of Austin Open Data dataset:

**Purchase Order Quantity & Price Detail for Commodity/Goods Procurements**

The dataset contains purchase-order and commodity-level procurement records.

### Analytical grain

The primary fact table operates at:

> **Purchase Order × Commodity Line**

This grain allows supplier, commodity, purchasing and spend analysis without reducing every purchase order to a single observation.

### Main fields used

The analysis uses fields including:

* Purchase Order
* Vendor Code
* Vendor Name
* Spend
* Quantity
* Unit Price
* Award Date
* Commodity
* Commodity Description
* Extended Description
* Vendor City
* Vendor State
* Vendor ZIP
* Vendor Country

---

## Project Scope

The project focuses on metrics that can be calculated defensibly from the available data.

### Included

* Total Spend
* Purchase Order Count
* Purchase Line Count
* Supplier Count
* Average PO Value
* Average Line Spend
* Average Unit Price
* Total Quantity
* Spend evolution over time
* Supplier spend ranking
* Supplier concentration
* Commodity spend ranking
* Commodity concentration
* ABC commodity classification
* Pareto analysis
* Quantity-related data-quality analysis

### Deliberately excluded

The dataset does not provide sufficient information to calculate several common procurement-performance KPIs reliably.

Therefore, the project does **not** fabricate or infer:

* OTIF
* Lead Time
* Procurement Savings
* Cost Avoidance
* Supplier Quality Score
* Supplier Risk Score
* Procurement Efficiency Score

These metrics would require additional operational, contractual or supplier-performance data.

---

## Data Model

The Power BI model follows a dimensional **Star Schema**.

```text
                    ┌───────────────┐
                    │   DimDate     │
                    └───────┬───────┘
                            │
                            │
┌───────────────┐     ┌─────▼─────────┐     ┌─────────────────┐
│   DimVendor   │────▶│ FactPurchase  │◀────│ DimCommodity    │
└───────────────┘     └────────────────┘     └─────────────────┘
```

### Fact table

**FactPurchase**

Contains the transaction-level procurement records.

### Dimension tables

**DimVendor**

* Supplier attributes

**DimCommodity**

* Commodity attributes

**DimDate**

* Date attributes used for time-based analysis

### Relationships

* DimVendor (1) → FactPurchase (*)
* DimCommodity (1) → FactPurchase (*)
* DimDate (1) → FactPurchase (*)

The dimensional model separates transactional data from descriptive dimensions and supports reusable DAX measures across the report.

---

## Key Metrics

The dashboard includes measures such as:

### Spend

```DAX
Total Spend =
SUM(FactPurchase[Spend])
```

### Purchase Orders

```DAX
PO Count =
DISTINCTCOUNT(FactPurchase[PurchaseOrder])
```

### Suppliers

```DAX
Supplier Count =
DISTINCTCOUNT(FactPurchase[VendorCode])
```

### Average PO Value

```DAX
Average PO Value =
DIVIDE([Total Spend], [PO Count])
```

### Supplier Share

```DAX
Supplier Share % =
DIVIDE(
    [Total Spend],
    CALCULATE([Total Spend], ALL(DimVendor))
)
```

### Spend YoY

```DAX
Spend YoY % =
DIVIDE(
    [Total Spend] - [Previous Year Spend],
    [Previous Year Spend]
)
```

Additional measures support supplier ranking, commodity ranking, supplier concentration, ABC analysis and data-quality analysis.

---

## Dashboard

The report contains three analytical pages.

### 1. Executive Overview

The Executive Overview provides a high-level view of procurement activity.

It includes:

* Total Spend
* PO Count
* Supplier Count
* Average PO Value
* Spend Evolution
* Top 10 Suppliers by Spend
* Top 10 Commodities by Spend

The page answers:

> **How much are we spending, and where is the spend concentrated at a high level?**

---

### 2. Supplier & Concentration

This page focuses on supplier concentration and the relationship between commodity spend and supplier concentration.

It includes:

* Top 1 Supplier Share
* Top 5 Supplier Share
* Top 10 Supplier Share
* Top 20 Supplier Share
* Top 20 Suppliers by Spend
* Commodity Spend vs. Top Supplier Concentration

The page answers:

> **Where is supplier spend concentrated, and how does supplier concentration vary across commodities?**

---

### 3. ABC Analysis

The ABC Analysis page applies a spend-based ABC classification to commodities.

Classification:

* **Class A:** cumulative spend up to approximately 80%
* **Class B:** cumulative spend from approximately 80% to 95%
* **Class C:** remaining commodities

The page includes:

* A Spend %
* A Commodity Count
* B Commodity Count
* C Commodity Count
* Pareto Analysis
* Top 10 Commodities
* ABC Detail Table

The page answers:

> **Which commodities account for most of the spend and should receive greater analytical attention?**

---

## Key Findings

### 1. Spend is highly concentrated across commodities

The dataset contains **10,653 distinct commodities**.

Only **171 commodities**, representing approximately **1.61%** of all commodities, account for approximately **80% of total spend**.

The ABC distribution is:

| Class | Commodities | Share of Commodities | Share of Spend |
| ----- | ----------: | -------------------: | -------------: |
| A     |         171 |                1.61% |           ~80% |
| B     |         577 |                5.42% |           ~15% |
| C     |       9,905 |               92.97% |            ~5% |

**Procurement implication:** Class A commodities can be prioritized for deeper sourcing, supplier and spend analysis.

**Limitation:** ABC classification is based on spend only. It does not measure operational criticality, supply risk, substitutability or strategic importance.

---

### 2. Overall supplier spend is distributed across a broad supplier base

Supplier concentration at the overall level is:

| Supplier Group      | Share of Spend |
| ------------------- | -------------: |
| Top 1               |          3.79% |
| Top 5               |         15.47% |
| Top 10              |         26.15% |
| Top 20              |         38.11% |
| Remaining Suppliers |         61.89% |

The largest individual supplier therefore represents less than 4% of total spend.

**Procurement implication:** supplier concentration should be evaluated beyond the single largest supplier, with the Top 20 providing a useful starting point for deeper analysis.

**Limitation:** spend concentration alone does not establish supplier risk. Additional information about criticality, alternatives, capacity and substitutability would be required.

---

### 3. Supplier concentration varies significantly by commodity

The commodity-level concentration analysis shows that the supplier structure can differ substantially between categories.

Examples observed in the analysis include:

* Power Consumption Meters — approximately 98% top-supplier share
* Water Meters — approximately 73%
* Impact Tools — approximately 67%
* B20 Bio-Diesel — approximately 67%
* Transformers, Power Distribution — approximately 33%
* Two-Way Radio — 100%

**Procurement implication:** high-spend commodities with highly concentrated supplier bases may warrant further investigation into supplier dependency and sourcing alternatives.

**Limitation:** the analysis identifies concentration, not supplier risk. A concentrated category is not automatically a high-risk category.

---

### 4. Transaction-level analysis provides greater procurement granularity

The dataset contains approximately:

* **166,919 purchase orders**
* **319,186 purchase lines**
* **1.91 lines per purchase order**
* **$12,609 average spend per line**

Analyzing the data at the purchase-order × commodity-line level allows supplier and commodity spend to be examined with greater granularity than a simple purchase-order-level analysis.

---

### 5. Quantity has significant data-quality limitations

The dataset contains:

* **107,635 lines with Quantity = 0**
* Approximately **$1.948B** of spend associated with zero-quantity lines
* Approximately **$2.076B** of spend associated with positive-quantity lines

Therefore, approximately **48.4% of total spend** is associated with lines where Quantity equals zero.

**Implication:** Quantity cannot be treated as a universal proxy for physical purchasing volume in this dataset.

For this reason, **Spend is retained as the primary financial measure**, and unsupported calculations such as universally reconstructing spend from `Quantity × Unit Price` are avoided.

**Limitation:** the dataset alone does not establish why these records have zero quantity, so they should not automatically be classified as errors.

---

## Spend Evolution

The annual spend values represented in the dashboard are:

| Year |  Spend |
| ---- | -----: |
| 2016 | ~$193M |
| 2017 | ~$173M |
| 2018 | ~$233M |
| 2019 | ~$258M |
| 2020 | ~$257M |
| 2021 | ~$329M |
| 2022 | ~$271M |
| 2023 | ~$326M |
| 2024 | ~$323M |
| 2025 | ~$377M |

The series shows substantial variation across years, including increases in 2018, 2021 and 2023, followed by a higher reported value in 2025.

Year-over-year interpretation should take into account the underlying date coverage of the source data, particularly when comparing periods that may not have equivalent completeness.

---

## Methodology

The project follows the general workflow:

```text
Raw Public Data
       ↓
Data Preparation
       ↓
Power Query Transformation
       ↓
Dimensional Data Model
       ↓
DAX Measures
       ↓
Procurement Analysis
       ↓
Power BI Dashboard
       ↓
Business Insights
```

### Data preparation

Power Query was used to prepare and structure the source data before loading it into the analytical model.

### Data modeling

The model was organized into a Star Schema with:

* one central fact table;
* supplier dimension;
* commodity dimension;
* date dimension.

### DAX

DAX measures were created for:

* spend;
* purchase orders;
* suppliers;
* averages;
* rankings;
* shares;
* year-over-year analysis;
* concentration;
* ABC analysis;
* data-quality analysis.

### ABC methodology

Commodities are ranked by spend and assigned to A, B or C according to cumulative spend:

```text
A → up to ~80%
B → ~80% to ~95%
C → remaining ~5%
```

The classification is deterministic and uses commodity description as a tie-breaker when required.

---

## Data Quality & Limitations

A key principle of this project is to distinguish between **what the data can demonstrate and what it cannot**.

The dataset supports spend, supplier, commodity and transaction analysis, but it does not contain all information required for a complete procurement-performance assessment.

Important limitations include:

### Quantity

A significant amount of spend is associated with zero-quantity lines. Quantity is therefore not treated as a universally reliable volume measure.

### Supplier risk

Supplier concentration is measurable, but supplier risk is not directly measurable from the available fields.

### Lead time

The dataset does not provide reliable order-to-delivery timestamps required to calculate lead time.

### OTIF

On-time-in-full performance requires promised dates, actual delivery dates and complete quantity fulfillment information, which are not available at the required level.

### Savings

Savings or cost avoidance cannot be calculated without a reliable baseline, negotiated price information or comparable sourcing scenario.

### Supplier quality

There is insufficient information to calculate supplier defect, rejection or quality-performance metrics.

These limitations are documented rather than replaced with assumptions or synthetic metrics.

---

## Tools & Technologies

* **Microsoft Power BI**
* **Power Query**
* **DAX**
* **Dimensional / Star Schema Modeling**
* **Data Quality Analysis**
* **ABC / Pareto Analysis**
* **Procurement & Spend Analytics**

---

## Project Structure

The repository is organized around the final analytical deliverable.

```text
procurement-analytics-dashboard/
│
├── README.md
│
├── dashboard/
│   └── Procurement_Analytics_Dashboard.pbix
│
├── screenshots/
│   ├── executive-overview.png
│   ├── supplier-concentration.png
│   └── abc-analysis.png
│
└── documentation/
    └── data-dictionary.md
```

The repository structure may evolve as supporting documentation and dashboard assets are added.

---

## Future Improvements

If additional procurement data were available, the analytical model could be extended with:

* supplier lead-time analysis;
* OTIF performance;
* contract compliance;
* price variance analysis;
* purchase price variance;
* supplier quality;
* supplier risk indicators;
* sourcing alternatives;
* category-level benchmarking;
* procurement savings and cost avoidance.

These extensions would require additional source data rather than assumptions derived from the current dataset.

---

## Project Purpose

This project was developed as a portfolio case study to demonstrate practical application of **Business Intelligence and Procurement Analytics** concepts using real-world public data.

The emphasis is not only on building visualizations, but on:

* structuring transactional data;
* designing an analytical model;
* developing reusable DAX measures;
* identifying meaningful procurement patterns;
* assessing data quality;
* communicating business implications;
* and clearly documenting analytical limitations.

The result is intended to demonstrate an analytical approach applicable to **Procurement, Supply Chain, Business Intelligence, Data Analytics and Process Improvement** environments.
