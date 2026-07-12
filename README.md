# insurance-claims-sla-analytics
Excel + SQL + Power BI analysis of insurance claims SLA and regional processing delays.

Tools: Excel · SQL (SQLite, via Jupyter Notebook) · Power BI (DAX)

## Pipeline

```
Raw claims data (Kaggle)
        ↓
Excel: validate & clean
        ↓
SQL: query & analyze SLA bottlenecks
        ↓
Power BI: visualize KPIs & operational findings
```

 1. Excel - data validation & cleaning
- Removed 45 duplicate `Claim_ID` records
- Checked and handled missing values:
  - Recovered missing `Region` via `XLOOKUP` against customer master data
  - Retained missing `Settlement_Date` values as a signal for unresolved claims (not treated as errors)
- Standardized `Claim_Type` categories
- Added validation columns to flag remaining data quality issues

 2. SQL - SLA & KPI analysis (SQLite in Jupyter Notebook)
- Imported the cleaned dataset and built SQL tables
- Ran data quality checks
- Calculated core KPIs and SLA performance
- Broke down performance by region, workflow type, and region × workflow

 3. Power BI - dashboard (3 pages)
- *Executive Overview* - overall KPIs, claims trend, claims by status, policy-wise claim amount
- *Claims & SLA Performance* - SLA breach analysis, regional performance, workflow performance, SLA heatmap
- *Delay Root-Cause Analysis* - delay by workflow, delay by region, region × workflow matrix

## Key findings

Data quality
- 8,000 unique claims confirmed after removing 45 duplicate records
- 55 missing Claim Amount values and 70 missing Region values identified
- Missing Region values enriched using customer master data; 1,968 missing Settlement Dates retained as-is, since they correspond to unresolved claims rather than data errors
- Inconsistent Claim Type categories standardized

SLA performance
- *East* recorded the highest SLA breach rate (66.96–67.0%) and the longest average processing time (16.81 days), followed by Central (58.5%)
- *Fraud Review* had the highest SLA breach rate (99.63%) and average processing time (25.98 days), followed by Manual Review (89.55%)
- *Life* policies accounted for the highest total claim amount (₹471.76 million)

Root cause
- East's high breach rate isn't explained by its workflow mix - it actually had the *lowest* share of review-intensive workflows (29.34%)
- Despite that, every workflow type consistently took longer to process in East than in other regions, pointing to region-level operational inefficiency rather than case complexity
- Several agents showed consistently higher average processing times, suggesting workload allocation, training, or process complexity worth reviewing

Recommendation: prioritize operational review in the East and Central regions, particularly for review-intensive workflows.

## Repo structure
```
├── excel/          # cleaned dataset + validation workbook
├── sql/            # SQLite queries / notebook
└── README.md
```

How to reproduce
1. Open the Excel workbook in `excel/` to see the cleaning and validation steps
2. Run the notebook in `sql/` to reproduce the SLA and KPI queries
