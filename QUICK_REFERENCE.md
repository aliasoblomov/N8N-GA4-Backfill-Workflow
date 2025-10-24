# GA4 BigQuery Tables - Quick Reference

## 📊 13 Tables Available

### Traffic & Acquisition (4 tables)
| Table | Key Dimensions | Primary Use Case |
|-------|---------------|------------------|
| `ga4_data_session_channel_group` | Channel Group | Channel performance comparison |
| `ga4_data_session_source_campaign_medium` | Source, Medium, Campaign | Campaign tracking & attribution |
| `ga4_data_first_user_source_medium` | First User Source/Medium | First-touch attribution |
| `ga4_data_first_user_channel_group` | First User Channel | New user acquisition tracking |

### Geographic & Device (2 tables)
| Table | Key Dimensions | Primary Use Case |
|-------|---------------|------------------|
| `ga4_data_country_language_city` | Country, City, Language | Geographic analysis & localization |
| `ga4_data_browser_os_device` | Browser, OS, Device | Mobile vs Desktop, UX optimization |

### E-commerce (2 tables)
| Table | Key Dimensions | Primary Use Case |
|-------|---------------|------------------|
| `ga4_transaction_items` | Transaction ID, Item Name | Granular transaction analysis |
| `ga4_data_item_name` | Item Name | Product performance tracking |

### Advertising (1 table)
| Table | Key Dimensions | Primary Use Case |
|-------|---------------|------------------|
| `ga4_ads_data` | Source, Campaign | ROAS analysis, ad optimization |

### Engagement & Behavior (4 tables)
| Table | Key Dimensions | Primary Use Case |
|-------|---------------|------------------|
| `ga4_all_metrics_data` | Date only | Daily KPI dashboard |
| `ga4_event_metrics_data` | Event Name | Conversion funnel, custom events |
| `ga4_page_location_data` | Page URL | Content performance |
| `ga4_landing_page_data` | Landing Page URL | Entry point optimization |

---

## 🎯 Common Use Cases - Which Table to Use?

| What You Need | Use This Table |
|---------------|---------------|
| Daily revenue trends | `ga4_all_metrics_data` |
| Which channels drive sales | `ga4_data_session_channel_group` |
| Campaign ROAS | `ga4_ads_data` |
| Top selling products | `ga4_data_item_name` or `ga4_transaction_items` |
| Mobile vs Desktop conversion | `ga4_data_browser_os_device` |
| Top countries by revenue | `ga4_data_country_language_city` |
| Best landing pages | `ga4_landing_page_data` |
| First-touch attribution | `ga4_data_first_user_channel_group` |
| Conversion funnel | `ga4_event_metrics_data` |
| UTM parameter analysis | `ga4_data_session_source_campaign_medium` |

---

## 💡 Query Tips

### Union All Periods
```sql
SELECT * FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_all_metrics_data_*`
WHERE date >= '2025-01-01'
```

### Filter by Date Range
```sql
WHERE date BETWEEN '2025-01-01' AND '2025-01-31'
```

### Sum Across Periods
```sql
SELECT
  SUM(sessions) as total_sessions,
  SUM(purchaseRevenue) as total_revenue
FROM `ga4-pavaglione-cosmetics.Ga4backfill.ga4_all_metrics_data_*`
```

---

## 📁 File Naming Convention

Tables use date range suffix: `{table_name}_{YYYYMMDD}_{YYYYMMDD}`

**Examples:**
- January 2025: `ga4_all_metrics_data_20250101_20250131`
- Jan-Feb 2025: `ga4_all_metrics_data_20250101_20250228`
- Full year: `ga4_all_metrics_data_20250101_20251231`

---

## 📈 Key Metrics

| Metric | Tables Available | Type |
|--------|-----------------|------|
| Sessions | 8 tables | INT64 |
| Users | 11 tables | INT64 |
| Revenue | 11 tables | FLOAT64 (EUR) |
| Purchases | 11 tables | INT64 |
| Engagement Rate | 3 tables | FLOAT64 (0-1) |
| Ad Cost | 1 table (ads_data) | FLOAT64 (EUR) |
| ROAS | 1 table (ads_data) | FLOAT64 |

---

**Project:** `ga4-pavaglione-cosmetics`
**Dataset:** `Ga4backfill`
**Location:** US
**Currency:** EUR
**Timezone:** Europe/Rome

**Full Documentation:** See `BIGQUERY_SCHEMA_DOCUMENTATION.md`
**CSV Reference:** See `bigquery_schema_reference.csv`
