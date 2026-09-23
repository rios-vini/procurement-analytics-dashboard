# Data Dictionary

## Dataset

**Source:** City of Austin Open Data — Purchase Order Quantity & Price Detail for Commodity/Goods Procurements

The dataset contains public procurement transaction records covering purchase orders, commodities, suppliers, quantities and prices.

This project uses the dataset as an independent public-data analysis and does not represent employment or professional experience with the City of Austin.

## FactPurchase

The fact table contains procurement transaction lines at the **Purchase Order × Commodity Line** grain.

| Field                  | Description                                      | Role                     |
| ---------------------- | ------------------------------------------------ | ------------------------ |
| `PurchaseOrder`        | Purchase order identifier                        | Transaction ID           |
| `VendorCode`           | Supplier identifier                              | Supplier key             |
| `VendorName`           | Supplier name                                    | Supplier attribute       |
| `Commodity`            | Commodity code                                   | Commodity identifier     |
| `CommodityDescription` | Commodity description                            | Commodity attribute      |
| `ExtendedDescription`  | Additional description of the purchased item     | Descriptive attribute    |
| `Spend`                | Monetary value associated with the purchase line | Primary financial metric |
| `Quantity`             | Recorded quantity for the purchase line          | Volume-related metric    |
| `UnitPrice`            | Recorded unit price                              | Price-related metric     |
| `AwardDate`            | Date associated with the purchase order award    | Date key                 |
| `VendorCity`           | Supplier city                                    | Geographic attribute     |
| `VendorState`          | Supplier state                                   | Geographic attribute     |
| `VendorZip`            | Supplier ZIP code                                | Geographic attribute     |
| `VendorCountry`        | Supplier country                                 | Geographic attribute     |

## DimVendor

Supplier dimension used to analyze purchasing concentration and supplier performance.

| Field        | Description                |
| ------------ | -------------------------- |
| `VendorCode` | Unique supplier identifier |
| `VendorName` | Supplier name              |

## DimCommodity

Commodity dimension used to analyze spend distribution and ABC classification.

| Field                  | Description           |
| ---------------------- | --------------------- |
| `Commodity`            | Commodity code        |
| `CommodityDescription` | Commodity description |

## DimDate

Date dimension used for time-based analysis.

The model covers the available procurement dates in the source dataset and is related to `FactPurchase[AwardDate]`.

## Key Measures

| Measure                                   | Purpose                                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| `Total Spend`                             | Total procurement spend                                      |
| `PO Count`                                | Distinct number of purchase orders                           |
| `Supplier Count`                          | Distinct number of suppliers                                 |
| `Average PO Value`                        | Average spend per purchase order                             |
| `Average Line Spend`                      | Average spend per purchase line                              |
| `Average Unit Price`                      | Average recorded unit price                                  |
| `Total Quantity`                          | Total recorded quantity                                      |
| `Supplier Share %`                        | Supplier's share of total spend                              |
| `Supplier Rank`                           | Supplier ranking by spend                                    |
| `Commodity Spend`                         | Spend associated with a commodity                            |
| `Commodity Rank`                          | Commodity ranking by spend                                   |
| `Commodity Spend Share %`                 | Commodity share of total spend                               |
| `Supplier Share within Commodity %`       | Supplier share within the selected commodity                 |
| `Top 1 Supplier Share within Commodity %` | Share of commodity spend represented by its largest supplier |
| `Average Lines per PO`                    | Average number of purchase lines per purchase order          |
| `Zero Quantity Lines`                     | Number of purchase lines with zero recorded quantity         |
| `Spend with Zero Quantity`                | Spend associated with zero-quantity lines                    |
| `Spend with Positive Quantity`            | Spend associated with positive-quantity lines                |

## ABC Analysis

ABC classification is applied at the commodity level based on cumulative spend.

| Class | Interpretation                                                               |
| ----- | ---------------------------------------------------------------------------- |
| A     | Highest-spend commodities representing approximately 80% of cumulative spend |
| B     | Commodities representing the next approximately 15% of cumulative spend      |
| C     | Remaining commodities representing approximately 5% of cumulative spend      |

The classification is intended to support prioritization of procurement analysis and does not represent a formal inventory policy.

## Important Data Limitations

The source dataset does not provide sufficiently reliable information for several procurement performance indicators.

Therefore, this project does **not** claim to measure:

* On-Time In-Full (OTIF)
* Supplier delivery performance
* Lead time
* Procurement savings
* Negotiated savings
* Supplier quality performance
* Supplier risk
* Purchase order cycle time

Quantity also requires caution when interpreting the data. A substantial portion of procurement spend is associated with lines where the recorded quantity is zero. For this reason, **Spend is treated as the primary financial measure**, while Quantity is used as a supplementary field.

## Analytical Grain

The fundamental analytical grain is:

> **One purchase order × one commodity line**

Purchase order counts therefore use distinct purchase order identifiers rather than counting transaction rows.
