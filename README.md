# 📊 DAX Depo — Power BI DAX Measures Project

A comprehensive Power BI project demonstrating advanced DAX (Data Analysis Expressions) — building calculated measures covering aggregation, filter context manipulation, conditional logic, and time intelligence across a Star Schema data model with Sales, Returns, Customers, Products, Regions and Dates.

---

## 📌 Project Overview

| Property | Details |
|---|---|
| Tool | Microsoft Power BI Desktop |
| File | DAX_Depo.pbix |
| Student | Ravindra Kumar |
| GR ID | 11638 |
| Total Tables | 7 (2 Fact + 4 Dimension + 1 Measures Table) |
| Report Page | Matrix_Visuals |
| Schema Type | Star Schema |
| Total Visuals | 6 Matrix (Pivot) Tables |
| Total DAX Measures | 11 Measures |
| Power BI Release | 2026.06 (Cloud) |

---

## 🗃️ Data Model — 7 Tables

| Table | Type | Key Column | Role in Model |
|---|---|---|---|
| Sales_Fact | Fact Table | SalesID | Core transactional data — Revenue, Cost, Sales_Category |
| Returns_Fact | Fact Table | ReturnID | Returns transactions — linked to Sales via SalesID |
| Customer_Dim | Dimension | CustomerID | Customer profiles — Segment (Gold / Silver / Platinum) |
| Product_Dim | Dimension | ProductID | Product info — Category, Subcategory, Brand |
| Region_Dim | Dimension | RegionID | Geographic mapping — RegionName, Country, State, City |
| Date_Dim | Dimension | DateKey | Date intelligence — MonthName, Quarter, Year, FiscalYear |
| Measures (2) | Measures Table | — | Dedicated table housing all 11 DAX measures (no rows) |

---

## 🔗 Relationships & Cardinality

| From Table (Dimension) | Key Column | To Table (Fact) | Cardinality | Cross-Filter | Status |
|---|---|---|---|---|---|
| Customer_Dim | CustomerID | Sales_Fact | 1 : * | Single | ✅ Active |
| Product_Dim | ProductID | Sales_Fact | 1 : * | Single | ✅ Active |
| Region_Dim | RegionID | Sales_Fact | 1 : * | Single | ✅ Active |
| Date_Dim | DateKey | Sales_Fact | 1 : * | Single | ✅ Active |
| Date_Dim | DateKey | Returns_Fact | 1 : * | Single | ⚪ Inactive |
| Returns_Fact | SalesID | Sales_Fact | * : * | Single | 🔗 Reference |

> **Note:** The Date_Dim → Returns_Fact relationship on DateKey is kept **inactive** (dotted line) to avoid filter ambiguity. It is activated inside specific DAX measures using `USERELATIONSHIP()`.

---

## 🧮 DAX Measures — Measures (2) Table

All 11 measures are stored in the dedicated **Measures (2)** table — a best-practice approach for keeping calculated logic separate from raw data tables.

### 📦 Aggregation Measures

| Measure | DAX Functions Used | Description |
|---|---|---|
| Total_Sales | `SUM()` | Sum of all sales revenue |
| Total_Items | `SUM()` / `COUNT()` | Total quantity of items sold |
| Total_Cost | `SUM()` | Total cost across all sales |
| Total_Profit | `SUM()` / subtraction | Revenue minus cost |

### 🔍 Filter & Context Measures

| Measure | DAX Functions Used | Description |
|---|---|---|
| Total_Sales_ALL | `CALCULATE()`, `ALL()` | Grand total ignoring all active filters |
| Sales_% of Total | `DIVIDE()`, `ALL()` | Each row's percentage share of grand total |
| North_Sales | `CALCULATE()`, `FILTER()` | Sales filtered to North region only |
| Sales_Above_Avg | `CALCULATE()`, `AVERAGEX()`, `FILTER()` | Sales only where revenue exceeds average |
| High_Value_Sales | `CALCULATE()`, `FILTER()` | Sales above a defined high-value threshold |

### ⏱️ Time Intelligence Measures

| Measure | DAX Functions Used | Description |
|---|---|---|
| North_2023_Sales | `CALCULATE()`, `USERELATIONSHIP()` | North region sales for FY2023 — activates inactive Date→Returns relationship |
| YTD_Sales | `TOTALYTD()` / `DATESYTD()` | Year-to-date cumulative sales |
| Last_6_Months_Sales | `DATESINPERIOD()`, `LASTDATE()` | Rolling 6-month trailing sales window |
| YoY_Growth% | `DIVIDE()`, `SAMEPERIODLASTYEAR()` | Year-over-year sales growth percentage |

---

## 📊 Matrix Visual Verification (Page: Matrix_Visuals)

Six Matrix (Pivot Table) visuals verify all DAX measures across different filter contexts.

### Visual 1 — Sales by Region (ALL & Percentage)
| Field | Value |
|---|---|
| Rows | Region_Dim[RegionName] |
| Columns | *(Single column layout)* |
| Values | Total_Sales \| Total_Sales_ALL \| Sales_% of Total |
| Purpose | Verifies `CALCULATE + ALL()` for grand total alongside filtered totals, and `DIVIDE()` for share percentages |

### Visual 2 — Region Comparison with Conditional Measures
| Field | Value |
|---|---|
| Rows | Region_Dim[RegionName] |
| Columns | *(Single column layout)* |
| Values | Total_Sales \| North_Sales \| Sales_Above_Avg \| High_Value_Sales |
| Purpose | Verifies `FILTER()` context transitions — North_Sales locks region; conditional measures apply threshold logic |

### Visual 3 — Region with Time-Filtered & YTD Measures
| Field | Value |
|---|---|
| Rows | Region_Dim[RegionName] |
| Columns | *(Single column layout)* |
| Values | Total_Sales \| Total_Sales_ALL \| North_2023_Sales \| YTD_Sales |
| Purpose | Tests `USERELATIONSHIP()` for inactive Date path and `TOTALYTD()` for cumulative figures |

### Visual 4 — Monthly Sales with Time Intelligence
| Field | Value |
|---|---|
| Rows | Date_Dim[MonthName] |
| Columns | *(Single column layout)* |
| Values | Total_Sales \| YTD_Sales \| Last_6_Months_Sales \| YoY_Growth% |
| Purpose | Full time-intelligence verification — YTD accumulation, rolling 6-month window, and year-over-year comparison |

### Visual 5 — Sales by Category (Cost & Items)
| Field | Value |
|---|---|
| Rows | Sales_Fact[Sales_Category] |
| Columns | *(Single column layout)* |
| Values | Total_Sales \| Total_Items \| Sales_% of Total \| Total_Cost |
| Purpose | Validates aggregation measures using Sales_Category as row context — tests SUM of items and cost |

### Visual 6 — Customer Segment Profitability
| Field | Value |
|---|---|
| Rows | Customer_Dim[Segment] |
| Columns | *(Single column layout)* |
| Values | Total_Sales \| Total_Items \| Total_Profit \| High_Value_Sales |
| Purpose | Verifies profit calculation and high-value threshold across Gold, Silver, and Platinum segments |

---

## 🚧 Challenges & Solutions

| Challenge | Solution |
|---|---|
| Inactive relationship between Date_Dim & Returns_Fact causing filter ambiguity | Kept ReturnDate relationship inactive; activated inside `North_2023_Sales` using `USERELATIONSHIP()` |
| Bidirectional filters causing circular filter paths | Set all relationships to Single cross-filter direction |
| Many-to-Many ambiguity between Returns_Fact and Sales_Fact | Adjusted join keys and cardinality; used reference/bridge approach |
| Organizing many DAX measures cleanly | Created a dedicated `Measures (2)` table via Enter Data; all measures stored there |
| YoY measure returning errors when prior year data is absent | Used `DIVIDE()` with `BLANK()` handling inside `YoY_Growth%` |
| Rolling window measure across months | Used `DATESINPERIOD()` combined with `LASTDATE()` for a dynamic 6-month trailing window |

---

## 🛠️ Tech Stack

- **Tool:** Microsoft Power BI Desktop
- **Language:** DAX (Data Analysis Expressions)
- **Features Used:** Model View, Report View, Enter Data (Measures Table), Matrix Visuals
- **DAX Functions:** `SUM`, `COUNT`, `CALCULATE`, `ALL`, `FILTER`, `DIVIDE`, `AVERAGEX`, `USERELATIONSHIP`, `TOTALYTD`, `DATESYTD`, `DATESINPERIOD`, `LASTDATE`, `SAMEPERIODLASTYEAR`

---

## 📁 Project Structure

```
powerbi-dax-depo/
│
├── DAX_Depo.pbix                    # Main Power BI file
├── DAX_Depo_Summary.pdf             # DAX measures summary report
├── datasets/
│   ├── Customer_Dim.xlsx            # Customer dimension table
│   ├── Date_Dim.xlsx                # Date dimension table
│   ├── Product_Dim.xlsx             # Product dimension table
│   ├── Region_Dim.xlsx              # Region dimension table
│   ├── Returns_Fact.xlsx            # Returns fact table
│   └── Sales_Fact.xlsx              # Sales fact table
├── screenshots/
│   └── report.png                   # Report preview
└── README.md                        # Project documentation
```

---

## 💡 What I Learned

- Creating a dedicated **Measures Table** to organize all DAX logic separately from data tables
- Using `CALCULATE()` with `ALL()` and `FILTER()` to **override and manipulate filter context**
- Building **conditional measures** using `AVERAGEX()` and threshold-based `FILTER()` logic
- Activating **inactive relationships** inside measures using `USERELATIONSHIP()`
- Implementing **Time Intelligence** — YTD, rolling 6-month window, and Year-over-Year growth
- Verifying all measures using **Matrix (Pivot Table)** visuals across Region, Date, Category, and Segment dimensions

---
