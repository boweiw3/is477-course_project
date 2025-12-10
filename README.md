# Crime and Public Education: Constructing an Integrated State-Level Dataset for Exploratory Analysis (2015–2021)

## Contributors
- Bowei Wang

---

# 1. Summary

Crime and public education are two central components of state-level governance in the United States, yet they are rarely examined together using harmonized quantitative data. This project aims to bridge that gap by constructing a reproducible, multi-source dataset that aligns crime statistics from the FBI’s Summary Reporting System (SRS) with public-school staffing information from the National Center for Education Statistics (NCES) Common Core of Data (CCD). The goal of this project is not to establish causal relationships but to explore descriptive patterns and structural associations between these two domains across states and over time. Through this process, the project also demonstrates the challenges of working with administrative datasets that differ in structure, quality, and temporal definitions.

The project focuses on four years where both datasets provide sufficient coverage: 2015, 2017, 2019, and 2021. These years offer a consistent basis for comparison, even though the NCES data are reported on a school-year basis (e.g., 2015–2016) and the FBI data follow calendar years. The primary tasks of the project include raw data ingestion, profiling, cleaning, normalization, temporal alignment, dataset integration, and statistical exploration. The analysis is implemented through a series of Jupyter notebooks that form a coherent workflow.

From a methodological standpoint, this project involves several core data engineering tasks: filtering incomplete records, resolving naming inconsistencies, collapsing sparse variables, deriving per-capita crime statistics, aligning state identifiers, and merging datasets using a consistent join key. These tasks are essential for transforming heterogeneous administrative data into a unified analytical framework.

The findings from the integrated dataset highlight several large-scale trends. Property crime shows a clear downward trajectory across the selected years, a pattern that is well-documented nationally and appears consistently across most states. Violent crime, in contrast, remains relatively stable with only moderate fluctuations. Education staffing levels exhibit far less interpretable behavior, largely because the NCES data suffer from inconsistencies, missing values, and variation in reporting practices across states. As a result, while the crime indicators form coherent temporal and correlational structures, the education data do not.

Correlation analysis further reveals that education staffing totals exhibit minimal linear association with crime rates. This does not imply the absence of a conceptual relationship; rather, it reflects the limitations of the available data, particularly the extreme aggregation of the staffing variable and the mismatch between educational and calendar years.

Overall, this project offers a practical demonstration of the challenges inherent in integrating administrative datasets, while also illuminating some broad patterns in U.S. crime data. It serves as a foundation for more refined analysis should more detailed education or socioeconomic metrics be incorporated in future work.

---

# 2. Data Profile

This project utilizes two major datasets collected by federal agencies that maintain long-running administrative records across states.

## 2.1 FBI Summary Reporting System (SRS)

The SRS dataset is an annual criminal offense report compiled by participating law-enforcement agencies. The version used in this project includes national crime statistics from 1979 to 2024, housed in the raw directory at:

Key characteristics of the SRS dataset:

- **Coverage:** All 50 states and the District of Columbia.
- **Variables:** Includes counts of property crime, violent crime, aggravated assault, robbery, burglary, motor vehicle theft, and population.
- **Temporal Structure:** Reported by calendar year.
- **Ethical Considerations:** Data are aggregated at the state level and contain no personally identifiable information.
- **Legal Considerations:** Publicly available for research and educational purposes.

For this project, only the years 2015, 2017, 2019, and 2021 are extracted. State names are standardized, and population is used to compute per-capita crime rates.

---

## 2.2 NCES State Education Agency (SEA) Staffing Files

The NCES SEA staffing files contain administrative records on personnel employed by state education agencies. These files are stored under:

Each subfolder corresponds to a school year (e.g., 15–16, 17–18, etc.). The files include hundreds of personnel categories, but sparsity is a significant issue: most variables contain 95–99% missing values. After profiling, only broad staffing totals were deemed suitable for use.

Key features of the NCES dataset:

- **Temporal Structure:** Reported by academic year, not calendar year.
- **Units:** Number of staff employed by each state’s education system.
- **Ethical Considerations:** Aggregated data without personal identifiers.
- **Constraints:** Substantial missingness, varying reporting standards, and inconsistent coding across years.

Because of these constraints, a single aggregated variable—`edu_staff_total`—is retained after cleaning. It provides a coarse measure of education system staffing but should be interpreted carefully.

---

## 2.3 Integrated Dataset

The two datasets are merged after several alignment steps:

- Standardizing state identifiers.
- Matching school-year data to the nearest corresponding calendar year.
- Retaining only years available in both datasets.
- Filtering out non-state entities, territories, and invalid entries.

The final merged dataset contains 204 state-year observations with crime rates, crime counts, population, and education staffing totals. The cleaned outputs (`crime_cleaned.csv`, `education_cleaned.csv`, and `merged.csv`) are provided via Box (see Section 8).

---

# 3. Data Quality

Data quality is assessed along three dimensions: completeness, consistency, and comparability.

## 3.1 Crime Data Quality

The SRS dataset exhibits strong quality indicators:

- **Completeness:** Few missing values in selected years.
- **Consistency:** Uniform definitions enable comparison across states and years.
- **Validity:** Population data allow calculation of normalized rates.
- **Potential Issues:** Crime reporting varies slightly between states, but this effect is small at the level of aggregation used here.

Overall, the crime dataset is reliable and well-structured.

---

## 3.2 Education Data Quality

The education staffing data were significantly more challenging:

- **Missingness:** Most variables are too sparse for meaningful analysis.
- **Reporting Variability:** States differ in which categories they report from year to year.
- **Temporal Misalignment:** School-year reporting complicates merging with calendar-year crime data.
- **Anomalies:** Some states show implausibly low staffing values due to reporting gaps.

Because of these issues, education staffing totals should be interpreted as noisy indicators rather than precise measures.

---

## 3.3 Integration Quality

The integration process introduces unavoidable limitations:

- Aligning school-year and calendar-year data requires simplifying assumptions.
- Differences in agency reporting standards introduce structural noise.
- State-level aggregation obscures variation within states.

Despite these challenges, the merged dataset is sufficiently stable for exploratory analysis.

---

# 4. Findings

The exploratory analysis yields several noteworthy observations.

## 4.1 Temporal Trends

- **Property crime declines consistently** across 2015–2021, reinforcing national trends documented by criminologists.
- **Violent crime remains stable**, showing smaller fluctuations and less directional movement.
- **Education staffing totals do not form coherent trends**, largely due to missing or inconsistent reporting.

These patterns reflect both genuine social trends and limitations in data quality.

---

## 4.2 Correlation Patterns

Correlation analysis reveals:

- **High internal correlation within crime categories** (e.g., violent crime and aggravated assault).
- **Moderate correlation between population and crime counts**, which is expected.
- **Near-zero correlation between education staffing and crime rates**, likely due to:
  - data sparsity,
  - extreme aggregation,
  - temporal misalignment,
  - measurement inconsistencies.

Thus, the absence of correlation should not be interpreted as evidence of no relationship between education systems and crime—only a limitation of the available data.

---

## 4.3 Cross-State Differences

States differ widely in crime rates, with smaller states sometimes showing higher per-capita rates due to population effects. Education staffing differences are harder to interpret given data inconsistencies.

---

# 5. Future Work

Several directions could improve the robustness and interpretability of this analysis:

- **Expand educational metrics** beyond staffing totals (e.g., student–teacher ratios, expenditures, or district-level staff).
- **Incorporate socioeconomic indicators** such as income, unemployment, and demographic composition.
- **Use more granular geographic units** (counties, districts) to address state-level aggregation issues.
- **Develop better temporal alignment strategies**, possibly by using lagged variables.
- **Automate the entire pipeline** using Snakemake or similar workflow tools.
- **Investigate alternative integration frameworks**, including fixed-effects or hierarchical models.

---

# 6. Reproducing This Project

### Repository Structure

data/
cleaned/      (download cleaned files here)
raw/
crime/
education/
notebooks/
clean_crime.ipynb
clean_education.ipynb
merge_data.ipynb
analysis.ipynb
requirements.txt
README.md

### Steps

1. Clone this repository.
2. Download the cleaned CSVs from Box (Section 8).
3. Place them in:
data/cleaned/

4. Install dependencies:
pip install -r requirements.txt

5. Run the notebooks in order:
   - `clean_education.ipynb`
   - `clean_crime.ipynb`
   - `merge_data.ipynb`
   - `analysis.ipynb`

All figures and outputs will regenerate.

---

# 7. Cleaned Data Access (Box)

Per course requirements, cleaned data are not hosted in the GitHub repository.

📦 **Box link:**  
https://uofi.box.com/s/uul7holx2svv3vi8e8lw6stffu8gr1ag

Download:
crime_cleaned.csv
education_cleaned.csv
merged.csv

Place them in:
data/cleaned

---

# 8. References

- FBI Uniform Crime Reporting (UCR) Program: Summary Reporting System (SRS) Documentation  
- National Center for Education Statistics (NCES), Common Core of Data (CCD), State Education Agency (SEA) Files  
- Additional methodological notes included within project notebooks
