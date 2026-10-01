# Case Brief: Diagnosing the Revenue Decline at "NovaFit Subscriptions"

## Background
NovaFit Subscriptions is a mid-size D2C subscription business (fitness content +
coaching plans, 3 tiers: Basic / Standard / Premium). New customer signups have
stayed roughly steady over the past 18 months, but **total monthly recurring
revenue (MRR) has been declining over the last several months** despite that
steady signup volume.

Leadership is meeting next week to decide where to allocate a limited retention
budget, and they've asked for a diagnosis — not just a dashboard.

## The Ask
> "Revenue is down and new signups look fine, so something else is going on.
> Is this a churn problem, a downgrade problem, or an acquisition-quality
> problem? Find the real driver, size it in rupees, and tell us the single
> highest-ROI place to intervene."

## Suggested Approach (this is the "problem-solving" structure — don't skip steps)
1. **State 3-4 competing hypotheses before touching the data**, e.g.:
   - H1: Existing customers are downgrading plans (contraction)
   - H2: One or more cohorts are churning faster than usual
   - H3: One acquisition channel is bringing in lower-quality/less-sticky customers
   - H4: Seasonal effect, not a structural problem
2. **Build a revenue bridge/waterfall** (New + Expansion − Contraction − Churn)
   to see which component is actually driving the MoM decline.
3. **Drill into the responsible component** by cohort, channel, plan tier, and
   region to isolate *where* it's concentrated.
4. **Quantify a fix**: model the revenue recovered if you reduced the churn/
   downgrade rate in the responsible segment by X points, versus a realistic
   intervention cost (e.g., a retention campaign).
5. **Write a 1-page recommendation memo** with 2-3 supporting exhibits — not a
   10-tab dashboard nobody will open.

## Files Provided

### 1. `customers_master.csv` (customer-level, relatively clean — CRM export)
| Column | Description |
|---|---|
| customer_id | Unique customer identifier |
| signup_date | Date customer first subscribed (YYYY-MM-DD) |
| acquisition_channel | Marketing channel that acquired the customer |
| initial_plan_tier | Plan tier at signup (Basic / Standard / Premium) |
| region | Customer's region (North/South/East/West) |
| acquisition_cost_inr | Cost to acquire this customer (CAC), in ₹ |

### 2. `monthly_billing_activity.csv` (transaction-level — raw billing system export)
**Note: this file is intentionally messy**, the way a real billing system export
usually is. Expect to clean it in Power Query before analysis.
| Column | Description |
|---|---|
| customer_id | Customer identifier (occasionally has stray whitespace) |
| billing_month | Month of this billing record — **mixed date formats** (some `YYYY-MM-DD`, some `DD-MM-YYYY`) |
| acquisition_channel | Same as master file, but **inconsistent casing/spacing/underscores** (e.g. "Paid Social", "PAID_SOCIAL", "paid social ") |
| plan_tier | Basic/Standard/Premium — **inconsistent casing** |
| tenure_month | Number of full months since signup (0 = signup month) |
| status | `new`, `active`, `churned`, `upgraded`, `downgraded` for that month |
| mrr_amount | Revenue billed that month (₹). **A small % are blank** — recompute from plan_tier for active rows |
| signup_date | Duplicated here for convenience — same mixed-format issue |
| region | Occasionally blank |
| acquisition_cost_inr | Duplicated here for convenience |

**Known data quality issues to handle in Power Query:**
- Standardize `acquisition_channel` and `plan_tier` text (trim, proper case, fix underscores)
- Parse `billing_month` / `signup_date` — two different date formats mixed together
- Recompute missing `mrr_amount` from `plan_tier` where status is revenue-bearing
- Remove exact duplicate rows (a small number exist — billing system glitch)
- Trim whitespace from `customer_id`
- Handle blank `region` values (decide: exclude from region-cut analysis, or bucket as "Unknown")

## Deliverable
A single Excel workbook containing:
1. Cleaned data model (Power Query)
2. Revenue bridge/waterfall by month
3. Cohort retention table (heatmap)
4. Channel × tenure churn drill-down
5. Win-back ROI scenario model (Data Table / Scenario Manager)
6. One-page recommendation summary (numbers + recommendation, not just charts)
