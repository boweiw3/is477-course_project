# Crime and Public Education: Constructing a State-Level Integrated Dataset for Exploratory Analysis (2015–2021)

## Contributors
- Bowei Wang

---

# Summary 

Crime and education represent two foundational components of state governance and public policy in the United States, yet empirical work that connects them often confronts substantial data-related challenges. Crime is tracked annually by the FBI through structured incident reporting that reflects calendar-year activity. Public education, by contrast, is administered through state education agencies, and the National Center for Education Statistics (NCES) collects staffing and enrollment information on a school-year basis. These two domains differ not only in purpose but in reporting structures, units of analysis, and temporal coverage. As a result, researchers interested in exploring relationships—descriptive, structural, or causal—between crime and educational systems often face obstacles when attempting to join datasets meaningfully. The overarching goal of this project is to construct a reproducible, high-quality, multi-source dataset that harmonizes these two major domains and allows for basic exploratory analysis across U.S. states.

The project focuses on building an integrated panel dataset covering four key years: 2015, 2017, 2019, and 2021. These years represent a balanced window in which both the FBI Summary Reporting System (SRS) dataset and the NCES SEA staffing dataset provide reasonably complete coverage for all 50 states. While the SRS dataset provides consistent annual crime counts and population estimates, the NCES SEA staffing files contain state-level administrative counts of staff employed in public education systems. Because the NCES data exhibit significant sparsity and inconsistency across reporting categories, a major component of this project is assessing which variables carry meaningful information and which must be excluded.

The project workflow consists of four major stages: (1) raw data profiling and extraction, (2) cleaning and harmonization, (3) dataset integration, and (4) statistical exploration. To make this workflow reproducible and transparent, all steps are implemented in Jupyter notebooks, each focusing on a specific segment of the pipeline. These notebooks include detailed comments that document the rationale for each transformation and allow users to trace how the final dataset was constructed.

At the conceptual level, this project is motivated by the desire to understand whether crime patterns and education system indicators share any structural similarities across states and time. The analysis is not designed to support causal claims; rather, it seeks to identify whether descriptive correlations or parallel trends exist when viewing the two domains side by side. For example, one might hypothesize that states with robust education staffing levels may exhibit lower crime rates, or that changes in education investment might precede changes in crime levels. While the present dataset cannot test such hypotheses formally, constructing a well-organized panel is a necessary first step toward future causal analysis.

The findings from this exploratory phase indicate several important patterns. Property crime rates show a clear and steady decline across states over the 2015–2021 period, reflecting national shifts documented in criminology literature. Violent crime rates, in contrast, remain comparatively stable, with smaller fluctuations across years. Education staffing totals, however, do not present an interpretable pattern due to significant missingness and variation in reporting practices. When examining correlation structures, crime variables correlate strongly with one another, showing their shared underlying drivers, while education staffing exhibits near-zero correlation with crime measures. This does not imply the absence of any meaningful relationship between educational and criminal justice systems; instead, it underscores the limitations of highly aggregated, inconsistently reported administrative variables.

Ultimately, this project demonstrates how combining public administrative datasets requires careful cleaning, alignment, and validation. It also shows how data quality directly shapes the scope and interpretability of subsequent analyses. The integrated dataset created here provides a transparent foundation for further research while also illustrating the methodological challenges of multi-source data integration.

---

# Data Profile 

This project relies on two large-scale administrative datasets: the FBI Summary Reporting System (SRS) crime dataset and the NCES State Education Agency (SEA) staffing dataset. Each dataset provides a different perspective on state-level public institutions, and both require extensive preparation before meaningful integration.

## FBI Summary Reporting System (SRS)

The SRS dataset forms the backbone of crime reporting in the United States. It compiles annual offense counts submitted by local law-enforcement agencies to the FBI. The dataset used here spans 1979–2024, but only selected years are extracted. Key variables include:

- Violent crime counts  
- Property crime counts  
- Aggravated assault  
- Robbery  
- Burglary  
- Motor vehicle theft  
- State population estimates  

The SRS dataset is located under:
data/raw/crime/

The dataset is well-structured and standardized, enabling straightforward extraction of crime indicators for each state and year. Because population estimates are included, per-capita crime rates can be computed, allowing normalization across states of vastly different sizes. Importantly, the SRS dataset does not contain personally identifiable information, making it suitable for educational and research use without ethical concerns.

Temporal coverage is annual, which enhances comparability but introduces challenges when combining with school-year datasets such as NCES staffing. Nonetheless, the SRS data are reliable, internally consistent, and sufficiently complete to support the goals of this project.

---

## NCES SEA Staffing Data

The NCES staffing files contain administrative records for public-school employees across states, organized by school year (e.g., 2015–2016). These files are stored under:
data/raw/education/

Each folder contains a CSV file with hundreds of variables representing personnel categories, many of which are sparsely reported. During data profiling, it became clear that most staffing categories were unusable due to missing values, inconsistent coding, or irregular reporting across states. Because of this, the project adopts a pragmatic approach: retaining only an aggregated staffing total (`edu_staff_total`) that is consistently populated across years.

Key characteristics of the NCES dataset include:

- Lack of consistency in reporting standards across states  
- Substantial sparsity across most variables  
- Complex encoding of staff categories  
- Temporal misalignment relative to crime data  

These issues constrain the analytical potential of the dataset but still allow exploration of broad staffing patterns.

---

## Integrated Dataset

After cleaning and harmonization, the datasets are merged on standardized state identifiers and aligned years. Because the NCES files follow a school-year structure, they are matched to the nearest calendar year for integration purposes. Only the four selected years—2015, 2017, 2019, and 2021—contain sufficiently complete data from both sources.

The resulting dataset contains:

- Crime counts  
- Crime rates per 100,000 residents  
- Total education staffing  
- State population  
- State-year identifiers  

The cleaned outputs are distributed via Box due to assignment requirements.

---

# Data Quality  *(~900 words)*

Evaluating data quality is essential for understanding the reliability and limitations of the findings.

## Crime Data Quality

The SRS dataset is comparatively high quality. Missingness is minimal for the selected years, and definitions of key crime categories remain stable. Internal consistency is strong, as reflected by expected correlations between variables (e.g., violent crime and aggravated assault). The primary sources of variability stem from differences in local law-enforcement reporting practices. However, because the data are aggregated at the state level, these inconsistencies have limited impact on the analysis.

Temporal completeness is a strength: all four selected years provide full coverage of all states. This makes the data suitable for identifying broad national patterns.

---

## Education Data Quality

The NCES staffing dataset presents substantial challenges:

### 1. Sparsity  
Most variables contain more than 95% missing values, making them impossible to use for analysis. Only broad staffing totals are sufficiently populated to be retained.

### 2. Inconsistent Reporting  
States differ widely in what they report, causing discontinuities over time.

### 3. Temporal Misalignment  
Because the files correspond to school years (e.g., 2015–2016), aligning them with calendar-year crime data introduces additional noise.

### 4. Implausible Observations  
Several states report staffing totals that are clearly too low or inconsistent with trends, reflecting underreporting rather than actual changes.

Collectively, these quality issues mean the education staffing variable should be treated as a coarse, noisy indicator rather than a precise measure.

---

## Integration Quality

Integration quality depends on harmonizing inconsistent temporal structures. Because school-year data cannot be perfectly aligned with calendar-year crime data, the merged dataset incorporates approximations. This introduces uncertainty into any analysis attempting to compare year-to-year relationships.

Despite these challenges, the merged dataset maintains internal coherence, and the main crime indicators remain highly interpretable.

---

# Findings  *(~500 words)*

The exploratory analysis offers several informative patterns regarding crime and education system indicators across U.S. states.

First, property crime displays a clear downward trajectory over the selected years. This decline appears consistently across states and aligns with national criminological research documenting long-term reductions in property-related offenses. The decrease is visible both in raw counts and in per-capita rates, confirming that this trend is not an artifact of population changes.

In contrast, violent crime exhibits relative stability. While some states show localized increases or decreases, the overall pattern does not indicate a strong directional trend. This suggests that violent crime is influenced by longer-term structural factors that do not fluctuate rapidly from year to year.

The education staffing variable exhibits no coherent trend. Increases or decreases in staffing appear random across states and years, a reflection of the underlying sparsity and inconsistency in the NCES data rather than meaningful variation. Because of this, staffing totals cannot reliably capture actual changes in state education labor forces.

Correlation analysis reveals high internal correlation within crime categories, as expected. States with high aggravated assault rates, for example, also tend to exhibit high violent crime rates. Property crime categories cluster similarly. By contrast, education staffing totals show near-zero correlation with crime rates. This does not imply no relationship exists between education and crime; rather, the dataset is too coarse and inconsistent to capture any such relationship, especially given the temporal misalignment.

Overall, the findings demonstrate that while crime data support clear and interpretable patterns, education staffing data impose major constraints on the depth and reliability of cross-domain analysis.

---

# Future Work  *(~850 words)*

Several improvements could significantly enhance the reliability and analytical potential of this project.

### 1. Incorporate richer education metrics  
The staffing total used here is highly aggregated and insufficient for deeper analysis. Future work should incorporate:
- student–teacher ratios  
- per-pupil expenditures  
- enrollment counts  
- district-level staffing  
- teacher turnover data  

These would provide more nuanced representations of education systems.

### 2. Add socioeconomic indicators  
Crime and education are strongly shaped by socioeconomic context. Integrating Census or ACS variables such as unemployment, poverty rates, income distribution, and demographic composition would provide additional explanatory depth.

### 3. Improve temporal alignment  
A major limitation is the mismatch between calendar-year crime data and school-year education data. Future work could incorporate lag structures or weighted averages to improve alignment.

### 4. Move to finer geographic units  
State-level aggregation obscures local variation. Moving to county- or district-level data would reveal patterns hidden at the state level.

### 5. Automate data processing  
A Snakemake workflow or similar system could fully automate the cleaning, integration, and analysis pipeline, improving reproducibility.

### 6. Conduct causal analyses  
If richer and more consistent data become available, methods such as panel regressions, fixed-effects models, or causal inference frameworks could be applied.

---

# Reproducing

To reproduce this project end-to-end:

### **1. Download cleaned data from Box**
Assignment rules require storing cleaned data externally.

📦 **Box link:**  
https://uofi.box.com/s/uul7holx2svv3vi8e8lw6stffu8gr1ag

Download:
crime_cleaned.csv
education_cleaned.csv
merged.csv

Place the files into:
data/cleaned/

### **2. Install dependencies**
pip install -r requirements.txt

### **3. Run the workflow notebooks**
In order:
notebooks/clean_education.ipynb
notebooks/clean_crime.ipynb
notebooks/merge_data.ipynb
notebooks/analysis.ipynb

All figures, statistics, and merged outputs will regenerate.

---

# References

- FBI Uniform Crime Reporting (UCR) Program: Summary Reporting System (SRS)  
- National Center for Education Statistics (NCES): Common Core of Data (CCD), State Education Agency (SEA) Files  
- Additional documentation cited within notebooks  
