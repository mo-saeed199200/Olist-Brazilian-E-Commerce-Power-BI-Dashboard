# 🛒 Olist Brazilian E-Commerce — Power BI Dashboard

## Project Overview

This project analyzes the **Olist Brazilian E-Commerce** public dataset from Kaggle, covering over **99,000 orders** placed between **2016 and 2018**. The goal is to uncover actionable business insights across sales performance, customer satisfaction, logistics, and seller behavior using Power BI.

---

## 📁 Dataset

- **Source:** [Kaggle — Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
- **Tables used:** 9 tables including orders, customers, sellers, products, payments, reviews, and geolocation
- **Time period:** October 2016 – August 2018

---

## ⚠️ Data Limitations

Before diving into the insights, it's important to acknowledge dataset limitations that shape what this analysis can and cannot claim:

1. **No COGS (Cost of Goods Sold):** The dataset has no product cost data. "Gross Profit" in this dashboard means **Revenue minus Freight Cost only** — not true gross profit. Net Profit cannot be calculated.
2. **Customer ID resets per order:** Olist assigns a new `customer_id` for every order, even for the same person. Repeat-purchase behavior and true customer lifetime value **cannot be accurately tracked**.
3. **Dataset ends mid-2018:** Orders drop sharply after August 2018 — this is **data truncation**, not a real business downturn.
4. **Geolocation duplicates:** The geolocation table had multiple lat/lng rows per zip code, requiring a split into separate customer-geo and seller-geo tables to avoid Many-to-Many relationship errors.
5. **Product categories are in Portuguese** throughout the dashboard.
6. **Small-sample states:** Several states (RR, AP, AC, AL) have very low order counts (45–400 orders). Any insight involving them should be treated as **lower-confidence** — a handful of orders can swing an average significantly.
7. **Some metrics below are derived**, not pulled directly from a single visual (e.g., AOV per state, gross margin per state, orders-per-seller). These are calculated by combining two confirmed dashboard numbers (e.g., Revenue ÷ Orders) and are labeled as such.

---

## 📊 Dashboard Pages

| Page | Description |
|---|---|
| **Home** | Navigation landing page |
| **Overview** | High-level KPIs and revenue trend |
| **Revenue** | Sales by quarter, category, and payment method |
| **Customer** | Satisfaction scores, delivery impact, and state performance |
| **Sellers** | Top and bottom sellers by revenue and orders |
| **Map** | Geographic distribution of orders and performance metrics |

---

## 🔑 Headline KPIs

| Metric | Value |
|---|---|
| Total Revenue | **$13.6M** |
| Total Orders | **99K** |
| Total Customers | **96K** |
| Average Order Value (AOV) | **$136.7** |
| Gross Profit (Revenue − Freight) | **$11.34M** |
| Gross Profit Margin | **83.43%** |
| Average Delivery Time | **12.5 days** |
| On-Time Delivery Rate | **92.07%** |
| Delayed Orders | **7.93%** |
| Late Shipment Rate (seller-side) | **14.62%** |
| Average Review Score | **4.0 / 5** |
| Positive Reviews (4–5★) | **85.37%** |
| Active Sellers | **~3,000** |

---

## 💡 Deep-Dive Insights

### 1. Revenue is heavily concentrated — both geographically and by seller

| Level | Concentration |
|---|---|
| São Paulo (SP) alone | **37%** of total revenue ($5.03M) |
| Top 3 states (SP + RJ + MG) | **61%** of total revenue |
| Top 10 sellers (out of 3,000+) | **~13%** of total revenue (~$1.79M) |

**So what:** Fewer than 0.35% of sellers generate 13% of platform revenue. This is a concentration risk — losing a handful of top sellers or underperformance in SP would have an outsized impact on total revenue.

---

### 2. Delivery time is the strongest predictor of review score — at the order level

| Review Score | Avg. Delivery Time |
|---|---|
| ⭐ 1 | 21.29 days |
| ⭐ 2 | 16.60 days |
| ⭐ 3 | 14.20 days |
| ⭐ 4 | 12.25 days |
| ⭐ 5 | 10.61 days |

**So what:** Customers who leave a 1-star review wait roughly **2x longer** than 5-star reviewers. This is the cleanest causal-looking relationship in the entire dataset and should anchor any delivery-improvement business case.

---

### 3. But at the state level, the same delivery time produces opposite satisfaction outcomes

| State | Delivery Time | Review Score |
|---|---|---|
| AP | 27 days | **4.20** (highest) |
| RR | 29.85 days | **3.58** (lowest) |

**So what:** AP and RR have almost identical (long) delivery times, yet opposite satisfaction results. This means delivery time alone doesn't fully explain satisfaction at a regional level — local factors (customer expectations, order volume, sample size) are also at play. **This remains an open question** rather than a fully explained pattern — flagging it honestly is more valuable than forcing a tidy explanation the data doesn't support.

---

### 4. RR isn't failing on multiple fronts — it's one root cause showing up everywhere

| Metric | RR | Rank among states |
|---|---|---|
| Delivery Time | 29.85 days | Longest |
| Review Score | 3.58 | Lowest |
| Total Orders | 45 | Fewest |
| Active Sellers | 38 | Fewest |
| Freight % of Revenue | 28.20% | Highest |
| Estimated Gross Margin | ~72% | Lowest |

**So what:** These aren't six separate problems — they're one geographic/infrastructure gap radiating through every metric. A thin seller base → few orders → proportionally higher freight cost → slower delivery → lower satisfaction. The fix isn't "improve delivery speed" in isolation; it's growing the seller base in remote regions.

---

### 5. RR customers actually spend *more* per order — the problem is reach, not spending power

*(AOV per state is derived: Revenue ÷ Orders, using confirmed dashboard figures)*

| State | Estimated AOV |
|---|---|
| SP | $125.9 (lowest) |
| RJ | $143.2 |
| BA | $152.9 |
| **RR** | **~$166.7 (highest)** |

**So what:** RR's problem is not low-value customers — its individual orders are worth more than SP's. The real constraint is market access: only 45 orders exist at all. This reframes the fix from "discount to attract buyers" to "expand seller/logistics reach."

---

### 6. Freight cost eats disproportionately into remote-state margins

*(Estimated gross margin = (Revenue − Freight) ÷ Revenue, derived from Revenue-by-state and Freight-by-state tooltips)*

| State | Freight % of Revenue | Est. Gross Margin |
|---|---|---|
| SP | 13.77% (lowest) | ~86% |
| RJ | 16.72% | ~83% |
| AL | 19.94% | ~80% |
| **RR** | **28.20% (highest)** | **~72%** |

**So what:** RR is not just the slowest state — it's also the least profitable on a margin basis, roughly 14 points below SP. Distance erodes margin directly, not just customer experience.

---

### 7. Late shipments don't always become late deliveries — except in Alagoas (AL)

- **Late Shipment Rate** (seller misses pickup deadline): 14.62%
- **Delayed Orders Rate** (order actually arrives late to customer): 7.93%

**So what:** Roughly half of seller-side shipping delays get absorbed somewhere in the logistics chain before reaching the customer — Olist's delivery estimate appears to have built-in buffer. **Alagoas (AL) is the exception**: it has both the highest delayed-orders rate (23.12%) *and* the highest late-shipment rate (12.33%) simultaneously — a genuine, unbuffered operational failure rather than a statistical outlier.

---

### 8. Category performance: volume and quality don't automatically move together

| Category | Orders | Review Score | Note |
|---|---|---|---|
| cama_mesa_banho | **9,417** (highest volume) | 3.97 | High volume, average quality |
| beleza_saude | 8,836 | **4.18** | Best quality *among* high-volume categories |
| moveis_escritorio | 1,273 | **3.62** | Lowest score with a reliable sample size |
| seguros_e_servicos | 2 orders only | 2.50 | ⚠️ Sample too small to be meaningful — exclude from rankings |

**So what:** beleza_saude proves high volume and high satisfaction can coexist — it's the benchmark other large categories (especially cama_mesa_banho) should be measured against.

---

### 9. Credit card users spend significantly more than other payment methods

| Payment Type | Share of Orders | Avg. Order Value |
|---|---|---|
| Credit Card | 74% | **$163** |
| Boleto | 19% | $145 |
| Debit Card | 1% | $143 |
| Voucher | 6% | **$66** |

**So what:** Credit card customers spend 2.5x more than voucher users. Incentivizing credit card adoption (installment plans, cashback) is a plausible lever to raise overall AOV.

---

## 💼 Business Recommendations

> *Grounded directly in the findings above — not generic advice.*

1. **Prioritize Alagoas (AL) for logistics intervention.** It's the only state where both shipping delays *and* customer-facing delays are simultaneously high — indicating a real operational failure rather than a statistical fluke elsewhere.
2. **Treat RR as a market-access problem, not a pricing problem.** Its AOV is already the highest in the dataset; the constraint is seller density and order volume, not customer willingness to spend.
3. **Protect the top-10-seller concentration.** With ~13% of revenue tied to 10 sellers, consider seller retention programs and onboarding more mid-tier sellers in SP to diversify risk.
4. **Use beleza_saude as the internal benchmark** for balancing scale and quality when reviewing underperforming high-volume categories like cama_mesa_banho and moveis_escritorio.
5. **Push credit card adoption** through installment or cashback incentives — it's already the dominant and highest-spending payment method.
6. **Exclude micro-sample states/categories (RR, AP, AC, seguros_e_servicos)** from headline rankings in stakeholder-facing reports; report them separately with a sample-size caveat.

---

## 🛠️ Technical Details

### Data Model
- **Star schema** with `olist_orders_dataset` as the central fact table
- Separate **Date Table** created in DAX with fixed boundaries (2016–2018) for Time Intelligence
- Geolocation split into `geo_customers` and `geo_sellers` to resolve Many-to-Many relationship issues

### Key DAX Measures
- `Total Revenue` — SUM of price from order_items
- `Gross Profit` — Revenue minus Total Freight Cost
- `Avg Delivery Time` — AVERAGEX with DATEDIFF on delivered orders only
- `Delayed Orders %` — CALCULATE with FILTER on delivery vs. estimated date
- `Late Shipment %` — COUNTROWS with RELATED comparing shipping_limit_date vs. actual carrier pickup
- `Avg Review Score` — CALCULATE with CROSSFILTER to bridge reviews and order_items
- `On-Time Delivery Rate` — complement of Delayed Orders %

---

## 👤 Author

**Mohammed** — Data Analyst | Power BI & Excel


---

*Dataset: Olist Brazilian E-Commerce — Kaggle | Tool: Microsoft Power BI Desktop*
