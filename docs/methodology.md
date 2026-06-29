# Methodology

This project follows the **CRISP-DM** (Cross-Industry Standard Process for Data Mining) framework, adapted for an Excel-only analytics workflow.

---

## Framework Overview

```
Business Understanding
        ↓
Data Understanding
        ↓
Data Preparation    ← Phase 1
        ↓
Exploratory Analysis ← Phase 2
        ↓
Dashboard Build     ← Phase 3
        ↓
Insights Narrative  ← Phase 4
```

---

## Phase 1 — Data Understanding & Preparation

### Data Understanding
- Audited all 38 columns for type, range, uniqueness, and analytical relevance
- Identified target variable: `Attrition` (Yes/No) — 16.2% positive rate
- Noted dataset-specific quirks: irreconcilable compensation fields, restricted PerformanceRating range, MonthlyIncome text formatting

### Data Preparation
All cleaning performed using two complementary tools:

**Power Query (structural cleaning):**
- Removed 3 constant columns
- Deduplicated on EmpID (1,480 → 1,470 rows)
- Converted MonthlyIncome from text to numeric
- Preserved original data in Raw Data sheet as audit trail

**Excel Formulas (derived analytical columns):**
- `AttritionFlag` — 1/0 encoding for rate calculation
- `OverTimeFlag` — 1/0 encoding for overtime analysis
- `TenureBand` — nested IF grouping YearsAtCompany into 5 career-stage bands
- `IncomeCategory` — nested IF grouping MonthlyIncome into 5 pay bands
- `SatisfactionIndex` — equal-weighted average of 4 satisfaction dimensions

---

## Phase 2 — Exploratory Analysis

### PivotTable Design Principle
All attrition rate calculations use `AttritionFlag` summarised as **Average** — not Count, Sum, or % of Column Total.

**Why this matters:** Because AttritionFlag is coded 1 (left) or 0 (stayed), its average for any group equals that group's attrition rate, using the group's own headcount as denominator automatically. The % of Column Total approach produces composition percentages (share of total leavers), not rates — a common and misleading error.

**Verification check:** Every PivotTable's Grand Total row should read 16.2% — matching the overall company rate from the baseline pivot. Any deviation signals a data connection or filter error.

### PivotTables Built (10 total)

| # | Name | Analytical Question |
|---|---|---|
| 1 | PT_OverallAttrition | What is the baseline attrition rate? |
| 2 | PT_ByDept | Where is attrition highest? |
| 3 | PT_ByOvertime | How much does overtime increase risk? |
| 4 | PT_ByRole | Which specific roles are most vulnerable? |
| 5 | PT_ByTenure | When in the career lifecycle do employees leave? |
| 6 | PT_IncomeByRole | What is the pay distribution across roles? |
| 7 | PT_IncomeVsAttrition | Does higher pay correlate with lower attrition? |
| 8 | PT_SatisfactionByAttrition | Did leavers score lower on satisfaction metrics? |
| 9 | PT_Demographics | What is the workforce composition? |
| 10 | PT_AgeVsAttrition | Which career stage carries the highest attrition risk? |

---

## Phase 3 — Dashboard Build

### Design Principles
- **Single visual argument:** Every element answers one of three questions — *What is happening? Where and why? Let me explore.*
- **Three-layer structure:** KPI cards (headline numbers) → Charts (breakdowns) → Slicers (interactivity)
- **Colour-coded thresholds:** Bars above company average (16.2%) coloured red; below coloured green — enables 2-second pattern reading

### Dashboard Components

**KPI Cards (5):**
- Total Headcount
- Overall Attrition Rate
- Average Monthly Income
- Average Years at Company
- Overtime Attrition Rate

**Charts (8):**
- Attrition by Department (horizontal bar)
- Attrition by Job Role (horizontal bar, sorted descending, colour-coded by threshold)
- Attrition by Tenure Band (column, chronological order)
- Attrition by Age Group (column, youngest to oldest)
- Income Category vs Attrition (column — pay gradient test)
- Satisfaction Scores: Leavers vs Stayers (clustered bar — 4 metrics × 2 groups)
- OverTime vs Attrition (column — 2-bar comparison with annotation)
- Workforce Composition by Gender (stacked bar)

**Slicers (3, connected to all 10 PivotTables):**
- Department
- Gender
- AgeGroup

---

## Phase 4 — Insights Narrative

### Structure
Each finding follows a four-layer pyramid:
1. **What** — The observation
2. **How bad** — Magnitude, benchmark comparison
3. **Why** — Diagnosis, contributing factors
4. **So what** — Business implication and recommended action

### Caveats Documented
- Satisfaction scores are point-in-time — causal direction (did low satisfaction *cause* departure, or did the decision to leave *reduce* satisfaction scores?) cannot be confirmed from this dataset
- YearsWithCurrManager has 57 missing values — manager-level analysis should be treated with appropriate caution
- PerformanceRating restricted range limits its use as a discriminating variable
- Replacement cost estimate ($9.3M) uses a 50% of annual salary multiplier — a conservative industry assumption; actual costs may be higher

---

## Tools & Techniques Reference

| Technique | Tool | Applied In |
|---|---|---|
| Data deduplication | Power Query | Phase 1 |
| Type conversion (text → number) | Power Query + Excel formula | Phase 1 |
| Derived column creation | Excel nested IF, AVERAGE | Phase 1 |
| Attrition rate calculation | PivotTable + Average of 1/0 flag | Phase 2 |
| Threshold colour-coding | Manual chart formatting | Phase 3 |
| Cross-slicer connectivity | Report Connections | Phase 3 |
| Business impact quantification | Manual calculation with documented assumptions | Phase 4 |
