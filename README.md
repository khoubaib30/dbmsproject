# HR Harmony – HR Analytics Dashboard (Power BI)

An end-to-end BI project on **real, anonymised HR data from a Tunisian company**: a Python ETL pipeline consolidates four raw sources into a PostgreSQL star schema, and a Power BI dashboard tracks attendance, absenteeism, working hours and leave at employee, department and company level.

## Data

- **Sources (4):** employee database (SQL), daily attendance logs, absences and leave records (Excel)
- **Scope:** 113 employees, 13 departments, October – December 2022 (65 working days)
- **Privacy:** the raw data is not published; employee and department names are replaced by pseudonyms in the dashboard

## ETL pipeline (`etlprocess.ipynb`)

1. Convert the Excel sheets to JSON
2. **Match employee names across files** with fuzzy matching (RapidFuzz, similarity threshold of 70), since the same person was spelled differently in each source
3. Standardise dates (`YYYY-MM-DD`) and times (`HH:MM:SS`)
4. Compute total working time and overtime per day, and leave duration (including half days)
5. Build the fact table and the dimensions, then load them into PostgreSQL

## Data model (star schema)

| Table | Rows | Content |
|---|---|---|
| `hrfact` | 6,264 | One row per employee and day: present, absent, on leave, weekend, working time, overtime |
| `employeedimension` | 113 | Employee ID, department, pseudonym |
| `departmentdimension` | 13 | Department ID, headcount, pseudonym |
| `datedimension` | 92 | Date, day, month, weekday, weekend flag |

## Dashboard (`projectbi.pbix`)

The report contains **15 DAX measures**: absenteeism rate, presence rate, average working hours, leave days and leave rate.

The main measures are **context-aware**: using `HASONEVALUE`, the same measure returns the individual value when one employee is selected, the department average when a department is selected, and the company average otherwise.

Visuals: KPI cards and gauges, absences by department, trends over time, working hours vs overtime, with interactive slicers.

## Key findings (Q4 2022)

- Average absenteeism rate of **4.6%** over 65 working days
- **All recorded absences are concentrated in 5 of the 13 departments**
- More than **1,900 overtime hours** over the quarter

## Tech stack

Python (Pandas, RapidFuzz) · PostgreSQL · Power BI (DAX) · Google Colab

## How to open

- **ETL:** open `etlprocess.ipynb` in Google Colab or Jupyter (the raw files are not included)
- **Dashboard:** open `projectbi.pbix` with Power BI Desktop
