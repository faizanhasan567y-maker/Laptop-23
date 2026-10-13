# 💻 Flipkart Laptop Purchase Behavior at Scale

> **Best-Sellers • Sales Trends • Ratings • Regional Demand • Inventory**
>
> A descriptive analytics report on daily laptop purchase behavior, built on a synthetic dataset that mirrors real e-commerce patterns.

![Type](https://img.shields.io/badge/Type-Descriptive%20Analytics-blue)
![Domain](https://img.shields.io/badge/Domain-E--commerce-orange)
![Language](https://img.shields.io/badge/Python-pandas%20%7C%20Matplotlib-3776AB?logo=python&logoColor=white)
![Data](https://img.shields.io/badge/Data-Synthetic-lightgrey)
![Purpose](https://img.shields.io/badge/Purpose-Academic%20%2F%20Demonstration-green)

**Author:** Mohd Faizan Hasan · **Roll No:** 30 · **Date:** August 2026

---

## ⚠️ Important Note on Data

Flipkart's live transaction data is **not publicly accessible**. This project therefore uses a **synthetic dataset** designed to mirror real e-commerce behavior. **None of the figures in this repository are actual Flipkart sales data.** They illustrate what a descriptive-analytics pipeline would produce, and the same structure applies once real data (API, database export, or CSV) is substituted in.

---

## 📑 Table of Contents

1. [Overview](#-overview)
2. [Objectives](#-objectives)
3. [Dataset & Assumptions](#-dataset--assumptions)
4. [Pipeline Architecture](#-pipeline-architecture)
5. [Key Metrics at a Glance](#-key-metrics-at-a-glance)
6. [Analysis & Findings](#-analysis--findings)
7. [Key Insights](#-key-insights)
8. [Additional Insights](#-additional-insights)
9. [How Each Insight Was Derived](#-how-each-insight-was-derived)
10. [Data Notes & Limitations](#-data-notes--limitations)
11. [Roadmap: Descriptive → Predictive](#-roadmap-descriptive--predictive)
12. [Raw Data Schema](#-raw-data-schema)
13. [Glossary](#-glossary)
14. [Repository Structure](#-repository-structure)
15. [Author](#-author)

---

## 🔎 Overview

This report builds a **descriptive analytics layer** over daily laptop purchase transactions on Flipkart. A single layer, refreshed daily, serves several teams from **one consistent source of truth** instead of separate ad-hoc reports:

| Stakeholder | What they need |
| --- | --- |
| 🛍️ Merchandising | Best-selling laptops |
| 💰 Finance | Revenue and sales trend |
| 🎧 Customer Experience | Rating quality |
| 🚚 Operations | Regional demand |
| 📦 Supply Chain | Inventory / stock-out risk |

---

## 🎯 Objectives

The report answers five recurring business questions:

1. **What are today's best-selling laptops?**
2. **How are total sales trending day over day?**
3. **How are laptops rated, and where is rating quality slipping?**
4. **Where is laptop demand concentrated geographically?**
5. **Which laptop SKUs are at risk of stocking out?**

---

## 🧪 Dataset & Assumptions

Because live data is unavailable, a synthetic dataset was generated to follow realistic laptop e-commerce patterns:

| Parameter | Value |
| --- | --- |
| **SKUs** | 45 laptop models |
| **Categories** | 6 (Budget & Everyday, Business & Productivity, Gaming, Premium Ultrabooks, 2-in-1 Convertibles, Student/Chromebooks) |
| **Regions** | 8 Indian metros (Bengaluru, Delhi NCR, Mumbai, Hyderabad, Pune, Chennai, Kolkata, Ahmedabad) |
| **Time window** | 30 days |
| **Simulated volume** | ~1,56,257 units |
| **Weekly seasonality** | Weekend demand ≈ **28% higher** than weekday |
| **Growth trend** | **+22%** across the 30-day window |
| **Ratings** | Centered at **4.1 / 5**, spread across five rating buckets (not uniform) |
| **Inventory tiers** | Most SKUs in a healthy buffer; a small minority Low Stock or Out of Stock |
| **Regional demand** | Baseline share per region + random daily noise |

---

## 🏗️ Pipeline Architecture

Processing large-scale purchase events is an engineering problem before it is an analytics one. The pipeline collects order events, aggregates them by SKU / region / time, stores raw and processed data, and exposes KPIs through a visualization layer.

| Layer | Purpose | Tools |
| --- | --- | --- |
| **Investigation** | Research, understand and reconstruct the transaction dataset | Google, Claude, ChatGPT |
| **Extraction** | Recover row-level records from the exported source file | Python (`pdfplumber`, `regex`) |
| **Storage** | Retain raw and cleaned data | CSV / Excel |
| **Batch Aggregation** | SKU, region and behavioral KPIs | Python (`pandas`) |
| **Report** | Analysis, graphs and insights | Matplotlib / MS Word |

```
Order events ─► Extraction ─► Storage (CSV/Excel) ─► Aggregation (pandas) ─► Charts & Report
```

> The report reflects **daily aggregated tables**, not raw event-level data. No dashboard renders millions of individual rows usefully.

---

## 📊 Key Metrics at a Glance

| Metric | Value |
| --- | --- |
| Total units sold (30 days) | **1,56,257** |
| Total revenue (30 days) | **₹812.5 Cr** |
| Average Selling Price (ASP) | **₹51,998 / unit** |
| Average daily units sold | ~5,209 |
| Average product rating | **4.1 / 5** |
| Top single-day seller | 612 units (Lenovo IdeaPad Slim 3) |
| Hero SKU share of top day's volume | ~11.7% |
| Weekend demand vs weekday | +28% |
| Growth over the 30-day window | +22% |
| Top 2 metros' share of demand | ~35.5% |
| Metro regions tracked | 8 |
| Laptop categories tracked | 6 |
| Catalog size | 45 SKUs |
| Peak single-day revenue | ~₹33.6 Cr |
| Lowest single-day revenue | ~₹21.3 Cr |
| Core rating range | 3.5 – 5.0 |
| In-Stock SKUs | 38 of 45 (~84.4%) |
| SKUs at Low Stock or below | 7 of 45 (~15.6%) |
| Long-tail (#2–#10) combined volume | 2,510 units |

<p align="center">
  <img src="images/09_total_units_revenue.png" width="48%" alt="Total units sold and total revenue">
  <img src="images/11_asp_and_rating.png" width="48%" alt="Average selling price and average rating">
</p>

---

## 📈 Analysis & Findings

### 1. Daily Sales Trend

Over the 30-day window the dataset produces ~1,56,257 units and ~₹812.5 Cr in revenue, with a **weekly seasonal cycle** layered on a **+22% growth trend**.

**KPI definitions**
- **Daily units sold** = sum of quantity across all completed orders that day
- **Daily revenue** = sum of (unit price × quantity), net of the aggregation layer (returns/cancellations net out separately)

![Daily Laptop Sales Trend](images/01_daily_sales_trend.png)

The recurring weekly peak (roughly every 7 days) is the clearest signal, and successive peaks trend upward. In production this would inform **staffing rosters, warehouse pick-pack scheduling and paid-marketing pacing**. Pushing promotional spend into an already-strong weekend adds less incremental lift than smoothing demand into the midweek trough.

![Cumulative Revenue](images/02_cumulative_revenue.png)

The running total climbs steadily to ~₹812.5 Cr by day 30, which shows growth is **broad-based** rather than driven by one or two outlier days.

![Daily Activity Snapshot](images/10_daily_activity.png)

### 2. Best-Selling Laptops

**Lenovo IdeaPad Slim 3** led the most recent day with **612 units**, nearly **50% ahead** of the runner-up (HP 15s Ryzen 5, 410 units). This is a classic **hero SKU** pattern, where a few listings drive a disproportionate share of volume (a Pareto effect).

| Rank | Model | Units (most recent day) |
| --- | --- | --- |
| 1 | Lenovo IdeaPad Slim 3 | 612 |
| 2 | HP 15s Ryzen 5 | 410 |
| 3 | ASUS VivoBook 15 | 372 |
| 4 | Acer Aspire 7 | 338 |
| 5 | Dell Inspiron 15 | 305 |
| 6 | Redmi Book Pro | 274 |
| 7 | Samsung Galaxy Book3 | 246 |
| 8 | MSI Katana Gaming | 214 |
| 9 | Apple MacBook Air M2 | 189 |
| 10 | Infinix INBook Y1 | 162 |

![Top 10 Laptops](images/03_top10_laptops.png)

**Ranking logic:** by *units sold*, not revenue. A budget laptop outselling a premium model in volume matters differently to warehousing than to finance, so a real dashboard should offer **both** leaderboard views.

The #1 → #10 drop-off is steady rather than a cliff, which suggests healthy demand across the catalog. Still, the #1 SKU alone is ~11.7% of that day's volume and deserves its own **reorder and quality-monitoring workflow**.

![Hero SKU vs Long Tail](images/14_hero_vs_long_tail.png)

### 3. Category-Wise Sales Breakdown

**Budget & Everyday** and **Business & Productivity** lead, together making up roughly half of total units. Gaming, Premium Ultrabooks, 2-in-1 Convertibles and Student/Chromebooks form the rest.

| Category | Units (30 days) |
| --- | --- |
| Budget & Everyday | 47.6K |
| Business & Productivity | 33.7K |
| Gaming Laptops | 25.6K |
| Premium Ultrabooks | 19.6K |
| 2-in-1 Convertibles | 16.0K |
| Student / Chromebooks | 13.8K |

![Category Breakdown](images/04_category_breakdown.png)

A category can be strong without a single standout hero SKU, which matters for **category-level marketing and buying** versus SKU-level inventory decisions.

### 4. Rating Distribution

**Average rating: 4.1 / 5** across 1,178 product-day ratings. About **88%** (1,040) fall at 3.5 or above; the largest bucket is **4.0–4.5**.

| Bucket | Product-days |
| --- | --- |
| < 3.0 | 42 |
| 3.0 – 3.5 | 96 |
| 3.5 – 4.0 | 268 |
| 4.0 – 4.5 | 512 |
| 4.5 – 5.0 | 260 |

![Rating Distribution](images/05_rating_distribution.png)

**Flagged SKUs**
- **Acer Aspire 7** and **Infinix INBook Y1** show a downward-trending rolling 7-day average, an early signal of a quality or fulfillment issue ahead of any spike in returns.
- **MSI Katana Gaming** sells at high volume but sits at **3.8 / 5**, the highest-risk combination because volume amplifies the damage of poor reviews.

A tight distribution is good news, but it leaves little room to absorb a real quality problem. A cluster of 1–2★ ratings on one high-volume SKU would pull the aggregate down quickly, so **segment-level tracking matters more than the aggregate** as volume grows.

![Rating vs Sales Volume](images/06_rating_vs_sales.png)

The trend line is only mildly downward-sloping, so high-volume SKUs are not, on the whole, rated meaningfully worse. The few high-volume, sub-4.0 points on the right (such as MSI Katana Gaming) are the first to watch.

### 5. Regional Demand

Demand is fairly balanced across the top metros, with **Bengaluru (~27.5K)** and **Delhi NCR (~25.8K)** leading.

| Region | Units (30 days) |
| --- | --- |
| Bengaluru | 27.5K |
| Delhi NCR | 25.8K |
| Mumbai | 22.1K |
| Hyderabad | 19.4K |
| Pune | 17.2K |
| Chennai | 15.6K |
| Kolkata | 12.8K |
| Ahmedabad | 9.6K |

![Regional Demand](images/07_regional_demand.png)

This view drives:
- Regional warehouse / fulfillment-center allocation
- Localized pricing and promotion decisions
- Shipping SLA risk where local inventory is limited

Even in this fairly even simulation, the top 2 metros hold over a third of demand. Real-world concentration would likely be higher, so **metro-level fulfillment capacity, not national capacity, is the binding constraint**.

### 6. Inventory Status

**45 SKUs tracked, 7 need attention.**

| Tier | Meaning | Count | Action |
| --- | --- | --- | --- |
| 🟢 In Stock | Comfortable buffer | 38 | None |
| 🟡 Low Stock (< 200 units) | Reorder trigger zone | 5 | Redmi Book Pro + 4 other SKUs |
| 🔴 Out of Stock | Lost-sale risk | 2 | **Samsung Galaxy Book3, urgent restock** |

![Inventory Status](images/08_inventory_status.png)

**Samsung Galaxy Book3**, a top-10 seller, is Out of Stock. That is a far bigger problem than a slow mover doing the same. The most actionable version of this view cross-tabs stock status against sales velocity to produce a **"days of supply remaining"** metric per SKU instead of a static count.

### 7. Summary Indicators

![Percentage-Based Indicators](images/12_percentage_indicators.png)

![Additional Key Metrics](images/13_additional_key_metrics.png)

---

## 💡 Key Insights

1. **Hero SKU dependency.** Lenovo IdeaPad Slim 3 sold ~612 units in a day, nearly 50% ahead of #2 and ~12% of that day's volume. A stock-out would materially dent daily revenue, so it belongs at the top of the inventory-priority list.
2. **Weekend-driven demand cycle.** Weekend units run ~28% above weekday, on top of a +22% growth trend. Staffing, delivery-slot capacity and reorder timing should follow this weekly rhythm rather than a flat daily average.
3. **Regional concentration risk.** Bengaluru and Delhi NCR together account for ~35.5% of demand. Fulfillment capacity and SLAs there carry outsized impact and warrant a contingency plan.
4. **Rating quality is healthy but has a visible tail.** The 4.1 average hides that ~12% of product-days fall below 3.5. Acer Aspire 7, Infinix INBook Y1 and MSI Katana Gaming should be the first stop for quality review.
5. **At-risk inventory matters disproportionately.** Only 7 of 45 SKUs (~15.6%) are Low or Out of Stock, but Samsung Galaxy Book3, a top-10 seller, is among them. Always read the best-seller list and the inventory list together.
6. **Revenue tracks volume closely.** Units and revenue move almost in lockstep, with a stable ASP of ~₹51,998, so pricing is stable and revenue is not propped up by a few high-price outliers. Any future divergence would flag shifting discounts or category mix.
7. **The catalog isn't overly reliant on the hero SKU.** SKUs #2–#10 together sell 2,510 units versus the hero's 612 (over 4×), a healthier mix worth preserving as the catalog grows.

---

## 🧭 Additional Insights

![Additional Insights by Theme](images/15_additional_insights.png)

| # | Theme | Insight |
| --- | --- | --- |
| 1 | Best-sellers | Top 10 spans at least four categories (Budget, Business, Gaming, Premium Ultrabooks), so demand is broad-based. |
| 2 | Regional | Even the smallest metros, Kolkata (~12.8K) and Ahmedabad (~9.6K), each move more volume in 30 days than the top SKU sells in a day. Don't skip them in expansion planning. |
| 3 | Ratings | The 4.1 average held across ~1.56 lakh units, so quality did not visibly erode as volume scaled. Keep monitoring as volume rises. |
| 4 | Inventory | Use a **tiered reorder cadence**: daily checks for hero and at-risk SKUs, monthly for the stable majority. |
| 5 | Sales trends | A ~22% growth trend sits beneath the weekly cycle. Isolate it with trend decomposition instead of attributing all movement to weekends. |
| 6 | Best-sellers | Revenue concentration likely mirrors volume concentration (stable ASP), so a pricing misstep on the hero SKU would hit the P&L harder than its single-SKU status suggests. |
| 7 | Sales trends | The weekend cycle likely holds across categories and metros, so replenishment, staffing and delivery slots are best synchronized to one shared weekly rhythm. |

---

## 🧮 How Each Insight Was Derived

| Insight | Calculation / Basis |
| --- | --- |
| Hero SKU share | 612 ÷ 5,209 ≈ **11.7%** |
| Lead over #2 | (612 − 410) ÷ 410 ≈ **49%** |
| Weekend & growth | +28% and +22% from the KPI set, confirmed visually in the Daily Sales Trend |
| Top-2 metro share | (27,500 + 25,800) ÷ 1,50,000 ≈ **35.5%** |
| Rating tail | (42 + 96) ÷ 1,178 ≈ **11.7%** (~12%) below 3.5 |
| Inventory risk | 7 ÷ 45 ≈ **15.6%** |
| ASP | ₹812.5 Cr ÷ 1,56,257 ≈ **₹51,998** |
| Long-tail volume | 410 + 372 + 338 + 305 + 274 + 246 + 214 + 189 + 162 = **2,510** |

**Qualitative / inferred items**
- *Revenue–volume correlation:* observed by comparing the shape of the two lines in the trend chart.
- *Revenue concentration mirroring volume:* an **inference** from stable ASP. Revenue per SKU is not broken out in the data.
- *Metro-wide cadence:* an **extrapolation** from the aggregate weekly cycle. Data is not split by category-by-day or metro-by-day, so treat it as a hypothesis to test on real data.
- *Tiered reorder cadence:* a suggested operating practice, not a dataset figure.

---

## 📝 Data Notes & Limitations

- **Synthetic data only.** Use for demonstration and learning, not business decisions.
- **Regional totals vs. overall units.** The eight regional figures sum to ~1,50,000, while total units are 1,56,257. The top-2 share (~35.5%) is computed on the regional sum. Reconcile the two when plugging in real data.
- **Hero SKU share.** The 11.7% compares one day's units (612) to the *average* daily volume (5,209), not that specific day's total.
- **Stock-out count.** The report text highlights Samsung Galaxy Book3 as Out of Stock; the inventory chart shows **2 Out of Stock + 5 Low Stock = 7** at-risk SKUs.
- **Revenue per SKU** is not available, so revenue-concentration findings are inferences.
- **Single-day leaderboard.** Best-seller rankings use the most recent day only. Use a rolling window for stable merchandising decisions.

---

## 🚀 Roadmap: Descriptive → Predictive

This report answers *what happened*. Natural next layers:

- [ ] **Demand forecasting** per SKU / region to set reorder points automatically before a hero SKU goes out of stock
- [ ] **Anomaly detection** on rating drops or sudden demand spikes, catching issues in hours, not at month-end
- [ ] **Cohort & retention analysis** of repeat-purchase and upgrade-cycle behavior, not just unit counts
- [ ] **Days-of-supply** metric per SKU (stock ÷ average daily sales)
- [ ] **Trend decomposition** to separate weekly seasonality from underlying growth
- [ ] **Live data integration** via API, DB export or CSV. The same pipeline and chart structure applies directly.

---

## 🗂️ Raw Data Schema

Each raw transaction event feeding the pipeline carries, at minimum:

| Group | Fields |
| --- | --- |
| Identifiers | `order_id`, `sku_id`, `category`, `region` |
| Transaction facts | `unit_price`, `quantity`, `order_timestamp` |
| Quality signal (nullable) | `rating`, `rating_timestamp` |
| Inventory state | `stock_on_hand`, `reorder_threshold` |

---

## 📖 Glossary

| Term | Meaning |
| --- | --- |
| **ASP** | Average Selling Price: total revenue ÷ total units sold over a period |
| **Hero SKU** | A single model responsible for a disproportionately large share of daily volume |
| **Long tail** | Lower-ranked SKUs or metros that are individually small but collectively significant |
| **SLA** | Service-Level Agreement: a committed standard for delivery time or fulfillment |
| **Days of supply** | Stock on hand ÷ average daily sales rate, i.e. days until a SKU runs out |
| **Pareto effect** | A small share of items driving a large share of results |

---

## 📁 Repository Structure

```
.
├── README.md
├── Flipkart_Laptop_Purchase_Behavior_Report.docx   # Full report
└── images/
    ├── 01_daily_sales_trend.png
    ├── 02_cumulative_revenue.png
    ├── 03_top10_laptops.png
    ├── 04_category_breakdown.png
    ├── 05_rating_distribution.png
    ├── 06_rating_vs_sales.png
    ├── 07_regional_demand.png
    ├── 08_inventory_status.png
    ├── 09_total_units_revenue.png
    ├── 10_daily_activity.png
    ├── 11_asp_and_rating.png
    ├── 12_percentage_indicators.png
    ├── 13_additional_key_metrics.png
    ├── 14_hero_vs_long_tail.png
    └── 15_additional_insights.png
```

---

## 👤 Author

**Mohd Faizan Hasan**
Roll No: 30
*Descriptive Analytics Report: Flipkart Laptop Category (August 2026)*

---

## 📜 Disclaimer

This project is prepared for **academic / demonstration purposes only**. All data is synthetic and is not affiliated with, endorsed by, or representative of Flipkart. Flipkart is a trademark of its respective owner.
# 📊 Business Intelligence Reporting Practicals

A collection of hands-on reporting and data visualization practicals covering dashboard design, list reports, cross-filtering, and chart creation using sales data. Each practical is exported as a PDF report and documented below.

---

## 📑 Table of Contents

1. [Overview](#-overview)
2. [Repository Structure](#-repository-structure)
3. [Tools & Technologies](#-tools--technologies)
4. [Dataset Description](#-dataset-description)
5. [Practical 1 – Pie Chart Dashboard with Filter Panels](#-practical-1--pie-chart-dashboard-with-filter-panels)
6. [Practical 2 – List Reports with Clustered Column Chart](#-practical-2--list-reports-with-clustered-column-chart)
7. [Practical 3 – Filtered Subset Reports](#-practical-3--filtered-subset-reports)
8. [Practical 4](#-practical-4)
9. [Key Observations & Insights](#-key-observations--insights)
10. [Known Issues & Possible Improvements](#-known-issues--possible-improvements)
11. [How to View the Reports](#-how-to-view-the-reports)
12. [Learning Outcomes](#-learning-outcomes)
13. [Author](#-author)
14. [License](#-license)

---

## 🔎 Overview

This repository contains four reporting practicals built around retail sales data. The practicals progress from a single visual-heavy dashboard to multi-component reports that combine tabular data with charts, and then to filtered subsets of those reports.

Skills demonstrated:

- Building dashboards with themed layouts (headers, side panels, footers)
- Creating pie charts and clustered column (bar) charts
- Designing list/table reports with formatted currency columns
- Using categorical filters (Region, Payment Method, Product Category)
- Adding report metadata such as date, page number and time stamps

---

## 📁 Repository Structure

```
.
├── Prac_1.pdf      # Pie chart dashboard with four filter-panel pages
├── PRAC_2.pdf      # Full list reports + clustered column chart
├── PRAC_3.pdf      # Filtered subset of the Practical 2 reports
├── PRAC_4.pdf      # Practical 4 report
└── README.md       # Project documentation
```

---

## 🛠 Tools & Technologies

| Category            | Details                                              |
|---------------------|------------------------------------------------------|
| Reporting tool      | IBM Cognos Analytics (report/dashboard authoring)    |
| Output format       | PDF export                                           |
| Visualizations      | Pie chart, clustered column chart, list tables       |
| Data currency       | Indian Rupee (₹) for Practicals 2 and 3              |
| Report generated on | 8–9 October 2026                                     |

> Update this table if you used a different tool.

---

## 🗂 Dataset Description

The practicals use sales transaction data with the following fields.

### Practical 1 fields

| Field          | Description                                          | Example Values                               |
|----------------|------------------------------------------------------|----------------------------------------------|
| Date           | Transaction date (Jan–Mar 2024)                      | 1 Jan 2024, 3 Jan 2024, 2 Mar 2024           |
| Region         | Geographic region of the sale                        | North America, Europe, Asia                  |
| Payment Method | How the customer paid                                | Credit Card, PayPal, Debit Card              |
| Units Sold     | Number of units in a transaction                     | 180, 105, 145, 88                            |
| Unit Price     | Price per unit                                       | 353.87225, 190.90425, 67.5365, 261.284       |
| Sales value    | Value plotted on the pie chart                       | 3,899.99, 2,399, 1,895, 1,599.99, 1,499.98   |

### Practical 2 and 3 fields

| Field            | Description                            | Example Values                                  |
|------------------|----------------------------------------|-------------------------------------------------|
| Sales Amount     | Transaction value in Indian Rupees (₹) | ₹18,628.00, ₹22,793.00, ₹47,380.00              |
| Product Category | Category of the product sold           | Electronics, Furniture, Grocery, Clothing       |
| Store Name       | Individual store identifier            | East Store A, North Store B, South Store A      |
| Region           | Store region                           | North, South, East, West                        |
| Quantity         | Units sold (used in the chart)         | See chart data below                            |

---

## 🥧 Practical 1 – Pie Chart Dashboard with Filter Panels

**File:** `Prac_1.pdf` (4 pages)

### Objective
Design a themed dashboard that visualizes sales across dates using a pie chart, with a side panel listing a different dimension or measure on each page.

### Layout
- **Theme:** Light green background with dark green header blocks and a bright green top accent bar
- **Left panel:** Dark green banner area with a list/filter table below it
- **Main panel:** Dark green title banner with a pie chart beneath it
- **Footer:** Report date (9 Oct 2026), page number, and time stamp (00:38:04)

### Pages

| Page | Left Panel Column | Values Shown                                      |
|------|-------------------|---------------------------------------------------|
| 1    | Region            | North America, Europe, Asia, Asia                 |
| 2    | Payment Method    | Credit Card, PayPal, Debit Card, Credit Card      |
| 3    | Units Sold        | 180, 105, 145, 88                                 |
| 4    | Unit Price        | 353.87225, 190.90425, 67.5365, 261.284            |

### Chart details
- **Type:** Pie chart
- **Category (legend):** Date, running from 1 Jan 2024 to 2 Mar 2024. The legend is truncated with "..." because there are so many dates.
- **Data labels:** The largest slices are labelled 3,899.99, 2,399, 1,895, 1,599.99 and 1,499.98.

### Notes
- The pie chart is identical on all four pages, so the left-panel lists are not filtering the chart.
- With roughly 60 or more date slices, small slices are hard to read.

---

## 📈 Practical 2 – List Reports with Clustered Column Chart

**File:** `PRAC_2.pdf` (2 pages)

### Objective
Combine list (table) reports with a clustered column chart showing quantity sold by region and product category.

### Page 1 – Sales Amount by Product Category

| Sales Amount | Product Category |
|--------------|------------------|
| ₹18,628.00   | Electronics      |
| ₹22,793.00   | Furniture        |
| ₹15,345.00   | Grocery          |
| ₹24,411.00   | Clothing         |
| ₹47,380.00   | Furniture        |
| ₹10,117.00   | Clothing         |
| ₹11,126.00   | Furniture        |
| ₹7,814.00    | Clothing         |
| ₹17,955.00   | Grocery          |
| ₹26,249.00   | Furniture        |
| ₹29,305.00   | Electronics      |
| ₹8,647.00    | Clothing         |
| ₹28,756.00   | Electronics      |
| ₹5,032.00    | Clothing         |
| ₹17,070.00   | Electronics      |
| ₹26,223.00   | Furniture        |
| ₹32,282.00   | Grocery          |
| ₹15,066.00   | Clothing         |
| ₹ (continues on next page) | Electronics |

### Page 2 – Stores by Region

| Store Name    | Region |
|---------------|--------|
| East Store A  | East   |
| East Store B  | East   |
| East Store C  | East   |
| North Store A | North  |
| North Store B | North  |
| North Store C | North  |
| South Store A | South  |

### Chart – Quantity by Region and Product Category

- **Type:** Clustered column chart
- **X-axis:** Region (North, South, West, East)
- **Y-axis:** Quantity (0–160)
- **Series:** Product Category (Furniture, Clothing, Grocery, Electronics)

Approximate values read from the chart:

| Region | Furniture | Clothing | Grocery | Electronics |
|--------|-----------|----------|---------|-------------|
| North  | ~61       | ~50      | ~32     | ~70         |
| South  | ~6        | ~92      | ~46     | ~141        |
| West   | ~28       | ~44      | ~45     | ~36         |
| East   | ~65       | ~6       | ~9      | ~24         |

Report generated: **08-Oct-26, 4:09:15 AM**

---

## 🔍 Practical 3 – Filtered Subset Reports

**File:** `PRAC_3.pdf` (2 pages)

### Objective
Reproduce the Practical 2 report with the list tables limited to a subset of rows, while the chart stays the same.

### Page 1 – Sales Amount by Product Category (subset)

| Sales Amount | Product Category |
|--------------|------------------|
| ₹7,814.00    | Clothing         |
| ₹17,955.00   | Grocery          |
| ₹26,249.00   | Furniture        |
| ₹29,305.00   | Electronics      |
| ₹8,647.00    | Clothing         |
| ₹28,756.00   | Electronics      |

### Page 2 – Stores by Region (subset)

| Store Name    | Region |
|---------------|--------|
| North Store A | North  |
| North Store B | North  |

### Chart
Same clustered column chart as Practical 2 (Quantity by Region, grouped by Product Category).

Report generated: **08-Oct-26, 4:12:04 AM**

> The six sales rows are a contiguous slice of the Practical 2 list. Document the exact filter or condition you applied here (for example a rank filter, range filter, or prompt).

---

## 🧪 Practical 4

**File:** `PRAC_4.pdf`

> ⚠️ **TODO:** Add the objective, steps, and output description for Practical 4.

Suggested sections:
- Objective
- Data fields used
- Visualizations / report components
- Key observations

---

## 💡 Key Observations & Insights

From the Practical 2 and 3 chart:

1. **Electronics in the South is the top performer**, at roughly 141 units, well ahead of every other region and category.
2. **The South is highly imbalanced.** Furniture sells only about 6 units there, against about 92 for Clothing.
3. **The East favors Furniture** (about 65 units), while Clothing and Grocery are very weak there (about 6 and 9).
4. **The West is the most balanced region**, with every category between about 28 and 45 units.
5. **The North is led by Electronics** (about 70), followed by Furniture (about 61).

From the Practical 2 sales table:

- The highest single sale shown is **₹47,380 (Furniture)**.
- The lowest shown is **₹5,032 (Clothing)**.
- Furniture and Electronics tend to have higher transaction values than Clothing and Grocery.

---

## ⚠️ Known Issues & Possible Improvements

| Area        | Issue                                                                 | Suggested Fix                                                         |
|-------------|-----------------------------------------------------------------------|-----------------------------------------------------------------------|
| Practical 1 | Pie chart has too many slices to read                                 | Use a bar or line chart for time series, or group dates by month      |
| Practical 1 | Chart does not change between pages                                   | Connect the left-panel filters to the chart                           |
| Practical 1 | Region list shows "Asia" twice                                        | Apply distinct values or aggregation                                  |
| Practical 1 | Dark green banners are empty                                          | Add report titles and descriptive text                                |
| Practical 2 | Rupee symbol wraps to a separate line in narrow columns               | Widen the Sales Amount column                                         |
| Practical 2 | Table cuts off at the page end (last row's amount is on the next page)| Adjust pagination or row heights                                      |
| Practical 2 | Store table has no relation to the chart or sales table               | Join stores and sales into one query                                  |
| Practical 3 | Filter logic is not documented                                        | Add the filter criteria as a report subtitle                          |
| All         | Tables and charts lack titles                                         | Add titles, axis labels and units                                     |

---

## ▶️ How to View the Reports

1. Clone or download this repository:
```bash
   git clone https://github.com/<your-username>/<your-repo-name>.git
   cd <your-repo-name>
```
2. Open any PDF in a PDF viewer, or click the file in GitHub to preview it in the browser.
3. To regenerate the reports, open the source in your reporting tool, connect the data source, and re-run and export to PDF.

---

## 🎓 Learning Outcomes

After completing these practicals you should be able to:

- Create and style multi-panel dashboards
- Choose the right chart for the data, and recognize when a chart is a poor fit
- Build list reports with currency formatting
- Build clustered column charts with a categorical series
- Apply filters to produce subset reports
- Export and distribute reports with metadata footers

---

## 👤 Author

**Name:** `<Your Name>`
**Course / Institution:** `<Course Name, Institution>`
**Contact:** `<Email or GitHub profile>`

---

## 📄 License

This project is for educational purposes. Add a license (for example MIT) if you want to allow reuse.
