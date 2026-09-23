# powerbi-retail-analysis
Power BI report on Online Retail II dataset
# Retail Sales Analytics — Power Query & Dimensional Modelling

> **One-line summary:** _[e.g. An end-to-end Power BI solution turning 1M+ rows of messy transactional retail data into a governed star schema and an executive analytics report.]_

![Executive Summary page]executive-summary-page.png

---

## Contents
- [Business problem](#business-problem)
- [Dataset](#dataset)
- [Data quality issues found](#data-quality-issues-found)
- [Transformation approach](#transformation-approach)
- [Data model](#data-model)
- [Measures](#measures)
- [Findings](#findings)
- [Report pages](#report-pages)
- [Tools](#tools)
- [What I'd do differently](#what-id-do-differently)
- [Files in this repo](#files-in-this-repo)

---

## Business problem

_[2–4 sentences. Frame it as a stakeholder question, not a technical one.]_

_Example framing: A UK-based online giftware retailer has two years of transactional data but no reporting layer. Leadership can't answer basic questions — which products actually drive revenue, which customers are worth retaining, and how much revenue is lost to returns. The raw data is unusable as-is: it contains cancellations, missing customer IDs, and inconsistent product descriptions._

**Questions this report answers:**
1. _[e.g. What is our true net revenue after returns, and how is it trending?]_
2. _[e.g. Which customers are most valuable, and which are at risk of churning?]_
3. _[e.g. How concentrated is revenue across the product catalogue?]_

---

## Dataset

| | |
|---|---|
| **Source** | Online Retail II — UCI Machine Learning Repository |
| **Link** | https://archive.ics.uci.edu/dataset/502/online+retail+ii |
| **Period** | _[dates covered]_ |
| **Raw rows** | _[count]_ |
| **Structure** | Excel workbook, two sheets (one per year) |
| **Grain** | One row per invoice line item |

**Why this dataset:** It's deliberately messy. Real transactional data with cancellations, nulls, negative quantities and free-text product descriptions — which makes it a genuine test of transformation logic rather than a clean demo file.

---

## Data quality issues found

_[This is the section that differentiates you. Be specific and quantify everything.]_

| # | Issue | Rows affected | Decision | Rationale |
|---|---|---|---|---|
| 1 | Cancelled invoices (prefix `C`) | _[n]_ | Flagged as returns, retained | _[Needed for Return Rate; deleting would overstate revenue performance]_ |
| 2 | Null CustomerID | _[n]_ | _[Retained for revenue, excluded from customer analysis]_ | _[Guest checkouts are real revenue but can't be segmented]_ |
| 3 | Negative / zero quantities | _[n]_ | _[decision]_ | _[rationale]_ |
| 4 | Zero or negative unit price | _[n]_ | _[decision]_ | _[rationale]_ |
| 5 | Inconsistent product descriptions (casing, whitespace, control characters) | _[n]_ | Standardised upstream in Staging | _[Prevents duplicate products appearing in DimProduct]_ |
| 6 | Non-product stock codes (POSTAGE, MANUAL, etc.) | _[n]_ | _[decision]_ | _[rationale]_ |
| 7 | Duplicate rows | _[n]_ | _[decision]_ | _[rationale]_ |

**Row reconciliation:** _[X]_ raw rows → _[Y]_ rows loaded to FactSales (_[Z]_% retained).

---

## Transformation approach

All transformations were built in **Power Query (M)**. The pipeline uses a single **Staging** query as the source of truth, with dimension and fact tables built as **Reference** queries from it.

```
Sheet 2009-2010 ─┐
                 ├─► Staging (append, clean, type, enrich) ─┬─► DimProduct
Sheet 2010-2011 ─┘        [load disabled]                   ├─► DimCustomer
                                                            └─► FactSales

Blank query (M) ──────────────────────────────────────────► DimDate
```

**Key decisions:**

- **Reference over Duplicate** — Duplicate copies the entire step chain, so every cleaning change would need repeating in four places. Reference points back to Staging, so cleaning is written once and inherited downstream.
- **Clean upstream, not downstream** — Text standardisation (`Text.Upper`, `Text.Clean`, `Text.Trim`) is applied in Staging. Doing it later would mean DimProduct and FactSales could disagree on the same product key.
- **Staging load disabled** — It exists to feed other queries, not to sit in the model.
- **DimDate written in M** — No UI equivalent for generating a contiguous date spine, so this is the one place hand-written M was necessary.

![Applied Steps](screenshots/02-applied-steps.png)

_[Optional: paste the DimDate M code in a collapsed block below.]_

<details>
<summary>DimDate M code</summary>

```m
[paste your date spine code here]
```

</details>

---

## Data model

![Star schema](screenshots/03-model-view.png)

A **star schema** with one fact table and three conformed dimensions.

| Table | Type | Grain | Rows |
|---|---|---|---|
| `FactSales` | Fact | One row per invoice line | _[n]_ |
| `DimProduct` | Dimension | One row per stock code | _[n]_ |
| `DimCustomer` | Dimension | One row per customer | _[n]_ |
| `DimDate` | Dimension | One row per day | _[n]_ |

**Modelling choices:**
- All relationships one-to-many, **single filter direction** (dimension → fact) to keep filter propagation predictable and avoid ambiguity.
- `DimDate` **marked as a date table** so time intelligence functions behave correctly.
- Key columns hidden from Report view — users see attributes, not surrogate keys.
- _[Any role-playing dimensions, inactive relationships, or disconnected tables — e.g. the Top N parameter table.]_

---

## Measures

_[~30 measures organised into display folders. Show 3–4 that demonstrate range, not all of them.]_

<details>
<summary>Business logic — Net Revenue</summary>

```dax
[paste measure]
```
_Why: [e.g. Gross revenue overstates performance because returns are recorded as negative-quantity lines against the original invoice.]_
</details>

<details>
<summary>Time intelligence — Revenue YoY %</summary>

```dax
[paste measure]
```
_Why: [rationale]_
</details>

<details>
<summary>Customer segmentation — RFM Score</summary>

```dax
[paste measure]
```
_Why: [rationale]_
</details>

<details>
<summary>Pareto — Cumulative Revenue %</summary>

```dax
[paste measure]
```
_Why: [e.g. Uses a VAR + FILTER pattern to compare each product's revenue against all others within the current filter context, since ALL() on a single column returns a one-column table that removes only the product filter.]_
</details>

**Measure folders:** Base Aggregations · Business Logic · Time Intelligence · Customer Segmentation · Product Ranking · Parameters

---

## Findings

### 1. _[Headline finding]_
_[2–3 sentences with the number and the "so what".]_

### 2. Revenue is long-tail, not 80/20
The top 20 products account for only **_[12–17]_%** of total revenue — well short of the classic Pareto expectation. This isn't a data error; it reflects a wide giftware catalogue where demand is spread across thousands of low-volume SKUs rather than concentrated in a few hero products.

**Implication:** _[e.g. Range rationalisation would be risky here — cutting the tail would cut most of the revenue. Inventory strategy should optimise for breadth and low per-SKU holding cost rather than depth on bestsellers.]_

### 3. _[Finding]_
_[...]_

---

## Report pages

| Page | Purpose | Key visuals |
|---|---|---|
| **Executive Summary** | _[At-a-glance performance for leadership]_ | _[KPI cards, revenue trend, top products]_ |
| **Customer Analysis** | _[Who's valuable, who's at risk]_ | _[RFM matrix, cohort/retention, customer table]_ |
| **Data Quality** | _[Transparency on what was cleaned and why]_ | _[Row reconciliation funnel, issue breakdown, transformation log]_ |

![Customer Analysis](screenshots/04-customer-analysis.png)
![Data Quality](screenshots/05-data-quality.png)

🔗 **[View the live report](INSERT_PUBLISH_TO_WEB_LINK)** _(or: see the walkthrough video below)_

---

## Tools

Power BI Desktop · Power Query (M) · DAX · Excel

---

## What I'd do differently

_[Be honest here. It reads as maturity, not weakness. 2–3 bullets.]_

- _[e.g. Product categorisation via keyword rules on Description would enable category-level analysis — scoped out here for time, but it's the first thing I'd add.]_
- _[e.g. Incremental refresh would be needed if this were fed from a live source rather than a static extract.]_
- _[e.g. Row-level security if the report were shared across regional teams.]_

---

## Files in this repo

```
├── README.md
├── retail-sales-analytics.pbix
├── /screenshots
│   ├── 01-executive-summary.png
│   ├── 02-applied-steps.png
│   ├── 03-model-view.png
│   ├── 04-customer-analysis.png
│   └── 05-data-quality.png
└── /data
    └── source.md          # link to original dataset
```

---

_[Your name] · [LinkedIn] · [Email]_
