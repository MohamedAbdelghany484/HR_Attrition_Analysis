# HR Attrition Analysis

An Excel-based analysis of why employees leave, built on IBM's HR Analytics Employee Attrition dataset (1,470 employees, 13 attributes). I built this to practice the kind of Excel work a Data Analyst actually does day to day: cleaning data, building reusable lookup logic, summarizing with PivotTables, and packaging the result into a dashboard someone could actually use.

## What I was trying to answer

Who is most likely to leave, and what factors actually drive it? I broke that into a few concrete questions:
- Does department, age, income, or marital status affect attrition?
- Do people who leave report lower job satisfaction?
- Does commute distance or number of past employers play a role?

## How the workbook is organized

| Sheet | What it does |
|---|---|
| `RAW_DATA` | The original dataset, untouched |
| `Clean_Data` | Linked from RAW_DATA, plus calculated helper columns |
| `ASSUMPTIONS` | Notes on threshold decisions |
| `Lookup_Tables` | Reference tables for classifying employees (used by VLOOKUP/XLOOKUP) |
| `PivotTables` | Attrition rate broken down by each factor |
| `Dashboard` | KPI cards, charts, and a key insights summary |

I checked the data first (no missing values, no duplicates — it's a clean dataset), so most of the work went into building helper columns that turn raw numbers into something analyzable:

- **AttritionFlag** — converts Yes/No into 1/0 so I can average it directly to get a percentage
- **AgeGroup, IncomeBand, TenureGroup** — bucket continuous values into bands using VLOOKUP and XLOOKUP against the Lookup_Tables sheet, instead of burying thresholds inside nested IFs
- **SatisfactionAvg** — averages three separate satisfaction scores (environment, job, work-life balance) into one composite score
- **EducationLabel** and satisfaction labels — VLOOKUP/XLOOKUP translating IBM's numeric codes (1-5) into readable text

I kept the band thresholds in `Lookup_Tables` rather than hardcoded in formulas, so I could change, say, what counts as "low income" by editing one cell instead of hunting through hundreds of formulas.

A small thing I ran into worth mentioning: when I first wrote a formula to average attrition for the "<25" age group, it kept breaking. Turns out Excel reads a criteria string starting with `<` as a comparison operator, not literal text — so `AVERAGEIF(range, "<25", ...)` doesn't match the label "<25" at all. The fix was forcing an exact match with `"="&cell`. Small bug, but the kind of thing you only learn by hitting it.

## What I found

- **Age is the strongest signal.** Employees under 25 leave at **39%**, compared to roughly 10% for employees aged 35-54 — almost a 4x difference.
- **Income matters a lot.** Low-income employees leave at **28.6%**, versus **10.8%** for high-income employees.
- **Marital status matters too.** Single employees leave at **25.5%**, about double the rate of married employees (12.5%).
- **Satisfaction and commute both move with attrition.** Employees who left report lower average satisfaction (2.5 vs 2.8) and commute about 1.7 km farther on average than employees who stayed.

Put together, the clearest at-risk group is young, single, lower-income employees — and that's compounded further by lower satisfaction and longer commutes. My recommendation in the dashboard is to focus retention efforts there: review entry-level compensation and consider commute flexibility (remote/hybrid days) for employees with long commutes.

## Skills used

Excel formulas (IF, AVERAGEIF, VLOOKUP, XLOOKUP, AVERAGE, COUNTIF), PivotTables, PivotCharts, and dashboard design (KPI cards, chart consistency, written insights).

## Dataset source

[IBM HR Analytics Employee Attrition & Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset) (via Kaggle).
