# Data Quality Log

Documents every issue found during the data audit phase, the action taken, and the rationale. All original data is preserved in the `Raw Data` sheet of the workbook.

---

## Issues Found & Actions Taken

### 1. Duplicate Employee Records

| Attribute | Detail |
|---|---|
| **Issue** | 10 EmpIDs appeared twice — 20 rows total representing the same employees entered twice |
| **Affected rows** | RM1461, RM1462, RM1463, RM1464, RM1465, RM1466, RM1467, RM1468, RM1469, RM1470 |
| **Action** | Kept first occurrence of each duplicate. Removed second occurrence using Power Query → Remove Duplicates on EmpID |
| **Result** | Row count reduced from 1,480 → 1,470 |
| **Audit trail** | All 1,480 original rows preserved in `Raw Data` sheet |

---

### 2. MonthlyIncome Stored as Text

| Attribute | Detail |
|---|---|
| **Issue** | `MonthlyIncome` field formatted as `"$5,993.00"` — currency symbol and comma prevent numeric operations |
| **Action** | Added `MonthlyIncome_Clean` as a live Excel formula: `=VALUE(SUBSTITUTE(SUBSTITUTE([@MonthlyIncome],"$",""),",",""))` |
| **Result** | Numeric values available for AVERAGE, PivotTable calculations, and chart axes |
| **Note** | Original `MonthlyIncome` column retained unchanged in Cleaned Data for reference |

---

### 3. Missing Values — YearsWithCurrManager

| Attribute | Detail |
|---|---|
| **Issue** | 57 blank values in `YearsWithCurrManager` (~3.9% of cleaned dataset) |
| **Action** | Retained as blank — no imputation applied |
| **Rationale** | PivotTable averages and Excel AVERAGE formulas automatically exclude blank cells, producing accurate means without imputation bias. Imputing with median (≈4 years) would artificially narrow the distribution |
| **Dashboard note** | Flagged as a known data limitation in the insights narrative |

---

### 4. Constant Columns Removed

| Attribute | Detail |
|---|---|
| **Issue** | Three columns carry no analytical information: `EmployeeCount` (always 1), `Over18` (always "Y"), `StandardHours` (always 80) |
| **Action** | Removed from `Cleaned Data` sheet using Power Query → Remove Columns |
| **Result** | Column count reduced from 38 → 35 in working table |
| **Audit trail** | All three columns retained in `Raw Data` sheet |

---

### 5. Irreconcilable Compensation Fields

| Attribute | Detail |
|---|---|
| **Issue** | `DailyRate`, `HourlyRate`, `MonthlyRate`, and `MonthlyIncome` do not reconcile mathematically with each other |
| **Nature** | Known artifact of the original IBM synthetic dataset — these fields were generated independently, not derived from each other |
| **Action** | `MonthlyIncome_Clean` designated as sole compensation source of truth for all analysis. `DailyRate`, `HourlyRate`, and `MonthlyRate` hidden in Cleaned Data sheet |
| **Implication** | All compensation findings (income bands, pay gradient analysis) are based exclusively on `MonthlyIncome_Clean` |

---

### 6. PerformanceRating — Restricted Range

| Attribute | Detail |
|---|---|
| **Issue** | `PerformanceRating` only contains values 3 and 4 (scale is 1–4) — no low performers recorded |
| **Nature** | Dataset characteristic, not a data entry error |
| **Action** | No correction applied. Noted in Data Dictionary and flagged in dashboard |
| **Implication** | `PerformanceRating` has minimal discriminatory power in this dataset and is not used as a primary analytical variable |

---

## Summary

| Issue | Rows Affected | Action | Status |
|---|---|---|---|
| Duplicate records | 10 rows removed | Power Query dedup on EmpID | ✅ Resolved |
| MonthlyIncome as text | All 1,470 rows | Derived numeric column via formula | ✅ Resolved |
| YearsWithCurrManager blanks | 57 rows | Retained as blank | ✅ Documented |
| Constant columns | 3 columns | Removed from working table | ✅ Resolved |
| Compensation field conflict | 3 columns | Hidden, single source of truth designated | ✅ Resolved |
| PerformanceRating range | All rows | Noted, not corrected | ✅ Documented |
