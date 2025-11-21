# loan-automation-los-analytics-case-study
STP Optimization Case Study- SQL and Event-Log Analytics Measuring the Impact of Automated Loan Issuance in LOS (Manual Review Drop 100% → 20-30%; Processing Time 30-60 min → &lt;1 min)
# 📊 Loan Automation in LOS - Data & Analytics Case Study

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
📈 4. Dashboard Validation (BI Layer)

BI tools used:

Power BI

Looker Studio

KPIs Visualized:

Manual Review %

STP rate

Average processing time

Median / P90

Automated vs Manual volume split

Decision engine pass/fail rate

Exception triggers

Data pipelines through SQL views:

vw_repeat_loan_metrics

vw_processing_time_metrics

vw_stp_split

🧱 5. Architecture (ASCII Diagram)
+-----------+       +-------------+       +-------------+       +-------------------+
| Customer  | ----> |   LOS UI    | ----> |   LOS BE    | ----> |  Decision Engine  |
+-----------+       +-------------+       +-------------+       +---------+---------+
                                                                         |
                                                                         v
                                                              +----------------------+
                                                              |  External Systems    |
                                                              | (Bureau, Registry,   |
                                                              |  Scoring, etc.)      |
                                                              +----------+-----------+
                                                                         |
                                                                         v
                                                              +----------------------+
                                                              |  Auto Disbursement   |
                                                              |       Service (ADS)  |
                                                              +----------------------+

🧭 6. Automated vs Manual Workflow (ASCII)
                    +------------------+
                    |  Repeat Loan?    |
                    +--------+---------+
                             |
                             v
                  +-------------------------+
                  | Run DE Ruleset (LTV,    |
                  | PTI, Score, Checklist)  |
                  +------------+------------+
                               |
                 +-------------+--------------+
                 |                            |
                 v                            v
      +---------------------+        +------------------------+
      | All rules passed?   |        | Rule failed / Missing  |
      +----------+----------+        | data → Manual Review   |
                 | Yes               +-----------+------------+
                 v                               |
    +-------------------------------+             |
    | Auto-Issue (STP, <1 minute)   |             |
    +-------------------------------+             |
                 |                               |
                 v                               v
    +-------------------------------+   +-----------------------------+
    | Auto Disbursement (ADS)       |   | Manual Review (20–30%)     |
    +-------------------------------+   +-----------------------------+

🏁 7. Conclusion

Automation delivered significant operational improvements:

Manual review cut from 100% → 20–30%

Processing time reduced from 30–60 minutes → <1 minute

STP coverage expanded to 70–80%

Better consistency, fewer error cases, improved customer experience

This case study demonstrates how SQL analytics + event logs + BI dashboards can quantify real business impact.

📎 Files in This Repository
/sql
    manual_review_analysis.sql
    processing_time_analysis.sql
    stp_metrics.sql

/docs
    architecture_ascii.txt
    workflow_ascii.txt
    methodology.md

README.md
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

Person(customer, "Customer", "Applies for micro-segment loan product")

System(LOS, "Loan Origination System (LOS)", "Handles application intake, validation, risk routing and disbursement initiation")

System_Ext(decisionEngine, "Decision Engine", "Calculates LTV/PTI/DTI, behavioral score and returns decision")

System_Ext(externalSystems, "External Systems", "Bureau, Registry, Scoring, etc.")

System_Ext(ADS, "Auto Disbursement Service (ADS)", "Executes automatic disbursement after approval")

Rel(customer, LOS, "Submits application / checks status")
Rel(LOS, decisionEngine, "Sends application data for decision")
Rel(decisionEngine, externalSystems, "Requests external data")
Rel(decisionEngine, LOS, "Returns decision + reasons")
Rel(LOS, ADS, "Triggers auto disbursement for approved loans")

@enduml
