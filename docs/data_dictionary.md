# Data Dictionary

Complete column definitions for the HR Attrition dataset.

---

## Original Columns (38)

| Column | Type | Scale / Values | Description |
|---|---|---|---|
| `EmpID` | Text | — | Unique employee identifier |
| `EmployeeNumber` | Number | — | Internal HRIS employee number |
| `Age` | Number | 18–60 | Employee age in years |
| `AgeGroup` | Category | e.g. 25-34 | Pre-binned age range |
| `Attrition` | Category | Yes / No | **Target variable** — did the employee leave? |
| `BusinessTravel` | Category | Non-Travel, Travel_Rarely, Travel_Frequently | Frequency of business travel |
| `DailyRate` | Number | — | Daily pay rate (synthetic field — does not reconcile with MonthlyIncome) |
| `Department` | Category | HR, R&D, Sales | Department of employment |
| `DistanceFromHome` | Number | 1–29 | Distance from home to office (miles) |
| `Education` | Ordinal | 1–5 | 1=Below College, 2=College, 3=Bachelor, 4=Master, 5=Doctor |
| `EducationField` | Category | — | Field of study |
| `EmployeeCount` | Constant | Always 1 | Dropped from Cleaned Data — no analytical value |
| `EnvironmentSatisfaction` | Ordinal | 1–4 | 1=Low, 2=Medium, 3=High, 4=Very High |
| `Gender` | Category | Male / Female | Employee gender |
| `HourlyRate` | Number | — | Hourly pay rate (synthetic field — does not reconcile with MonthlyIncome) |
| `JobInvolvement` | Ordinal | 1–4 | 1=Low, 2=Medium, 3=High, 4=Very High |
| `JobLevel` | Ordinal | 1–5 | Seniority level (1=entry, 5=executive) |
| `JobRole` | Category | 9 roles | Specific job role title |
| `JobSatisfaction` | Ordinal | 1–4 | 1=Low, 2=Medium, 3=High, 4=Very High |
| `MaritalStatus` | Category | Single, Married, Divorced | Marital status |
| `MonthlyIncome` | Text (currency) | "$X,XXX.00" | Stored as formatted text — use `MonthlyIncome_Clean` for analysis |
| `MonthlyRate` | Number | — | Monthly rate (synthetic field — does not reconcile with MonthlyIncome) |
| `NumCompaniesWorked` | Number | 0–9 | Prior employers before joining this company |
| `Over18` | Constant | Always "Y" | Dropped from Cleaned Data — no analytical value |
| `OverTime` | Category | Yes / No | Whether employee works overtime — **strongest attrition predictor** |
| `PercentSalaryHike` | Number | 11–25 | Most recent salary increase percentage |
| `PerformanceRating` | Ordinal | 3–4 only | Only values 3 and 4 present in this dataset (not an error — dataset characteristic) |
| `RelationshipSatisfaction` | Ordinal | 1–4 | 1=Low, 2=Medium, 3=High, 4=Very High |
| `SalarySlab` | Category | Upto 5k, 5k-10k, 10k-15k, 15k+ | Pre-binned income range (cross-check against MonthlyIncome_Clean) |
| `StandardHours` | Constant | Always 80 | Dropped from Cleaned Data — no analytical value |
| `StockOptionLevel` | Ordinal | 0–3 | Stock option tier (0=none, 3=highest) |
| `TotalWorkingYears` | Number | 0–40 | Total years of professional work experience |
| `TrainingTimesLastYear` | Number | 0–6 | Number of training sessions attended last year |
| `WorkLifeBalance` | Ordinal | 1–4 | 1=Bad, 2=Good, 3=Better, 4=Best |
| `YearsAtCompany` | Number | 0–40 | Tenure at current company |
| `YearsInCurrentRole` | Number | 0–18 | Years in current role |
| `YearsSinceLastPromotion` | Number | 0–15 | Years since most recent promotion |
| `YearsWithCurrManager` | Number | 0–17 | Years working with current manager — **57 missing values** |

---

## Derived Columns (added in Cleaned Data sheet)

| Column | Formula | Purpose |
|---|---|---|
| `MonthlyIncome_Clean` | `=VALUE(SUBSTITUTE(SUBSTITUTE([@MonthlyIncome],"$",""),",",""))` | Converts MonthlyIncome from text to numeric for use in calculations |
| `AttritionFlag` | `=IF([@Attrition]="Yes",1,0)` | 1/0 encoding — average of this column = attrition rate for any group |
| `OverTimeFlag` | `=IF([@OverTime]="Yes",1,0)` | 1/0 encoding for overtime analysis |
| `TenureBand` | Nested IF on YearsAtCompany | Groups tenure into 5 career-stage bands: 0-1 yrs, 2-4 yrs, 5-9 yrs, 10-19 yrs, 20+ yrs |
| `IncomeCategory` | Nested IF on MonthlyIncome_Clean | Groups income into 5 bands: Under $3K, $3K-$6K, $6K-$10K, $10K-$15K, $15K+ |
| `SatisfactionIndex` | `=ROUND(AVERAGE([@EnvironmentSatisfaction],[@JobSatisfaction],[@RelationshipSatisfaction],[@WorkLifeBalance]),1)` | Equal-weighted composite of all four satisfaction dimensions |

---

## Notes on Compensation Fields

This dataset contains four separate "rate" fields that **do not mathematically reconcile** with each other:

- `DailyRate`
- `HourlyRate`
- `MonthlyRate`
- `MonthlyIncome`

This is a known characteristic of the original IBM synthetic dataset. **`MonthlyIncome_Clean` is used as the single source of truth** for all compensation analysis in this project. The other three fields are retained in Raw Data but hidden in Cleaned Data.
