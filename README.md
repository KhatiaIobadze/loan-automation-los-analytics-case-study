# loan-automation-los-analytics-case-study
STP Optimization Case Study- SQL and Event-Log Analytics Measuring the Impact of Automated Loan Issuance in LOS (Manual Review Drop 100% → 20-30%; Processing Time 30-60 min → &lt;1 min)
# 📊 Loan Automation in LOS — Data & Analytics Case Study

This repository contains the analytical framework used to measure the impact of **Automated Loan Issuance** within the **Loan Origination System (LOS)**.  
It focuses on **SQL-based data extraction**, **event-log analytics**, **STP performance measurement**, and **BI dashboard validation**.

---

## 📌 Project Type
**Data / Analytics Case Study**  
SQL • Event Logs • KPI Measurement • BI Dashboards • STP Analysis

---

## 🚀 Summary of Impact

Automation reduced operational load and significantly improved processing speed:

| Metric | Before Automation | After Automation |
|--------|------------------|------------------|
| Manual Review Rate (Repeat Loans) | ~100% | **20–30%** |
| Processing Time | 30–60 min | **< 1 min** |
| STP Rate | ~0% | **70–80%** |

These results were validated through SQL queries, LOS event logs, and BI dashboards.

---

## 🧩 1. Objective

Measure the real-world efficiency impact of **automated product issuance** in the LOS system by analyzing:

- Manual review reduction  
- Processing time improvements  
- Straight-Through Processing (STP) performance  
- Decision Engine success rate  
- Exception paths and manual assignments  

---

## 📂 2. Data Sources

### **Core Tables**

| Table | Description |
|-------|-------------|
| `loan_applications` | Application-level master data |
| `application_events` | Event logs with timestamps |
| `decision_engine_logs` | Rule-engine results (LTV, PTI, score, etc.) |
| `customer_profile` | Borrower history, repeat-loan flags |

### **Key Fields Used**

- `application_id`  
- `customer_id`  
- `is_repeat_loan`  
- `is_manual_review`  
- `created_timestamp`  
- `final_decision_timestamp`  
- `event_type`  
- `event_timestamp`  

---

## ⚙️ 3. Methodology

### **3.1 Measuring Manual Review Rate**

Goal: quantify how automation reduced manual workload for repeat loans.

**SQL Logic (simplified):**

```sql
WITH repeat_loans AS (
    SELECT
        application_id,
        DATE(created_timestamp) AS dt,
        is_manual_review
    FROM loan_applications
    WHERE is_repeat_loan = 1
)
SELECT
    dt,
    ROUND(
        100.0 * SUM(CASE WHEN is_manual_review = 1 THEN 1 END) / COUNT(*),
        2
    ) AS manual_review_rate_pct
FROM repeat_loans
GROUP BY dt
ORDER BY dt;
