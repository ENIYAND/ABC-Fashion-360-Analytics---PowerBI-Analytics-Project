# 📊 ABC Fashion Inventory & Distribution Insights – Power BI Analytics Project

🔗 **Live Dashboard:** _add your published Power BI Service link here once published_

---

# 🌟 Project Overview

ABC Fashion Pvt. Ltd. is a women's apparel manufacturer distributing across Kerala through multiple distributors and retail outlets. While overall sales and production volumes have remained stable, management identified three concerns they couldn't explain from raw spreadsheets alone: rising inventory costs, underperforming products, and distributors reporting stock shortages.

This project builds an end-to-end Power BI analytics solution — from raw Excel data to a modeled star schema to a 6-page interactive dashboard — that converts those vague concerns into named, quantified, actionable findings.

The dashboard enables stakeholders to:

- Track sales performance and fulfillment health at a glance
- Identify which specific products/styles are overstocked or under-tracked
- Pinpoint which distributors and districts carry the highest unfulfilled exposure
- Understand production pipeline efficiency from planning through dispatch
- Review a directional demand forecast and inventory runway by style
- Act on a fully-specified set of business recommendations, each tied to a rupee value, an owner, and a risk

---

# 🏢 Business Problem

ABC Fashion previously had no unified way to connect its sales transactions to its production records. This created several blind spots:

- No way to tell whether a "stock shortage" reported by a distributor was a real supply gap or a demand-side mismatch
- No visibility into which specific products/styles were quietly overstocked, tying up working capital
- No structural way to separate discount effects from tax effects in reported sales figures
- No forward-looking view of inventory runway at the style level

The objective of this project was to structure and model both datasets, build the relationships needed to answer these questions rigorously, and translate the findings into decisions management can act on this week — not just charts to look at.

---

# 💻 Tech Stack

- **Power BI Desktop** – data modeling, DAX, dashboard development
- **Power Query (M)** – data cleaning, star-schema construction, dynamic date table
- **DAX** – business measures, ratio calculations, forecasting-adjacent metrics
- **Python (pandas)** – independent validation of the data model and cross-checking of key findings before they were built into DAX
- **Excel** – original source data (`Junior Data Analyst Interview Data.xlsx`)

---

# 🛠️ Power BI Features Implemented

- **Star schema data modeling** — two fact tables (`Fact_Sales`, `Fact_Production`) bridged through a shared conformed dimension (`Dim_Style`), rather than a direct fact-to-fact relationship
- **Custom M-code transformations** — derived `Style` key (`Product & "-" & Size`) built consistently across both fact tables; `Dim_Style` constructed as a **union** of both tables' style lists (not a one-sided lookup) to preserve styles present in sales but missing from production records
- **Dynamically-scoped date table** — `Dim_Date` built from `List.Min`/`List.Max` of `Fact_Sales[Invoice Date]` rather than hardcoded, so it always matches the actual data range
- **DAX measures** — explicit, reusable measures for sales, fulfillment, sell-through, pending value, and a custom net-realization variance metric (see Data Limitations below)
- **DIVIDE-based zero-protection** — all ratio measures use `DIVIDE(..., 0)` to avoid blank/error visuals under filtered slicer states
- **ALL vs. ALLSELECTED contribution measures** — two parallel "% of total" measures demonstrating the difference between a slicer-aware share and a fixed company-wide baseline
- **Drill-through** — Product & Fulfillment page drills through to distributor-level invoice detail
- **Conditional formatting** — Distributor × Product variance heat-matrix
- **Bubble map** — district-level sales and pending-value visualization
- **Native Power BI forecasting (ETS)** — applied with an explicitly stated 2–3 month horizon limitation given only 6 months of history
- **Custom icon-based page navigation rail**

---

# 📂 Dataset Overview

Source file: `Junior Data Analyst Interview Data.xlsx`

### Sales_Data (350 rows)
Invoice No, Invoice Date, Region (14 Kerala districts), Distributor (4), Product (10), Size (S/M/L/XL), Qty, Unit Price, Net Amount, Order Status (Delivered/Pending)

### Production Data (50 rows)
Factory (1: Kochi Plant), Product, Size, Color (6), Planned Qty, Produced Qty, QC Passed, Dispatched Qty — a static snapshot with **no date dimension**, meaning it cannot be reconciled month-by-month against sales, only compared as cumulative supply vs. 6-month demand.

**Style** = Product + Size, 38 unique combinations across both tables.

---

# 🗂️ Data Model

The model follows a **star schema**:

- `Fact_Sales` and `Fact_Production` each relate many-to-one to `Dim_Style`, `Dim_Distributor` (Sales only), `Dim_Region` (Sales only), and `Dim_Date` (Sales only)
- `Dim_Style` acts as the **sole bridge** between the two fact tables — there is no direct fact-to-fact relationship
- All relationships are `*:1` cardinality with **Single** cross-filter direction
- `Dim_Style` was deliberately built as a union of both fact tables' style lists, so the 10 styles present in sales but absent from production are preserved in the model rather than silently dropped

---

# 💡 Dashboard Overview

Six pages, each with a specific analytical job:

### 🏠 Executive Summary
KPI overview (Total Sales, Fulfillment Rate, Pending Value, Sell-Through Rate), an orphan-style data-quality alert, monthly revenue trend, and top-product ranking.

### 📦 Product & Fulfillment Analytics
Style-level sell-through table, a Sales-vs-Dispatched scatter plot (non-comparative measures across products), a production pipeline funnel (Planned → Produced → QC Passed → Dispatched), and drill-through to distributor detail.

### 🗺️ Territorial Distribution Analytics
Distributor pending-value ranking, a Distributor × Product net-realization variance matrix, and a district-level bubble map of sales and pending exposure.

### 📈 Demand Forecast & Inventory Outlook
A native Power BI ETS forecast on the aggregate revenue trend (explicitly capped at a short horizon given limited history), and a style-level inventory runway table (months of stock remaining at current sales velocity).

### 🎯 Strategic Initiatives & Action Plan (Parts 1 & 2)
Six fully-specified recommendations, each following a strict Finding → Decision → ₹ Value → Who Acts → Risk structure — covering untracked production, SKU overstock, distributor/district fulfillment exposure, production pipeline yield loss, a structurally non-selling SKU, and a data-schema gap in the source system itself.

---

# 🔑 Key Insights & Recommendations

1. **22.2% of total sales value** (₹10,92,345.58) is tied to 10 style combinations with no matching production record — nearly a quarter of all pending exposure sits in exactly these untracked styles.
2. **Smart Bottom-XL** shows a 3.5% sell-through rate — the most overstocked style by absolute unit volume in the portfolio.
3. **Malabar Distributors** holds the highest pending exposure (₹3,05,839.97, 29.9% of company-wide pending value); at the district level, **Kozhikode** shows the sharpest concentration at 41.6% of all sales value unfulfilled.
4. **5.15% of produced units (4,872)** never reach Dispatched status — of which 4,054 pass QC but still don't ship, a gap worth auditing rather than assuming is normal buffer stock.
5. **NovaFit Bra-S** carries the longest inventory runway in the portfolio (576 months at current velocity) — a structural non-seller distinct from Smart Bottom-XL's higher-volume overstock problem.
6. The source data has **no fields separating discount, tax, or other deductions** — every pricing variance in this project had to be modeled as a blended "Net Realization Variance," with a recommendation to add explicit Discount %, Tax %, COGS, and Pre/Post-Invoice Deduction fields at the data source (gold layer) level.

---

# ⚠️ Data Limitations & Assumptions

- **No discount/tax separation:** `Net Amount` reflects a blended adjustment to list price. Positive variance = discount-dominant (price below list); negative variance = tax/surcharge-dominant (price above list). This is stated explicitly rather than mislabeled as a clean "discount rate."
- **Production data has no time dimension:** all production-vs-sales comparisons are cumulative-supply-vs-6-month-demand, not month-by-month reconciliations.
- **Forecast horizon is intentionally short:** Power BI's native ETS forecasting needs a full annual cycle to detect seasonality (e.g. festive-season spikes). With only 6 months of history, the forecast is a directional baseline trend, not a seasonal prediction, and should not be used alone to lock next year's production planning.

---

# 🚀 Project Outcome

This project converted two disconnected operational spreadsheets (400 rows total) into a validated star-schema data model and a 6-page interactive dashboard, surfacing:

- ₹10,92,345.58 in sales tied to unverified production records
- A single SKU (Smart Bottom-XL) responsible for the most overstocked absolute inventory position in the portfolio
- ₹3,05,839.97 in distributor-level pending exposure requiring a fulfillment-capacity review
- A concrete, sourced-data recommendation for schema changes at the ERP/gold-layer level, not just a dashboard-level workaround

---

# 📬 Contact

_Add your name, LinkedIn, and email here._
