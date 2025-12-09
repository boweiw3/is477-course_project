# Crime & Education: Understanding How School System Indicators Relate to Crime Rates (2015–2024)

## Contributors
- Bowei Wang  

---

## 1. Summary

### 1.1 Project Introduction
This project investigates how state-level education characteristics relate to crime rates across the United States. We integrate two official federal datasets—the FBI Uniform Crime Reporting (UCR) **Summary Reporting System (SRS)** and the National Center for Education Statistics (NCES) **Common Core of Data (CCD) SEA state-level files**—to construct a reproducible dataset that links crime metrics with public-school staffing indicators for each state and year.

Our motivation is that education and public safety are deeply connected aspects of social well-being, yet they are often addressed separately in policy and public debate. Educational opportunities, staffing levels, and school resources can shape community stability, while crime affects both student outcomes and local school systems. Rather than making causal claims, our goal is to provide a transparent, data-driven view of how these systems move together over time.

The analysis focuses on the overlapping years **2015, 2017, 2019, and 2021**, which are the only years where both FBI and NCES data are simultaneously available after cleaning. For each state and year, we derive per-capita crime rates (e.g., violent crimes per 100,000 residents) and aggregate education staff totals. We then compute descriptive statistics, visualize temporal trends, and examine pairwise correlations to see whether differences in education indicators correspond to differences in violent and property crime rates.

Our main contributions are:

- A **cleaned crime dataset** derived from the FBI SRS file, with territories removed and standardized per-capita crime metrics.
- A **cleaned education dataset** from the CCD SEA state files, aggregating staffing indicators into a single, consistently reported measure (`edu_staff_total`).
- A **merged state-year dataset** (`data/merged.csv`) covering 204 state-year observations across four years.
- A set of **Python notebooks** that implement the full workflow: profiling, cleaning, integration, and exploratory analysis.
- A transparent discussion of data quality issues and limitations, especially the sparsity and definitional complexity of education data.

Throughout the project we emphasize association rather than causation. We frame our research questions around patterns and correlations: Do states with higher staffing levels show lower crime rates? How have crime rates and staff totals moved over time? Are there clear regional differences? By making our workflow and assumptions explicit, the project aims to support informed interpretation and future extensions rather than definitive policy prescriptions.

### 1.2 Ethical, Privacy, and Legal Considerations

Both the FBI SRS and NCES CCD SEA datasets are **public, aggregate, non-PII datasets**, meaning they contain no personal identifiers and pose minimal privacy risk. Nonetheless, ethical and legal considerations remain important.


#### **Data Licensing and Terms of Use**

- **FBI Crime Data Explorer** provides downloadable datasets intended for public use, subject to attribution and use within reasonable analytical contexts.
- **NCES CCD data** are governed by the Department of Education, which permits reuse for statistical and research purposes.  
  We adhere to these terms by maintaining attribution, avoiding any attempt to re-identify individuals, and using the datasets solely for educational research.


#### **Representation and Bias**

Although both datasets are official and comprehensive, they contain structural biases:

- FBI reporting depends on local law enforcement agencies’ participation, which varies by state and year.
- Education staffing data show inconsistencies in reporting practices, and many demographic breakdowns are missing for most states.

We explicitly avoid causal claims and acknowledge that measurement artifacts may influence observed patterns.


#### **Data Quality and Transparency**

All cleaning decisions (e.g., dropping high-missing columns, removing territories, aggregating staff totals)  
are documented in notebooks and justified in this report.

We preserve raw files to support full auditability.


#### **Provenance and Reproducibility**

- Each NCES row includes its origin ZIP file as a `source_file` tag, ensuring traceability.
- All transformation steps are scripted in notebooks, and the workflow can be reproduced with the provided instructions.


**Overall, this project prioritizes transparency, responsible use of public data, and clear communication of limitations.**


### 1.3 Data Lifecycle Framework

This project follows the **Wiggins & Vanderhoof Data Lifecycle Model**, which structures data work into six iterative stages: *Plan, Acquire, Process, Analyze, Preserve,* and *Share*. Aligning our workflow to this model ensures transparency, methodological consistency, and reproducibility.


#### **Plan**
We begin by defining the research scope: analyzing associations between education staffing indicators and crime rates across U.S. states. This determines our data needs—state-level crime statistics from the FBI SRS and staff-level education data from the NCES CCD SEA files.


#### **Acquire**
Data are obtained from two authoritative federal sources.

- FBI data are downloaded as a single CSV containing annual crime statistics.  
- NCES CCD SEA data consist of multiple ZIP archives per year, each containing staffing tables.

We preserve the raw files in `data/raw/` without modification to maintain provenance.


#### **Process**
Both datasets undergo structured profiling and cleaning.

- FBI data require removal of invalid rows, territories, and inconsistent fields such as legacy/revised rape categories.  
- NCES data require extraction, consolidation across years, removal of 95%+ missing columns, filtering to valid state entries, and construction of a consistent `edu_staff_total` variable.  
  All transformations are implemented in Jupyter notebooks to allow step-by-step verification.


#### **Analyze**
We integrate both datasets on `state` and `year`, compute per-capita crime metrics, and generate descriptive summaries, time series plots, and correlation matrices. Analyses follow an exploratory approach focused on association rather than prediction or causation.


#### **Preserve**
All cleaned datasets are saved to `data/cleaned/`, accompanied by metadata and documentation.  
Intermediate artifacts (profiling output, figures, merged datasets) are versioned through the Git repository.


#### **Share**
The final deliverables include notebooks, cleaned data files, and this README as a complete, reproducible package.  
All code and documentation follow open, transparent principles that enable users to repeat the workflow end-to-end.

---

## 2. Data Profile

### 2.1 FBI Crime Data (Summary Reporting System)

**Source**  
FBI Crime Data Explorer – Summary Reporting System (SRS), downloaded as `estimated_crimes_1979_2024.csv`.

**Coverage and Structure**  
The raw file contains annual crime statistics for U.S. states, the District of Columbia, and several territories from **1979 to 2024**. Key columns include:

- `year` – calendar year of record  
- `state_abbr` – two-letter state abbreviation  
- `state_name` – full state name  
- `population` – estimated state population  
- `violent_crime` – total violent crimes  
- `homicide` – murders and non-negligent manslaughter  
- `robbery`, `aggravated_assault` – major violent crime categories  
- `property_crime`, `burglary`, `larceny`, `motor_vehicle_theft` – property crime counts  
- `caveats`, `rape_legacy`, `rape_revised` – auxiliary or definition-dependent fields  

After cleaning, we retain **50 states + D.C.** and restrict the period to **2015–2021**, which is sufficient to cover our overlapping years with NCES education data. We compute several derived variables:

- `violent_crime_rate` = violent_crime / population × 100,000  
- `property_crime_rate` = property_crime / population × 100,000  
- `homicide_rate`, `robbery_rate`, `aggravated_assault_rate` defined analogously  

These rates allow meaningful comparisons across states with very different population sizes.

**Use in Analysis**  
The cleaned crime dataset (saved as `data/cleaned/crime_cleaned.csv`) is used to:

- Describe state-level crime levels and trends over time.
- Provide per-capita crime rates for correlation analysis.
- Serve as the main outcome variables in exploratory regression and visualization.

---

### 2.2 NCES Education Data (CCD SEA State-Level Files)

**Source**  
NCES Common Core of Data – SEA State-Level Nonfiscal Survey Data, downloaded for survey years:

- 2015–2016 (`15-16`)  
- 2017–2018 (`17-18`)  
- 2019–2020 (`19-20`)  
- 2021–2022 (`21-22`)  
- 2023–2024 (`23-24`)  

Each year’s folder contains two ZIP archives corresponding roughly to **staffing** and **membership** tables. We focus on the SEA staffing files and extract their CSVs programmatically.

**Coverage and Structure**  
The merged raw education dataset contains **62,841 rows** and **357 columns**. Important fields include:

- `SURVYEAR` – survey year label (e.g., “2015-2016”)  
- `STABR` – state abbreviation (57 unique values including territories)  
- `STATENAME` – state name  
- `STAFF`, `SECTCH`, `ELMTCH`, `SCHSUP`, `STUSUP`, etc. – aggregate counts of teachers and staff  
- Numerous highly specific race × grade × gender indicators  

Because most of the detailed demographic columns are sparsely populated, we focus on **aggregated staffing totals** that have near-complete coverage. During cleaning we derive a numeric year field (`year`) from the folder name and keep the range **2015–2023**.

**Cleaning and Core Variable**  
Due to extreme missingness, 341 out of 357 columns have more than 95% missing values and are dropped. We retain a small set of robust variables and then further narrow to four core columns:

- `state_fips` – numeric state code  
- `state` – uppercase state name  
- `edu_staff_total` – total staff count (from `STAFF`)  
- `year` – derived numeric year  

We remove rows with missing state, year, or staff totals and filter out territories such as Puerto Rico, Guam, and the Virgin Islands. The resulting cleaned education dataset (`data/cleaned/education_cleaned.csv`) contains **5,786 rows**, which are later aggregated to state-year totals.

**Aggregation and Use**  
We aggregate education data to one record per state and year by summing `edu_staff_total`. This produces **204 state-year observations** for the years **2015, 2017, 2019, and 2021**. The aggregated staff totals serve as our primary education indicator, approximating the staffing capacity of public school systems.

---

### 2.3 Integrated Dataset

To study education–crime relationships, we merge the two cleaned datasets:

1. Align states: convert all state names to uppercase and restrict both datasets to the 50 states + D.C.  
2. Align years: intersect the crime and education year sets. The intersection is **{2015, 2017, 2019, 2021}**.  
3. Aggregate education data to state–year level (`edu_staff_total`).  
4. Perform an inner join on `state` and `year`.

The final merged dataset (`data/merged.csv`) has:

- **204 rows** (51 states × 4 years)  
- **18 columns**, including crime counts, per-capita crime rates, population, and `edu_staff_total`.

This dataset is the basis for our trend plots, correlation analysis, and summary findings.

---

## 3. Data Quality

### 3.1 Crime Data Quality

The crime dataset is relatively clean and complete for the years we analyze:

- Core columns (`population`, `violent_crime`, `property_crime`, `homicide`, `robbery`, `aggravated_assault`, `burglary`, `larceny`, `motor_vehicle_theft`) have **0% missing values**.
- The `caveats` column is 97% missing and carries only textual notes, so it is removed.
- `rape_legacy` and `rape_revised` have mutually exclusive coverage due to definitional changes in the FBI’s rape reporting; to avoid inconsistent series, we drop them and rely on `violent_crime` instead.
- 38 records have missing `state_name`, corresponding to national totals or improperly coded rows; these are dropped.
- After restricting to 50 states + D.C. and years 2015–2024, the dataset contains 357 rows. Restricting further to the overlapping years with education data yields 204 rows.

We detect no duplicated state–year combinations and no impossible values (e.g., negative crime counts). Per-capita rate calculations use population as the denominator and therefore rely on the FBI’s population estimates, which may have their own model-based uncertainty but are standard for cross-state comparisons.

### 3.2 Education Data Quality

Education data quality is more challenging:

- The merged SEA dataset includes **57 state codes**, covering the 50 states, D.C., and multiple territories and specialized agencies (e.g., Bureau of Indian Education, Department of Defense Education Activity).
- Many columns represent detailed race-by-grade staff counts that are only reported for some states or years. Over **95% of columns** have more than 95% missing values.
- Core identifier fields (`STABR`, `STATENAME`) and aggregated staff counts (`STAFF`) are significantly more complete.
- We address this by dropping all high-missing columns and focusing on the more robust aggregated staff totals.

Residual limitations remain:

- Some staff totals are zero for later years, likely reflecting changes in reporting or suppression rather than true absence of staff.
- The SEA files capture state education agencies, which may not map perfectly onto district-level staffing; however, they still provide a reasonable proxy for statewide staff capacity.
- Because we rely only on a single staffing indicator (`edu_staff_total`), our education representation is narrow and does not include enrollment or graduation rate.

Despite these limitations, our final education dataset is structurally consistent and sufficient for basic association analysis.

### 3.3 Integration Quality

Integration quality is evaluated along three dimensions:

1. **Key Alignment**  
   - State names are normalized to uppercase across both datasets.
   - Territories and non-state agencies are removed in both data sources.
   - After filtering, we obtain a perfect set of 51 unique states (including D.C.) in both datasets.

2. **Temporal Consistency**  
   - FBI data are reported by calendar year, while CCD SEA data use survey years. We map SEA folder names (e.g., “15-16”) to the starting calendar year (2015), which is a reasonable approximation for annual comparisons.
   - We only use years that appear in both datasets: 2015, 2017, 2019, 2021.

3. **Missingness After Merge**  
   - The merged dataset contains no missing values in the variables we use for analysis.
   - We confirm that there is exactly one row per state-year combination.

Overall, while education data sparsity limits the richness of our indicators, the integrated dataset is internally consistent and suitable for exploratory analysis of associations between crime and staffing totals.

---

## 4. Findings

### 4.1 Temporal Trends

Using the merged dataset, we compute statewide averages of per-capita crime rates and education staff totals by year.

- **Property crime rates** show a clear downward trend from 2015 to 2021. Average property crime per 100,000 residents declines steadily, suggesting broad improvements in property crime across states.
- **Violent crime rates** remain relatively stable, with only modest changes over time compared to property crime.
- **Education staff totals** appear high in 2015 but drop sharply to near-zero levels for later years in the CCD SEA staffing totals. This pattern reflects changes in reporting or classification rather than actual elimination of staff and highlights the limitations of relying on a single aggregate measure.

These trends demonstrate that crime rates have generally improved over the time window we study, but the education time series is difficult to interpret due to structural data issues.

### 4.2 Correlation Patterns

We compute a correlation matrix for the main variables: violent crime rate, property crime rate, homicide rate, robbery rate, aggravated assault rate, and `edu_staff_total`.

- Crime rates are **strongly positively correlated** with one another. For example, aggravated assault rate has a correlation above 0.9 with the overall violent crime rate, and both are moderately correlated with property crime rate. This is expected since they share similar underlying drivers.
- The education staff total shows **very weak correlations** with crime rates:
  - Violent crime rate ≈ –0.01  
  - Property crime rate ≈ 0.12  
  - Robbery and homicide rates also have correlations near zero.

In other words, given the available data, we do not observe a strong linear association between total state-level staff counts and crime rates. This does **not** mean education has no relationship with crime; rather, our single aggregated staff measure, combined with state-level aggregation and data quality issues, is not sufficient to capture such relationships.

### 4.3 Geographic Patterns

Although our analysis is primarily numeric, we observe:

- States with larger populations (e.g., California, Texas, Florida) naturally have higher crime counts but do not necessarily have the highest crime **rates** once adjusted for population.
- Many states show parallel declines in property crime, suggesting broader national trends rather than purely local effects.
- Because education staff totals lack reliable variation after 2015, regional contrasts in education indicators are difficult to interpret.

Taken together, our findings highlight a clear decline in property crime and stable violent crime, but no strong evidence of association between total education staff and crime rates at the state level in this particular dataset.

---

## 5. Future Work

This project is best viewed as a first step toward integrating education and crime data. Several extensions could substantially improve both the robustness and interpretability of the results:

1. **Richer Education Indicators**  
   Instead of relying solely on staff totals, future work could incorporate:
   - Enrollment counts and student–teacher ratios (MEMBER and teacher FTE data from additional CCD tables).  
   - Graduation rates, dropout rates, and test scores from other NCES datasets.  
   These variables would provide a more direct measure of “education quality” and outcomes.

2. **Socioeconomic Controls**  
   Crime and education are both influenced by broader structural factors such as income, unemployment, urbanization, racial segregation, and policy differences. Adding state-level covariates from the Census or American Community Survey would enable multivariate modeling and help distinguish direct relationships from confounding.

3. **Finer Spatial Resolution**  
   State-level aggregation masks important within-state variation. Using county- or district-level data (e.g., UCR county data combined with CCD district files) could reveal spatial clusters where education and crime are more tightly linked.

4. **Improved Temporal Alignment**  
   Future work could align school-year and calendar-year data more carefully (for example, by mapping a school year partially across two calendar years or using lagged variables). This might capture delayed effects of education changes on crime rates.

5. **Causal Modeling and Robustness Checks**  
   With richer data, one could explore:
   - Fixed-effects panel regression to control for unobserved state characteristics.  
   - Difference-in-differences or event-study designs around major policy changes.  
   - Sensitivity analyses to test how robust findings are to different modeling choices.

6. **Workflow and Documentation Enhancements**  
   Finally, the computational workflow could be further automated using tools like Snakemake or Makefiles, and the project could be packaged for public reuse (for example, as a reproducible repository with a DOI).

By documenting both what works well and where the data fall short, this project sets up a foundation that can be extended into more detailed, policy-relevant research.

---

## 6. Reproducing the Project

### 6.1 Repository Structure

The project repository is organized as follows:

```text
data/
  raw/
    crime_data/
      estimated_crimes_1979_2024.csv
    education_data/
      15-16/
      17-18/
      19-20/
      21-22/
      23-24/
  cleaned/
    crime_cleaned.csv
    education_cleaned.csv
    merged.csv

notebooks/
  crime_profiling&cleaning.ipynb
  education_profiling&cleaning.ipynb
  data_integration.ipynb
  analysis.ipynb

ProjectPlan.md
StatusReport.md
README.md
requirements.txt
```

---

### 6.2 Obtaining the Raw Data

#### Crime Data (FBI UCR SRS)

Download:

estimated_crimes_1979_2024.csv

Place it into:

data/raw/crime_data/

---

#### Education Data (NCES CCD SEA)

Download the SEA ZIP archives for:

- 2015–2016  
- 2017–2018  
- 2019–2020  
- 2021–2022  
- 2023–2024  

Place each year’s ZIPs into the matching folders:

data/raw/education_data/15-16/  
data/raw/education_data/17-18/  
data/raw/education_data/19-20/  
data/raw/education_data/21-22/  
data/raw/education_data/23-24/  

The notebook handles extraction.

---

### 6.3 Running the Workflow

Run the notebooks **in order**:

---

#### 1. Education Profiling & Cleaning  
File: `notebooks/education_profiling&cleaning.ipynb`

This step:

- Extracts all SEA CSVs from ZIP archives  
- Merges ~62k rows across 357 columns  
- Drops >95% missing columns  
- Keeps identifiers + `edu_staff_total`  
- Filters to valid states + valid years  
- Saves cleaned education data to:

data/cleaned/education_cleaned.csv

---

#### 2. Crime Profiling & Cleaning  
File: `notebooks/crime_profiling&cleaning.ipynb`

- Loads FBI SRS dataset  
- Removes invalid or non-state rows  
- Drops unused or inconsistent fields  
- Computes per-capita crime rates  
- Saves cleaned crime data to:

data/cleaned/crime_cleaned.csv

---

#### 3. Data Integration  
File: `notebooks/data_integration.ipynb`

- Loads cleaned datasets  
- Identifies overlapping years: 2015, 2017, 2019, 2021  
- Aggregates education staff totals  
- Standardizes state names  
- Performs merge on `state` + `year`  
- Saves integrated dataset:

data/cleaned/merged.csv

---

#### 4. Analysis & Visualization  
File: `notebooks/analysis.ipynb`

- Loads merged dataset  
- Generates descriptive statistics  
- Produces time-series plots  
- Computes correlation matrix + heatmap  
- Supports findings section of the report  

---

### 6.4 Expected Outputs

After running all notebooks, you should have:

data/cleaned/
  crime_cleaned.csv
  education_cleaned.csv
  merged.csv

Notebooks produce all figures and summary tables used in the report.

---

## 7. References

- FBI Crime Data Explorer – Summary Reporting System (SRS)  
  https://cde.ucr.fbi.gov/LATEST/webapp/#/pages/downloads  

- NCES Common Core of Data – State Education Agency Files  
  https://nces.ed.gov/ccd/data_tables.asp  

- McKinney, W. (2010). *Data Structures for Statistical Computing in Python*.

- Hunter, J. D. (2007). *Matplotlib: A 2D Graphics Environment*.  

- Waskom, M. (2020). *seaborn: statistical data visualization*.  

---

## Appendix — Metadata / Data Dictionary

### Metadata Summary for Merged Dataset

Below is the data dictionary for `merged.csv`, which contains **204 state-year observations**.

| Column                   | Type   | Description                                                       |
|--------------------------|--------|-------------------------------------------------------------------|
| `year`                   | int    | Calendar year of observation (2015, 2017, 2019, 2021).           |
| `state_abbr`             | string | Two-letter USPS state abbreviation.                              |
| `state`                  | string | Uppercase state name used as join key.                           |
| `population`             | int    | Estimated state population from FBI SRS.                         |
| `violent_crime`          | int    | Total violent crimes (FBI definition).                           |
| `homicide`               | int    | Murder and non-negligent manslaughter.                           |
| `robbery`                | int    | Total robberies.                                                 |
| `aggravated_assault`     | int    | Total aggravated assaults.                                       |
| `property_crime`         | int    | Total property crimes.                                           |
| `burglary`               | int    | Burglary offenses.                                               |
| `larceny`                | int    | Larceny-theft offenses.                                          |
| `motor_vehicle_theft`    | int    | Motor vehicle theft offenses.                                    |
| `violent_crime_rate`     | float  | Violent crimes per 100,000 residents.                            |
| `property_crime_rate`    | float  | Property crimes per 100,000 residents.                           |
| `homicide_rate`          | float  | Homicides per 100,000 residents.                                 |
| `robbery_rate`           | float  | Robberies per 100,000 residents.                                 |
| `aggravated_assault_rate`| float  | Aggravated assaults per 100,000 residents.                       |
| `edu_staff_total`        | float  | Aggregated SEA staffing totals for each state-year.              |

This schema reflects the final integrated dataset used in analysis and supports consistent interpretation and reuse.
