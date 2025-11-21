# 📊 Loan Automation in LOS - Data & Analytics Case Study

This repository presents a complete **Data/Analytics case study** evaluating the impact of automated loan issuance within the Loan Origination System (LOS).  
The project includes technical architecture, workflow mapping, SQL-based KPI calculations, event-log analytics, and BI dashboard validation.

---

## 🧭 Project Type
**Data / Analytics Case Study**  
Tools: SQL • Event Logs • BI Dashboards • UML/BPMN • Architecture Modeling

---

# 🏗 1. Architecture Overview

The following diagram illustrates how LOS components, external systems, and automation services interact during the loan issuance process.

![Architecture Diagram](./docs/architecture.png)

**Key Components**
- **LOS UI & Backend** — handles application intake  
- **Decision Engine** — runs rule validation (LTV, PTI, score, checklist)  
- **External Systems** — bureau, registry, scoring data sources  
- **Auto Disbursement Service (ADS)** — performs instant loan disbursement  
- **Core Banking** — final posting and account updates  

---

# 🔀 2. Automated vs Manual Workflow

Repeat-loan applications follow one of two routing paths:

### **Automated Path (STP — under 1 minute)**
- All Decision Engine rules pass  
- LOS auto-issues the loan  
- ADS executes automatic disbursement  

### **Manual Review Path (20–30% of repeat loans)**
- Triggered by missing data or failed rule checks  
- Routed to a human checker  
- Before automation: **100%** of repeat loans required manual review  

![Workflow Diagram](./docs/workflow.png)

---

# 📡 3. Sequence Diagram (System Interaction Flow)

This UML sequence diagram visualizes message flow between system components:

- Customer  
- LOS UI  
- LOS Backend  
- Decision Engine  
- External Systems  
- ADS  
- Core Banking  

**Upload your PNG and update this path:**

![Sequence Diagram](./docs/sequence.png)

---

# 🔁 4. Data & Analytics Pipeline

This diagram shows how operational data flows into SQL-based analytics and BI dashboards:

1. LOS generates `loan_applications` data  
2. Decision Engine produces rule results  
3. `application_events` logs timestamps  
4. SQL aggregates KPIs  
5. Dashboard visualizes performance over time  

**Upload your PNG and update this path:**

![Analytics Pipeline](./docs/pipeline.png)

---

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
```

---

## 6. KPI Results (Before vs After Automation)

Measures automation impact across core operational indicators  
using SQL calculations and BI dashboard validation.

| KPI | Before Automation | After Automation | Measurement Method |
|-----|------------------|------------------|---------------------|
| **Manual Review Rate (Repeat Loans)** | ~100% | **20–30%** | SQL aggregation on `is_manual_review` |
| **Processing Time** | 30–60 minutes | **< 1 minute** | Event timestamps (`APP_CREATED` → `AUTO_ISSUED`) |
| **STP Rate** | ~0% | **70–80%** | Auto-issued / total repeat loans |
| **Human Error Rate** | High | **Significantly reduced** | Exception logs and manual correction volume |
