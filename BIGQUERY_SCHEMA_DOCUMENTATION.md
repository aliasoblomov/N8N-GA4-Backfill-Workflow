# BigQuery Schema Documentation - GA4 Backfill Tables

**Project:** `ga4-pavaglione-cosmetics`
**Dataset:** `Ga4backfill`
**Location:** US
**Date Range Format:** Tables are named with suffix `_{YYYYMMDD}_{YYYYMMDD}`
**Example:** `ga4_data_session_channel_group_20250101_20250228`

---

## Table of Contents
1. [Traffic & Acquisition Tables](#traffic--acquisition-tables)
2. [Geographic & Device Tables](#geographic--device-tables)
3. [E-commerce Tables](#e-commerce-tables)
4. [Advertising Performance](#advertising-performance)
5. [Engagement & Behavior Tables](#engagement--behavior-tables)
6. [Data Type Reference](#data-type-reference)
7. [Query Examples](#query-examples)

---

## Traffic & Acquisition Tables

### 1. `ga4_data_session_channel_group`
Session-level metrics aggregated by default channel grouping.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date of the session (YYYY-MM-DD) |
| `sessionDefaultChannelGroup` | STRING | Default channel group (Organic Search, Paid Search, Direct, Referral, etc.) |
| `sessions` | INT64 | Number of sessions |
| `totalUsers` | INT64 | Total number of users |
| `newUsers` | INT64 | Number of new users |
| `ecommercePurchases` | INT64 | Number of purchase transactions |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |

**Use Cases:**
- Channel performance comparison
- Marketing mix analysis
- Budget allocation optimization

**Example Query:**
```sql
SELECT
  sessionDefaultChannelGroup,
  SUM(sessions) as total_sessions,
  SUM(purchaseRevenue) as total_revenue,
  SUM(purchaseRevenue) / SUM(sessions) as revenue_per_session
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_data_session_channel_group_*`
GROUP BY sessionDefaultChannelGroup
ORDER BY total_revenue DESC;
```

---

### 2. `ga4_data_session_source_campaign_medium`
Detailed traffic source attribution with campaign tracking.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date of the session |
| `sessionSource` | STRING | Traffic source (google, facebook, direct, etc.) |
| `sessionCampaignName` | STRING | Campaign name from UTM parameters |
| `sessionMedium` | STRING | Traffic medium (organic, cpc, email, referral, etc.) |
| `sessions` | INT64 | Number of sessions |
| `totalUsers` | INT64 | Total number of users |
| `newUsers` | INT64 | Number of new users |
| `ecommercePurchases` | INT64 | Number of purchase transactions |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |

**Use Cases:**
- Campaign performance tracking
- UTM parameter analysis
- Multi-touch attribution modeling

---

### 3. `ga4_data_first_user_source_medium`
First-touch attribution data showing initial user acquisition source.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `firstUserMedium` | STRING | Medium of first user acquisition |
| `firstUserSource` | STRING | Source of first user acquisition |
| `firstUserCampaignName` | STRING | Campaign name of first user acquisition |
| `totalUsers` | INT64 | Total number of users |
| `newUsers` | INT64 | Number of new users |
| `ecommercePurchases` | INT64 | Number of purchase transactions |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |

**Use Cases:**
- First-touch attribution analysis
- Customer acquisition cost (CAC) calculation
- Long-term channel value assessment

---

### 4. `ga4_data_first_user_channel_group`
First-touch attribution aggregated by channel group.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `firstUserDefaultChannelGroup` | STRING | Channel group of first user acquisition |
| `totalUsers` | INT64 | Total number of users |
| `newUsers` | INT64 | Number of new users |
| `ecommercePurchases` | INT64 | Number of purchase transactions |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |

**Use Cases:**
- Top-of-funnel channel analysis
- New user acquisition tracking
- Lifetime value (LTV) by acquisition channel

---

## Geographic & Device Tables

### 5. `ga4_data_country_language_city`
Geographic and language segmentation of traffic and conversions.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `country` | STRING | User's country (ISO format: Italy, United States, etc.) |
| `language` | STRING | Browser language (it, en, es, etc.) |
| `city` | STRING | User's city |
| `sessions` | INT64 | Number of sessions |
| `screenPageViews` | INT64 | Number of page views |
| `totalUsers` | INT64 | Total number of users |
| `newUsers` | INT64 | Number of new users |
| `ecommercePurchases` | INT64 | Number of purchase transactions |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |

**Use Cases:**
- Market expansion analysis
- Localization strategy
- Regional performance comparison
- Shipping cost optimization

**Example Query:**
```sql
SELECT
  country,
  SUM(sessions) as sessions,
  SUM(purchaseRevenue) as revenue,
  SUM(ecommercePurchases) as purchases
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_data_country_language_city_*`
WHERE country IN ('Italy', 'France', 'Germany', 'Spain')
GROUP BY country
ORDER BY revenue DESC;
```

---

### 6. `ga4_data_browser_os_device`
Technical environment segmentation for UX optimization.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `browser` | STRING | Browser name (Chrome, Safari, Firefox, etc.) |
| `operatingSystem` | STRING | Operating system (Windows, iOS, Android, macOS, etc.) |
| `deviceCategory` | STRING | Device type (mobile, desktop, tablet) |
| `sessions` | INT64 | Number of sessions |
| `screenPageViews` | INT64 | Number of page views |
| `totalUsers` | INT64 | Total number of users |
| `newUsers` | INT64 | Number of new users |
| `ecommercePurchases` | INT64 | Number of purchase transactions |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |

**Use Cases:**
- Mobile vs desktop conversion analysis
- Browser compatibility testing priorities
- Device-specific user experience optimization
- Progressive Web App (PWA) impact measurement

---

## E-commerce Tables

### 7. `ga4_transaction_items`
Granular product-level transaction data.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date of the transaction |
| `transactionId` | STRING | Unique transaction identifier |
| `itemName` | STRING | Product name |
| `itemPurchaseQuantity` | INT64 | Quantity purchased |
| `itemRevenue` | FLOAT64 | Revenue from this item (EUR) |

**Use Cases:**
- Transaction-level analysis
- Product affinity analysis (when joined with other items in same transaction)
- Order value distribution
- Refund analysis (when revenue is negative)

**Example Query - Top Products:**
```sql
SELECT
  itemName,
  SUM(itemPurchaseQuantity) as total_quantity,
  SUM(itemRevenue) as total_revenue,
  COUNT(DISTINCT transactionId) as transactions,
  SUM(itemRevenue) / COUNT(DISTINCT transactionId) as avg_revenue_per_transaction
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_transaction_items_*`
GROUP BY itemName
ORDER BY total_revenue DESC
LIMIT 20;
```

---

### 8. `ga4_data_item_name`
Aggregated product performance metrics.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `itemName` | STRING | Product name |
| `itemPurchaseQuantity` | INT64 | Total quantity purchased |
| `itemRevenue` | FLOAT64 | Total revenue from this product (EUR) |

**Use Cases:**
- Daily product performance tracking
- Inventory planning
- Product catalog optimization
- Seasonal trend analysis

---

## Advertising Performance

### 9. `ga4_ads_data`
Comprehensive advertising metrics including cost and ROAS.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `sessionSource` | STRING | Ad source (google, facebook, etc.) |
| `sessionMedium` | STRING | Ad medium (typically "cpc" for paid ads) |
| `sessionCampaignName` | STRING | Campaign name |
| `ecommercePurchases` | INT64 | Number of purchase transactions |
| `averagePurchaseRevenue` | FLOAT64 | Average revenue per purchase (EUR) |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |
| `advertiserAdClicks` | INT64 | Number of ad clicks |
| `advertiserAdCost` | FLOAT64 | Total ad spend (EUR) |
| `advertiserAdCostPerClick` | FLOAT64 | Cost per click (EUR) |
| `returnOnAdSpend` | FLOAT64 | ROAS (Revenue / Cost) |

**Use Cases:**
- Campaign profitability analysis
- ROAS optimization
- Budget allocation between platforms
- CPC vs conversion rate analysis

**Example Query - Campaign Performance:**
```sql
SELECT
  sessionSource,
  sessionCampaignName,
  SUM(advertiserAdCost) as total_spend,
  SUM(purchaseRevenue) as total_revenue,
  SUM(purchaseRevenue) / NULLIF(SUM(advertiserAdCost), 0) as roas,
  SUM(ecommercePurchases) as purchases,
  SUM(advertiserAdCost) / NULLIF(SUM(ecommercePurchases), 0) as cost_per_purchase
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_ads_data_*`
WHERE advertiserAdCost > 0
GROUP BY sessionSource, sessionCampaignName
ORDER BY total_spend DESC;
```

---

## Engagement & Behavior Tables

### 10. `ga4_all_metrics_data`
High-level daily KPIs and engagement metrics.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `sessions` | INT64 | Number of sessions |
| `totalUsers` | INT64 | Total number of users |
| `userEngagementDuration` | INT64 | Total engagement time in seconds |
| `newUsers` | INT64 | Number of new users |
| `engagementRate` | FLOAT64 | Percentage of engaged sessions (0-1) |
| `engagedSessions` | INT64 | Number of engaged sessions (>10s or 2+ pages or conversion) |
| `screenPageViews` | INT64 | Number of page views |
| `purchaseRevenue` | FLOAT64 | Total purchase revenue (EUR) |
| `ecommercePurchases` | INT64 | Number of purchase transactions |

**Use Cases:**
- Daily KPI dashboard
- Week-over-week / month-over-month comparisons
- Trend analysis
- Executive reporting

**Example Query - Daily Trends:**
```sql
SELECT
  date,
  sessions,
  totalUsers,
  engagementRate,
  purchaseRevenue,
  ecommercePurchases,
  purchaseRevenue / NULLIF(sessions, 0) as revenue_per_session,
  ecommercePurchases / NULLIF(sessions, 0) as conversion_rate
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_all_metrics_data_*`
ORDER BY date DESC;
```

---

### 11. `ga4_event_metrics_data`
Custom event tracking for conversion funnel and user interaction analysis.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `eventName` | STRING | Event name (purchase, add_to_cart, page_view, etc.) |
| `eventCount` | INT64 | Total number of events |
| `eventValue` | FLOAT64 | Sum of event values (if applicable) |
| `eventCountPerUser` | FLOAT64 | Average events per user |

**Use Cases:**
- Conversion funnel analysis
- Custom event tracking
- User engagement patterns
- A/B test validation

**Example Query - Funnel Analysis:**
```sql
SELECT
  eventName,
  SUM(eventCount) as total_events
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_event_metrics_data_*`
WHERE eventName IN ('view_item', 'add_to_cart', 'begin_checkout', 'purchase')
GROUP BY eventName
ORDER BY
  CASE eventName
    WHEN 'view_item' THEN 1
    WHEN 'add_to_cart' THEN 2
    WHEN 'begin_checkout' THEN 3
    WHEN 'purchase' THEN 4
  END;
```

---

### 12. `ga4_page_location_data`
URL-level performance metrics for content optimization.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `pageLocation` | STRING | Full URL of the page |
| `totalUsers` | INT64 | Number of users who viewed this page |
| `ecommercePurchases` | INT64 | Number of purchases (page in conversion path) |
| `purchaseRevenue` | FLOAT64 | Revenue attributed to this page (EUR) |
| `screenPageViews` | INT64 | Number of page views |
| `eventCount` | INT64 | Number of events on this page |
| `engagementRate` | FLOAT64 | Engagement rate for this page (0-1) |

**Use Cases:**
- Content performance analysis
- Landing page optimization
- High-exit page identification
- SEO content strategy

---

### 13. `ga4_landing_page_data`
Entry point analysis for traffic optimization.

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| `date` | DATE | The date |
| `landingPage` | STRING | URL of the landing page (first page in session) |
| `totalUsers` | INT64 | Number of users who entered via this page |
| `sessions` | INT64 | Number of sessions starting on this page |
| `eventCount` | INT64 | Number of events in sessions starting here |
| `ecommercePurchases` | INT64 | Number of purchases from sessions starting here |
| `engagementRate` | FLOAT64 | Engagement rate for these sessions (0-1) |
| `purchaseRevenue` | FLOAT64 | Revenue from sessions starting here (EUR) |

**Use Cases:**
- Landing page optimization
- Ad destination page performance
- Entry point bounce rate analysis
- First impression optimization

**Example Query - Best Landing Pages:**
```sql
SELECT
  landingPage,
  SUM(sessions) as total_sessions,
  SUM(ecommercePurchases) as purchases,
  SUM(purchaseRevenue) as revenue,
  SUM(ecommercePurchases) / NULLIF(SUM(sessions), 0) as conversion_rate,
  AVG(engagementRate) as avg_engagement_rate
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_landing_page_data_*`
GROUP BY landingPage
HAVING SUM(sessions) > 100
ORDER BY conversion_rate DESC
LIMIT 20;
```

---

## Data Type Reference

| BigQuery Type | Description | Example Values |
|--------------|-------------|----------------|
| `DATE` | Calendar date | `2025-01-15` |
| `STRING` | Text/varchar | `"Organic Search"`, `"Italy"`, `"Chrome"` |
| `INT64` | Integer (64-bit) | `1234`, `0`, `-5` |
| `FLOAT64` | Decimal (64-bit) | `123.45`, `0.85`, `1500.99` |

**NULL Handling:**
- Metrics with no data appear as `0` (zero) for counts and sums
- Dimension values are never NULL (GA4 returns "(not set)" for missing dimensions)
- Revenue fields can be `0.0` for non-revenue events

---

## Query Examples

### 1. Union All Date-Sharded Tables
```sql
-- Get all data across all periods for a specific table
SELECT *
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_all_metrics_data_*`
WHERE date >= '2025-01-01'
ORDER BY date DESC;
```

### 2. Month-over-Month Comparison
```sql
WITH monthly_data AS (
  SELECT
    FORMAT_DATE('%Y-%m', date) as month,
    SUM(sessions) as sessions,
    SUM(purchaseRevenue) as revenue,
    SUM(ecommercePurchases) as purchases
  FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_all_metrics_data_*`
  GROUP BY month
)
SELECT
  month,
  sessions,
  revenue,
  purchases,
  LAG(revenue) OVER (ORDER BY month) as prev_month_revenue,
  (revenue - LAG(revenue) OVER (ORDER BY month)) / LAG(revenue) OVER (ORDER BY month) * 100 as revenue_growth_pct
FROM monthly_data
ORDER BY month;
```

### 3. Channel Attribution Analysis
```sql
-- Compare first-touch vs last-touch attribution
SELECT
  'First Touch' as attribution_model,
  c.firstUserDefaultChannelGroup as channel,
  SUM(c.purchaseRevenue) as revenue
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_data_first_user_channel_group_*` c
GROUP BY channel

UNION ALL

SELECT
  'Last Touch' as attribution_model,
  s.sessionDefaultChannelGroup as channel,
  SUM(s.purchaseRevenue) as revenue
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_data_session_channel_group_*` s
GROUP BY channel

ORDER BY attribution_model, revenue DESC;
```

### 4. Product Performance Dashboard
```sql
SELECT
  i.itemName,
  SUM(i.itemPurchaseQuantity) as units_sold,
  SUM(i.itemRevenue) as revenue,
  COUNT(DISTINCT i.transactionId) as transactions,
  SUM(i.itemRevenue) / COUNT(DISTINCT i.transactionId) as avg_order_value,
  SUM(i.itemRevenue) / SUM(i.itemPurchaseQuantity) as avg_price_per_unit
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_transaction_items_*` i
WHERE i.date >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY)
GROUP BY i.itemName
HAVING units_sold > 10
ORDER BY revenue DESC;
```

### 5. Mobile vs Desktop Performance
```sql
SELECT
  deviceCategory,
  SUM(sessions) as sessions,
  SUM(screenPageViews) as pageviews,
  SUM(screenPageViews) / SUM(sessions) as pages_per_session,
  SUM(ecommercePurchases) as purchases,
  SUM(ecommercePurchases) / SUM(sessions) as conversion_rate,
  SUM(purchaseRevenue) as revenue,
  SUM(purchaseRevenue) / SUM(sessions) as revenue_per_session
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_data_browser_os_device_*`
GROUP BY deviceCategory
ORDER BY sessions DESC;
```

---

## Notes

1. **Date Sharding:** Tables are created per date range. Use wildcard `*` in queries to union all periods.

2. **Currency:** All revenue fields are in **EUR** (Euros) as configured in GA4.

3. **Time Zone:** Data follows **Europe/Rome** timezone as configured in GA4.

4. **Data Freshness:** Historical backfill captures data with 2-day lag (GA4 processing time).

5. **Custom Dimensions:** This schema uses standard GA4 dimensions. Custom dimensions can be added by modifying the N8N workflow.

6. **Performance:** For large date ranges, use date filters (`WHERE date >= '2025-01-01'`) to avoid scanning unnecessary partitions.

---

**Generated:** 2025-10-24
**Workflow Version:** N8N-GA4-Backfill-Workflow v1.0
**Documentation Version:** 1.0
