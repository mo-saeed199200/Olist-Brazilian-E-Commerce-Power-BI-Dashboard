# 🛒 Olist Brazilian E-Commerce — Power BI Dashboard

## Project Overview

This project analyzes the **Olist Brazilian E-Commerce** public dataset from Kaggle, covering over **99,000 orders** placed between **October 2016 and August 2018**. It's a multi-page Power BI dashboard built to answer real business questions across sales, logistics, customer satisfaction, sellers, and geography.

**Tool:** Microsoft Power BI Desktop
**Dataset:** [Kaggle — Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
**Tables used:** 9 tables (orders, order items, customers, sellers, products, payments, reviews, geolocation, product category translation)

---

## 📊 Dashboard Pages

| Page | What it answers |
|---|---|
| **Home** | Navigation landing page |
| **Overview** | What's the overall health of the business? |
| **Revenue** | What's selling, how, and who's paying with what? |
| **Customer** | Are customers happy, and what drives satisfaction? |
| **Sellers** | Who are the best/worst performing sellers? |
| **Map** | Where geographically is the business strong or weak? |

---

## ⚠️ Data Limitations

1. **No COGS (Cost of Goods Sold).** "Gross Profit" here means **Revenue minus Freight Cost only** — not true gross profit. Net Profit cannot be calculated.
2. **Customer ID resets per order.** A new `customer_id` is assigned per order, even for the same person. Repeat-purchase behavior **cannot be tracked**.
3. **Dataset ends mid-2018.** The sharp revenue drop after August 2018 is **data truncation**, not a real downturn.
4. **Geolocation duplicates** required splitting geolocation into separate customer-geo and seller-geo tables to avoid Many-to-Many errors.
5. **Product categories are in Portuguese** throughout.
6. **Small-sample states** (RR: 45 orders, AP: ~67, AC: 77) make any insight involving them lower-confidence.
7. **Some metrics are derived** (e.g., AOV per state, gross margin per state) by combining two confirmed dashboard numbers — labeled "estimated" wherever used.
8. **seguros_e_servicos has only 2 orders** — its 2.50 review score is a statistical artifact, excluded from category rankings.

---

## 🔑 Headline KPIs

### Overall Business
| Metric | Value |
|---|---|
| Total Revenue | **$13.6M** |
| Total Orders | **99K** |
| Total Customers | **96K** |
| Total Items Sold | **113K** |
| Average Order Value (AOV) | **$136.7** |
| Items per Order | **1.14** |
| Gross Profit (Revenue − Freight) | **$11.34M** |
| Gross Profit Margin | **83.43%** |
| Total Freight Cost | **$2.25M** |
| Freight-to-Sales Ratio | **16.57%** |

### Logistics & Delivery
| Metric | Value |
|---|---|
| Average Delivery Time | **12.5 days** |
| On-Time Delivery Rate | **92.07%** |
| Delayed Orders Rate | **7.93%** |
| Late Shipment Rate (seller misses pickup deadline) | **14.62%** |

### Customer Satisfaction
| Metric | Value |
|---|---|
| Average Review Score | **4.0 / 5** |
| Positive Reviews (3–5★) | **85.37%** |
| Total Reviews Collected | **98K** |
| Sales per Customer | **$136.68** |

### Sellers
| Metric | Value |
|---|---|
| Active Sellers | **~3,000** |
| Avg Sales per Seller | **$4.39K** |

### Payments
| Payment Type | Share of Orders | Avg. Order Value |
|---|---|---|
| Credit Card | 74% (76,505 orders) | **$163** |
| Boleto | 19% (19,784 orders) | $145 |
| Voucher | 6% (3,866 orders) | **$66** (lowest) |
| Debit Card | 1% (1,528 orders) | $143 |

---

## 🚚 Delayed Orders & Late Shipment — Full State Breakdown

Two distinct metrics matter for logistics:
- **Late Shipment %** — the seller misses their own shipping deadline (`shipping_limit_date`)
- **Delayed Orders %** — the order actually arrives at the customer later than Olist's estimated date

These are **not the same failure point**. A high Late Shipment % with a *low* Delayed % means the logistics network is absorbing seller delays before they reach the customer. A high Delayed % *relative to* Late Shipment % means something is going wrong **after** the seller ships — most likely last-mile/carrier performance in that region.

| State | Delayed % | Late Shipment % | Gap (Delayed − Late) | Interpretation |
|---|---|---|---|---|
| AC | 2.60% | 8.14% | −5.54 | Strong buffer — seller delays fully absorbed |
| PR | 4.97% | 9.67% | −4.70 | Strong buffer |
| MG | 5.55% | 9.29% | −3.74 | Buffer working |
| SP | 5.80% | 9.32% | −3.52 | Buffer working |
| AM | 4.23% | 5.66% | −1.43 | Mild buffer |
| RS | 6.98% | 8.27% | −1.29 | Mild buffer |
| MT | 6.77% | 7.09% | −0.32 | Roughly balanced |
| AP | 4.48% | 4.94% | −0.46 | Roughly balanced |
| RN | 10.45% | 10.27% | +0.18 | Roughly balanced |
| MS | 11.50% | 10.19% | +1.31 | Slight downstream issue |
| RR | 11.11% | 8% | **+3.11** | Downstream/last-mile issue |
| TO | 12.55% | 9.12% | **+3.43** | Downstream/last-mile issue |
| **AL** | **23.12%** | **12.33%** | **+10.79** | **Major downstream failure** |

**So what:** Across most of Brazil, seller-side lateness (Late Shipment %) is *higher* than what customers actually experience (Delayed %) — meaning Olist's logistics network has real slack built in. **Alagoas (AL) breaks this pattern entirely**: its Delayed rate is almost **double** its Late Shipment rate. This means the root cause in AL is not seller punctuality — it's very likely a last-mile/carrier bottleneck specific to that region. RR, TO, and MS show the same directional pattern on a smaller scale. This reframes the fix: **AL doesn't need seller enforcement, it needs a carrier/logistics-partner review.**

---

### 🔍 Delayed Orders % in isolation — where does the customer actually feel the delay?

Looking at Delayed Orders % **on its own** (ignoring Late Shipment for a moment), the states split into three clear tiers:

| Tier | States | Delayed % Range |
|---|---|---|
| **Excellent** | AC, AM, AP, PR, MG, SP | 2.60% – 5.80% |
| **Concerning** | RN, RR, MS, TO | 10.45% – 12.55% |
| **Critical outlier** | **AL** | **23.12%** |

**So what:** AL is not just "the worst" — it's a *categorical* outlier. It sits at roughly **9x** the best-performing state (AC, 2.60%) and nearly **2x** the next-worst state (TO, 12.55%). Even before comparing it to seller behavior, Delayed Orders % alone already singles out AL as a distinct operational emergency, not a gradual regional weakness.

---

### 🔍 Late Shipment % in isolation — where are sellers missing their own deadlines?

Looking at Late Shipment % **on its own**, the picture is very different — much flatter and more evenly spread:

| State | Late Shipment % |
|---|---|
| AP (best) | 4.94% |
| AM | 5.66% |
| MT | 7.09% |
| RR | 8% |
| AC | 8.14% |
| RS | 8.27% |
| TO | 9.12% |
| MG | 9.29% |
| SP | 9.32% |
| PR | 9.67% |
| MS | 10.19% |
| RN | 10.27% |
| AL (worst) | 12.33% |

**So what:** The range here is only **~2.5x** (4.94% → 12.33%) compared to Delayed Orders' **~9x** range. Most states cluster tightly between 7–10%, which suggests seller lateness is a **platform-wide behavior pattern**, not a region-specific failure — sellers everywhere in Brazil miss deadlines at broadly similar rates. AL is still the worst here too, but only moderately worse than second-place RN (12.33% vs 10.27%) — nothing like the dramatic gap seen in Delayed Orders %.

**Interesting contrast — Acre (AC):** AC has the *best* Delayed Orders % (2.60%) despite a *middling* Late Shipment % (8.14%). This means AC's logistics chain absorbs seller lateness almost completely — whatever carrier setup serves AC could be a useful internal benchmark when redesigning AL's logistics partnership.

---

### 🧩 Putting it together: the full Alagoas (AL) story

Now combine both lenses using AL's own confirmed numbers:

| Step | Value |
|---|---|
| AL's Late Shipment % (the reference point — how late sellers actually ship) | 12.33% |
| Typical pattern elsewhere: Late Shipment is usually 3–5 pts *higher* than Delayed (buffer absorbs seller delay) | e.g. SP: 9.32% Late vs 5.80% Delayed |
| **If AL followed that same typical pattern**, its Delayed % should have landed *below* 12.33% | Expected: **~8–12%** |
| AL's **actual** Delayed Orders % | **23.12%** |
| **Unexplained excess** (Actual − Expected) | **≈ +11–15 percentage points** |

**So what:** Even if AL's sellers behaved exactly like the platform average (12.33% late shipment, roughly in line with RN/MS), the "typical" logistics buffer seen everywhere else would predict a Delayed rate somewhere around 8–12%, similar to RN/MS/TO. Instead, AL's actual Delayed rate is **23.12%** — an excess of roughly **11–15 percentage points that seller behavior does not explain at all**. This gap is the real fingerprint of a last-mile/carrier problem unique to Alagoas: not sellers shipping late, but something breaking down specifically in the journey between the carrier pickup and the customer's door in that state. This is why the recommendation is a **logistics-partner audit**, not seller pressure — pushing AL's sellers to ship earlier would not meaningfully move the Delayed Orders number.

---

## 🗺️ State-Level KPIs (Top & Bottom Performers)

| State | Revenue | Orders | Review Score | Delivery Time |
|---|---|---|---|---|
| **SP** | $5.03M (37% of total) | 39,938 | 4.17 | **9 days** (fastest) |
| RJ | $1.76M | 12,284 | 3.88 | 15.23 |
| MG | $1.54M | 11,162 | 4.14 | 11.94 |
| **AP** | ~$13.3K | ~67 | **4.20** (highest) | 27 |
| **AC** | ~$15.2K | 77 | 4.18 | 20 |
| **AL** | ~$15.4K | 398 | 3.77 | 24.55 |
| **RR** | **$7.5K** (lowest) | **45** (fewest) | **3.58** (lowest) | **29.85** (longest) |

*Revenue and order figures for smaller states are approximate, read from chart tooltips.*

---

## 🛍️ Category-Level KPIs

| Category | Orders | Revenue | Review Score |
|---|---|---|---|
| cama_mesa_banho | **9,417** (highest volume) | $1.04M | 3.97 |
| beleza_saude | 8,836 | **$1.26M** (highest revenue) | **4.18** (best among high-volume) |
| esporte_lazer | 7,720 | $988K | 4.17 |
| informatica_acessorios | 6,689 | $912K | 4.03 |
| moveis_decoracao | 6,449 | $730K | 4.01 |
| relogios_presentes | 5,624 | $1.21M | 4.07 |
| cds_dvds_musicais | 730 | $730 | **4.67** (highest score overall) |
| moveis_escritorio | 1,273 | $273,961 | **3.62** (lowest with reliable sample) |
| seguros_e_servicos | **2 only** | $283 | 2.50 ⚠️ (excluded — sample too small) |

---

## 💡 Deep-Dive Insights

### 1. 🚨 Alagoas (AL) has a downstream logistics failure, not a seller problem — the most actionable finding in this dataset

| Metric | Most other states | Alagoas (AL) |
|---|---|---|
| Pattern | Late Shipment % > Delayed % | **Delayed % (23.12%) is nearly 2x Late Shipment % (12.33%)** |
| Meaning | Seller delays get absorbed before reaching the customer | Even orders shipped **on time** still arrive late |

In almost every other state (AC, PR, MG, SP...), the seller's shipping lateness is *higher* than what the customer actually experiences — meaning Olist's logistics network has real buffer built in. **Alagoas breaks that pattern entirely.** Its Delayed Orders rate is nearly **double** its Late Shipment rate, which means the failure is happening **after** the seller ships, not because of the seller. RR, TO, and MS show the same directional pattern on a smaller scale (see the full state-by-state breakdown, standalone Delayed/Late Shipment analysis, and the complete AL walkthrough in the section titled *"🚚 Delayed Orders & Late Shipment — Full State Breakdown"* earlier in this document).

**So what:** Fixing AL is not about pressuring sellers to ship faster — it's about auditing the **carrier/last-mile logistics partner** operating in that region specifically. This is the single clearest, most actionable operational insight in the entire dataset.

---

### 2. Revenue is heavily concentrated — geographically and by seller
- **SP alone = 37%** of total revenue
- **Top 3 states (SP+RJ+MG) = 61%** of total revenue
- **Top 10 sellers (out of 3,000+) = ~13%** of total revenue (~$1.79M)

**So what:** Fewer than 0.35% of sellers generate 13% of platform revenue — a concentration risk if a handful of top sellers or SP itself underperforms.

---

### 3. Delivery time is the strongest predictor of review score — at order level
| Review Score | Avg. Delivery Time |
|---|---|
| ⭐1 | 21.29 days |
| ⭐2 | 16.60 days |
| ⭐3 | 14.20 days |
| ⭐4 | 12.25 days |
| ⭐5 | 10.61 days |

**So what:** A 1-star reviewer waits roughly **2x longer** than a 5-star reviewer — the cleanest relationship in the dataset.

---

### 4. At the state level, identical delivery times can produce opposite satisfaction — an open question
AP (67 customers) and (27 days) scores **4.20** (highest); RR (45 customers) and (29.85 days) scores **3.58** (lowest) — nearly the same wait, opposite outcomes. Delivery time alone doesn't fully explain regional satisfaction; this is flagged as an open question rather than forced into a tidy explanation the data doesn't support.

---

### 5. RR isn't failing on multiple fronts — it's one root cause radiating outward
| Metric | RR | Rank |
|---|---|---|
| Delivery Time | 29.85 days | Longest |
| Review Score | 3.58 | Lowest |
| Orders | 45 | Fewest |
| Active Sellers | 38 | Fewest |
| Freight % of Revenue | 28.20% | Highest |
| Est. Gross Margin | ~72% | Lowest |

**So what:** Thin seller base → few orders → proportionally higher freight cost → slower delivery → lower satisfaction. One geographic/infrastructure gap, not six separate problems.

---

### 6. RR customers spend *more* per order — the constraint is reach, not spending power
*(AOV per state = Revenue ÷ Orders, derived from confirmed figures)*

| State | Est. AOV |
|---|---|
| SP | $125.9 (lowest) |
| RJ | $143.2 |
| BA | $152.9 |
| **RR** | **~$166.7 (highest)** |

**So what:** RR's issue isn't low-value customers — the real constraint is market access (only 45 orders exist total).

---

### 7. Freight cost erodes margin disproportionately in remote states
*(Est. margin = (Revenue − Freight) ÷ Revenue, derived from tooltip data)*

| State | Freight % of Revenue | Est. Gross Margin |
|---|---|---|
| SP | 13.77% (lowest) | ~86% |
| RJ | 16.72% | ~83% |
| AL | 19.94% | ~80% |
| **RR** | **28.20% (highest)** | **~72%** |

**So what:** RR is the least profitable state on a margin basis — roughly 14 points below SP.

---

### 8. Category performance: volume and quality don't automatically move together
**cama_mesa_banho** has the highest order volume (9,417) but only average satisfaction (3.97), while **beleza_saude** achieves both high volume (8,836) *and* the best satisfaction among big categories (4.18).

**So what:** beleza_saude is the internal benchmark other high-volume categories should be measured against.

---

### 9. Credit card users spend 2.5x more than voucher users
Credit card AOV ($163) vs. voucher AOV ($66), while credit card is also the dominant payment method (74% of orders).

**So what:** Incentivizing credit card adoption (installment plans, cashback) is a plausible lever to raise overall AOV.

---

## 💼 Business Recommendations

Each recommendation follows the same structure: the data behind it, the specific action, the expected impact, and how to know if it worked.


### 1. 🚨 Audit the carrier/last-mile logistics partner serving Alagoas (AL)

The data: AL's Delayed Orders rate (23.12%) is nearly double its Late Shipment rate (12.33%) — the opposite of every other state, where seller lateness is higher than what customers experience. The unexplained gap (≈11–15 points) cannot be attributed to seller behavior (see the full Case Study above).

**Action:**
Request performance-by-region data from the carrier(s) currently handling AL deliveries
Compare AL's carrier against the one serving AC — AC has the best Delayed % (2.60%) despite average seller lateness, suggesting its carrier absorbs delays well and could be a benchmark or even a direct alternative for AL
If a single carrier serves both regions, isolate whether the AL branch/route specifically underperforms

**Expected impact:**
Closing even half the unexplained gap would bring AL's Delayed rate from 23.12% down toward ~15%, likely lifting its review score (currently 3.77, below the 4.0 platform average) meaningfully, since delivery time is the strongest single predictor of review score in this dataset (Insight #3).

**How to measure success:**
Track AL's Delayed Orders % and Late Shipment % separately, month over month. Success = the gap between them shrinks toward the platform-typical −3 to −5 points, not just the headline Delayed number dropping.

### 2. 📦 Reframe Roraima (RR) as a market-access investment, not a pricing problem

The data: RR has the highest estimated AOV in the dataset (~$166.7) but the fewest orders (45) and fewest active sellers (38). Its freight cost consumes 28.20% of revenue — the highest of any state — while its estimated gross margin (~72%) is the lowest.

**Action:**
Do not run discount or voucher campaigns targeting RR — its customers already spend more than average per order, so price is not the barrier
Investigate seller onboarding incentives specific to RR (reduced platform fees, subsidized freight for the first N shipments) to grow the 38-seller base
Evaluate whether a regional fulfillment/consolidation point could reduce the 28.20% freight-to-revenue ratio

**Expected impact:**
Even a modest increase in active sellers (e.g., 38 → 60) could proportionally increase order volume and improve delivery times through more localized shipping options, without needing to change customer behavior at all.

**How to measure success:**
Track active-seller count and average freight % of revenue in RR quarterly — both should move before order volume does, since seller density is the root constraint identified in Insight #5.

## 3. 🏪 Build a seller-concentration risk dashboard and diversification plan

The data: The top 10 sellers (out of 3,000+) generate approximately 13% of total platform revenue (~$1.79M). SP alone accounts for 37% of total revenue.

**Action:**
Set up ongoing monitoring of revenue share by top-N sellers (10, 25, 50) to catch rising concentration early
Launch a retention program for the current top 10 (dedicated support, early access to promotions) to reduce churn risk
Actively recruit and onboard mid-tier sellers in SP specifically, since that's where order density is already highest (15.8 orders per active seller — see Insight #5's regional density analysis) and easiest to scale

**Expected impact:**
Reducing top-10 revenue share from 13% toward, e.g., 8–10% over time would meaningfully lower single-seller dependency risk without requiring any drop in absolute revenue from those sellers.

**How to measure success:**
Quarterly revenue-share-by-top-N-sellers trend; flag if any single seller exceeds 2–3% of total platform revenue.

## 4. ⭐ Use beleza_saude as the internal quality benchmark for high-volume categories

The data: beleza_saude has both high order volume (8,836) and the best review score among large categories (4.18). By contrast, cama_mesa_banho has higher volume (9,417) but a noticeably lower score (3.97), and moveis_escritorio scores even lower (3.62) with a reliable sample (1,273 orders).

**Action:**
Compare seller quality standards, packaging practices, and delivery partners used within beleza_saude against cama_mesa_banho and moveis_escritorio
Survey or sample recent low-rated reviews specifically within moveis_escritorio to identify whether the issue is product quality, delivery damage, or expectation mismatch
Consider category-specific seller guidelines if a common root cause emerges

**Expected impact:**
Even closing half the gap between moveis_escritorio (3.62) and beleza_saude (4.18) would materially improve that category's contribution to overall platform satisfaction.

**How to measure success:**
Track review score trend for moveis_escritorio and cama_mesa_banho specifically after any intervention, independent of overall platform average.

## 5. 💳 Incentivize credit card adoption to raise platform-wide AOV

The data: Credit card is both the dominant payment method (74% of orders) and the highest-spending one (AOV $163), compared to voucher users who spend the least (AOV $66) — a 2.5x gap.

**Action:**
Test installment-plan promotions or cashback offers specifically for credit card checkouts
A/B test whether nudging voucher-eligible customers toward credit card (where eligible) changes basket size
Avoid assuming causation — test this as a hypothesis, since higher-spending customers may simply prefer credit cards already, rather than the payment method itself driving spend

**Expected impact:**
If the relationship is even partially causal, shifting a portion of boleto/voucher volume to credit card could lift overall AOV from $136.7 toward the credit-card-only average of $163.

**How to measure success:**
Run as a controlled test (e.g., regional or time-boxed promotion) and compare AOV lift against a control group — don't rely on the raw correlation alone as proof.

## 6. 📊 Apply a minimum sample-size threshold to all regional and category reporting

The data: RR (45 orders), AP (~67), AC (77), and seguros_e_servicos (2 orders) all have sample sizes small enough that a handful of orders can swing an average dramatically — as seen in Insight #4, where AP and RR have nearly identical delivery times but opposite satisfaction outcomes.

**Action:**
Set a minimum order threshold (e.g., 200+ orders) before a state or category appears in headline "best/worst" rankings shown to stakeholders
Report sub-threshold regions/categories separately, clearly labeled as "directional only — low sample size"
Apply the same logic before greenlighting any operational change based on a single low-volume region's numbers

**Expected impact:**
Prevents resources being misallocated based on statistically unstable numbers (e.g., treating AP's high score as a success story to replicate, when it may simply be a small, favorable sample).

**How to measure success:**
No metric here — this is a reporting-hygiene practice. Success is qualitative: fewer stakeholder decisions made on volatile small-sample data going forward.

---

## 📍 Case Study: Alagoas (AL) — Anatomy of a Logistics Failure

*This section pulls every AL-related finding from across the dashboard into one standalone narrative, since it's the single most actionable operational discovery in this analysis.*

### The headline number
Alagoas has a **23.12% Delayed Orders rate** — nearly **double** the second-worst state (TO, 12.55%) and roughly **9x** the best-performing state (AC, 2.60%). This alone marks AL as a categorical outlier, not a gradually weaker region.

### Ruling out the obvious suspect: sellers
The first assumption would be "AL's sellers ship late." The data says otherwise:

| Metric | AL | Platform pattern |
|---|---|---|
| Late Shipment % (seller misses their own deadline) | 12.33% | Highest in Brazil, but only moderately above RN (10.27%) — a **~2.5x** spread across all states |
| Delayed Orders % (order actually arrives late) | 23.12% | Dramatically highest — a **~9x** spread across all states |

Seller lateness varies only modestly from state to state (roughly 5–12% everywhere). If AL's sellers were the root cause, its Delayed rate should track that same modest variation. It doesn't — which rules sellers out as the primary driver.

### Quantifying the "unexplained" gap
In every other state, Late Shipment % is *higher* than Delayed % — meaning Olist's logistics network typically absorbs and hides seller delays before the customer notices (e.g., SP: 9.32% late shipment → only 5.80% delayed). Applying that same absorption pattern to AL's own late-shipment rate (12.33%) would predict a Delayed rate of roughly **8–12%** — in line with RN, MS, and TO.

Instead, AL's actual Delayed rate is **23.12%** — an excess of **≈ 11–15 percentage points that seller behavior cannot explain.**

### What this means
The failure point sits **after** the seller hands off the package — most likely a specific carrier or last-mile logistics partner struggling to operate in that state. This is reinforced by AL also carrying elevated freight costs relative to revenue and a below-average review score (3.77), consistent with a region where the delivery experience — not the product or the seller — is the weak link.

### The business implication
Every other lever this dashboard suggests (seller incentives, pricing, category focus) targets behavior Olist can influence through sellers or marketing. **AL requires a different kind of fix entirely: a logistics/carrier-partner audit specific to that state.** Pressuring AL's sellers to ship earlier — the intuitive first response — would not meaningfully move the Delayed Orders number, because the problem was never on the seller's side of the handoff.

---

## 🛠️ Technical Details

### Data Model
- **Star schema** with `olist_orders_dataset` as the central fact table
- Separate **Date Table** built in DAX with fixed boundaries (2016–2018), marked as an official Date Table for Time Intelligence
- Geolocation split into `geo_customers` and `geo_sellers` to resolve Many-to-Many relationship errors
- `CROSSFILTER()` used selectively inside measures to bridge fact tables (orders ↔ order_items ↔ reviews) without introducing ambiguous paths in the model

### Key DAX Measures
```dax
Total Revenue = SUM(olist_order_items_dataset[price])

Total Freight Cost = SUM(olist_order_items_dataset[freight_value])

Gross Profit = [Total Revenue] - [Total Freight Cost]

Gross Profit Margin % = DIVIDE([Gross Profit], [Total Revenue], 0)

Avg Delivery Time =
AVERAGEX(
    FILTER(olist_orders_dataset, olist_orders_dataset[order_status] = "delivered"),
    DATEDIFF(
        olist_orders_dataset[order_purchase_timestamp],
        olist_orders_dataset[order_delivered_customer_date],
        DAY
    )
)

Delayed Orders % =
VAR DelayedCount =
    CALCULATE([Total Orders],
        FILTER(olist_orders_dataset,
            olist_orders_dataset[order_delivered_customer_date] >
            olist_orders_dataset[order_estimated_delivery_date]))
RETURN DIVIDE(DelayedCount, [Total Orders], 0)

Late Shipment % =
DIVIDE(
    COUNTROWS(
        FILTER(olist_order_items_dataset,
            olist_order_items_dataset[shipping_limit_date] <
            RELATED(olist_orders_dataset[order_delivered_carrier_date]))),
    COUNTROWS(olist_order_items_dataset), 0)

Avg Review Score =
CALCULATE(
    AVERAGE(olist_order_reviews_dataset[review_score]),
    CROSSFILTER(olist_orders_dataset[order_id], olist_order_reviews_dataset[order_id], BOTH)
)

Active Sellers = DISTINCTCOUNT(olist_order_items_dataset[seller_id])
```

---

## 👤 Author

**Mohammed** — Data Analyst | Power BI & Excel


---

*Dataset: Olist Brazilian E-Commerce — Kaggle | Tool: Microsoft Power BI Desktop*
