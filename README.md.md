# HR Attrition Dashboard (Power BI)

A Power BI dashboard that analyzes **1,000 employees across 8 departments** to answer three questions about **who leaves the company, and what they have in common**.

![Dashboard preview](images/dashboard.png)

> **Note:** The dataset is a free *sample* dataset, not real company data. Treat the findings as a practice case study, not as real-world HR advice.

---

## Business questions

1. **Where is attrition highest?** (by department)
2. **Do leavers look different from stayers?** (satisfaction and performance)
3. **Is pay linked to leaving?** (salary bands)

---

## Dataset

- **Source:** [Employee HR Records sample data, TrumpExcel](https://trumpexcel.com/excel-sample-data/)
- **Size:** 1,000 employees, 10 columns, 8 departments, 39 job titles
- **Columns:** Employee ID, Full Name, Gender, Department, Job Title, Hire Date, Annual Salary, Performance Rating, Satisfaction Score, Employment Status
- **Hire dates:** Jan 2015 to Jun 2025
- **Employment Status:** Active (724), Resigned (206), Terminated (70)
- **Observed scales:** Performance Rating 1–5, Satisfaction Score 1–9
- **Data quality:** no blank cells and no duplicate Employee IDs

---

## Definitions

| Term | Definition |
|---|---|
| **Attrition** | Employees whose status is **Resigned or Terminated** (276 of 1,000) |
| **Attrition Rate** | Leavers ÷ Headcount |
| **Satisfaction Band** | Low (1–3), Medium (4–6), High (7–9) |
| **Performance Band** | Low (1–2), Medium (3), High (4–5) |
| **Salary Band** | Under 60K, 60K–90K, 90K–120K, 120K+ |

The band cut-offs are my own choice and could be set differently.

---

## Key findings

**Overall:** 1,000 employees, 276 leavers, **27.6% attrition** (20.6% resigned, 7.0% terminated). Average salary is $84,929, average satisfaction 5.29, and average performance 3.48.

### 1. Attrition by department

| Department | Headcount | Leavers | Attrition |
|---|---|---|---|
| Operations | 97 | 34 | 35.1% |
| Customer Support | 91 | 28 | 30.8% |
| Sales | 165 | 49 | 29.7% |
| Legal | 57 | 16 | 28.1% |
| Finance | 137 | 37 | 27.0% |
| Marketing | 122 | 32 | 26.2% |
| Engineering | 263 | 64 | 24.3% |
| Human Resources | 68 | 16 | 23.5% |

Operations has the highest attrition and also the lowest average satisfaction (5.03). Human Resources has the lowest attrition and the highest average satisfaction (5.63). Some departments are small (Legal 57, HR 68), so differences between them should be read with care.

### 2. Satisfaction and performance

| Satisfaction band | Headcount | Leavers | Attrition |
|---|---|---|---|
| Low (1–3) | 172 | 110 | 64.0% |
| Medium (4–6) | 562 | 133 | 23.7% |
| High (7–9) | 266 | 33 | 12.4% |

| Performance band | Headcount | Leavers | Attrition |
|---|---|---|---|
| Low (1–2) | 135 | 77 | 57.0% |
| Medium (3) | 354 | 113 | 31.9% |
| High (4–5) | 511 | 86 | 16.8% |

- Attrition falls steeply as satisfaction and performance rise.
- Employees who resigned scored lower on satisfaction (4.44) than active employees (5.70), and terminated employees scored lowest (3.53).
- **62 employees with low satisfaction (1–3) are still active.**

### 3. Pay

| Salary band | Headcount | Leavers | Attrition |
|---|---|---|---|
| Under 60K | 181 | 49 | 27.1% |
| 60K–90K | 445 | 127 | 28.5% |
| 90K–120K | 258 | 63 | 24.4% |
| 120K+ | 116 | 37 | 31.9% |

There is **no clear pattern** between salary and attrition. Average salary is similar for active (84.6K), resigned (85.9K), and terminated (85.3K) employees. In this dataset, pay does not separate leavers from stayers.

---

## Recommendations

These are hypotheses based on sample data and should be checked before acting on them.

1. **Look into Operations and Customer Support first** (35.1% and 30.8% attrition). Use exit interviews or surveys to find out why people leave.
2. **Use satisfaction as an early-warning signal.** Employees scoring 1–3 left at 64%, and 62 of them are still active. Pulse surveys and stay-conversations could target this group.
3. **Check whether high performers are leaving.** 86 employees rated 4–5 left. Review whether those exits were avoidable.
4. **Don't assume pay rises will fix attrition.** Salary shows no clear link to leaving here, so look at other drivers (workload, management, growth) before spending on pay.
5. **Collect better data:** exit dates, manager, tenure, and the reason for leaving would allow a much deeper analysis.

---

## Limitations

- **Sample data.** Findings apply to this dataset only.
- **No exit dates.** The data shows who left, not when, so there is no attrition trend over time and no tenure analysis for leavers.
- **Timing of scores is unknown.** Satisfaction and performance scores have no timestamp. They may have been recorded after the employee decided to leave, so this analysis shows *association*, not cause.
- **Resigned and terminated are combined** in the headline attrition rate. They are different things (voluntary vs company-initiated), and splitting them would give a clearer picture.
- **Descriptive analysis only.** No statistical tests were run, and each factor is looked at one at a time (for example, satisfaction and performance may be related to each other).
- **Small groups.** Some departments and salary bands have few people (for example 116 employees earning 120K+), so their rates can swing easily.
- **Band cut-offs are my choice.** Different cut-offs could change the numbers.
- **Single flat table.** This model has no relationships or star schema, because the data is one table.

---

## How it was built

1. **Power Query:** checked data types and quality, added a `Left Company` flag (1 if status is not Active), `Hire Year`, and three band columns using conditional columns.
2. **DAX measures:**

```
Headcount        = COUNTROWS(HR_Records)
Leavers          = SUM(HR_Records[Left Company])
Attrition Rate   = DIVIDE([Leavers], [Headcount])
Active Employees = CALCULATE([Headcount], HR_Records[Employment Status] = "Active")
Avg Salary       = AVERAGE(HR_Records[Annual Salary])
Avg Satisfaction = AVERAGE(HR_Records[Satisfaction Score])
Avg Performance  = AVERAGE(HR_Records[Performance Rating])
```

3. **Validation:** compared each measure against an independent calculation in Python (for example, 276 leavers in total, with every department and band matching).
4. **Dashboard:** four KPI cards, four charts (department, satisfaction band, performance band, salary band), and slicers for Department and Gender.

**Skills used:** Power Query, conditional columns, DAX measures, data validation, KPI cards, charts, slicers, and dashboard layout.

---

## Possible next steps

- Split attrition into **voluntary (resigned)** and **involuntary (terminated)**
- Add a **drill-through page** for each department
- Analyze combinations (for example, low satisfaction *and* high performance)
- Repeat the analysis on a richer dataset with exit dates and tenure
- Build a multi-table model with relationships (star schema)

---

## Repository structure

```
├── README.md
├── HR_Attrition_Dashboard.pbix
└── images/
    └── dashboard.png
```

---

## Credits

- **Dataset:** [TrumpExcel, Excel sample data](https://trumpexcel.com/excel-sample-data/)
- **Learning resource:** Fernan | Power BI (Solutions Abroad), *Beginners Guide to Power BI*, used to learn the workflow. The dashboard here is built from my own data and questions.

Please review the original sources' terms before redistributing their files.

---

## Author

**[Your Name]** · [LinkedIn](https://www.linkedin.com/) · [GitHub](https://github.com/)
