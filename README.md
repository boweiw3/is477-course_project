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

# Data Quality

Assessing data quality is a critical component of this project because the analytical conclusions and exploratory insights depend heavily on the reliability, completeness, and consistency of the underlying administrative datasets. This section evaluates data quality across three primary domains: (1) the FBI Summary Reporting System (SRS) crime dataset, (2) the NCES State Education Agency (SEA) staffing dataset, and (3) the process of integrating these two datasets. Each domain presents distinct strengths and challenges that shape the interpretability of the final merged dataset.

---

## Crime Data Quality

The FBI SRS dataset is comparatively robust, especially when evaluated against typical large-scale administrative data sources. Several key strengths contribute to its reliability.

**Completeness:**  
For the years selected in this project—2015, 2017, 2019, and 2021—the SRS dataset exhibits high completeness across all 50 states. While SRS participation varies across local agencies, state-level aggregation mitigates this issue because states typically achieve broad participation. The dataset includes full state crime totals as well as corresponding population estimates, which enable per-capita normalization and make cross-state comparisons more meaningful.

**Consistency and Standardization:**  
Crime categories such as *violent crime*, *property crime*, *aggravated assault*, and *robbery* are defined in a consistent manner across states and years. Although the FBI later transitioned toward the NIBRS reporting system, the years included in this project fall within a stable SRS reporting period, minimizing definitional shifts. The internal structure of the SRS dataset allows clean derivation of composite variables, such as crime rates per 100,000 residents.

**Validity and Interpretability:**  
Because the SRS dataset uses standardized definitions and reporting forms, its variables exhibit predictable correlations. As a test of internal validity, expected statistical relationships—such as high correlation between violent crime and aggravated assault—are indeed observed. This provides confidence that the crime data behave as theoretically expected and contain meaningful signal.

**Limitations:**  
Despite its strengths, the SRS dataset is not without imperfections. Reporting quality varies across states, and some local jurisdictions may underreport or delay submissions. Furthermore, crime data measure only reported incidents; they do not capture unreported offenses, which may vary systematically between states. However, for exploratory and comparative analysis at the state level, these limitations do not undermine the utility of the dataset. Overall, the SRS data rank as high quality relative to many other public administrative datasets.

---

## Education Data Quality

The NCES SEA staffing dataset presents substantially greater quality challenges. Unlike the SRS dataset, which offers consistent structure and broad coverage, the NCES dataset is characterized by high sparsity, variable reporting practices, and structural inconsistencies. These issues profoundly influence the analytical potential of the education variables.

**Sparsity and Missingness:**  
The SEA staffing files contain hundreds of columns representing detailed occupational categories within state education agencies. However, more than 90% of these fields are empty or inconsistently populated across states and years. Many staffing categories appear not to be reported at all by certain states, while others are reported sporadically. This level of sparsity makes most variables unusable for cross-state comparison or integration with crime data. The only consistently populated variable across all four target years is the broad staffing total (`FTE`, or full-time equivalent count), which becomes the basis for the project's aggregated education staffing indicator.

**Inconsistent Reporting Standards:**  
States differ significantly in how they classify and report staff. Some states consolidate categories, others split them into subcategories, and still others omit them entirely. Even within a single state, reporting conventions may shift year to year due to changes in administrative systems or shifting NCES requirements. This inconsistency undermines longitudinal comparability and makes it impossible to interpret staffing totals at a granular level.

**Temporal Misalignment and Interpretive Ambiguity:**  
The NCES data follow a school-year structure (e.g., 2015–2016), whereas the SRS data follow a calendar-year structure. Aligning these requires approximations—for example, matching the 2015–2016 school year to the 2015 calendar year. This introduces temporal ambiguity, particularly if staffing changes occur mid-year or at the beginning of a school cycle. For highly aggregated variables, this may not distort trends dramatically, but it does limit the precision of any analysis comparing crime rates and education staffing.

**Validity Concerns and Anomalous Observations:**  
Some states report implausibly low or fluctuating staffing totals, likely due to changes in reporting completeness rather than true organizational changes. These anomalies contribute noise to the dataset and reduce the interpretability of longitudinal patterns. As a result, while the staffing totals are the most reliable available variable, they remain only a coarse proxy for the true size and composition of a state's education workforce.

---

## Integration Quality

Combining the SRS and NCES datasets requires reconciling structural, temporal, and definitional differences. This integration process introduces its own data-quality considerations.

**State Identifier Harmonization:**  
State names and abbreviations must be standardized across datasets. This step is straightforward, but inconsistencies in capitalization, whitespace, and reporting format required manual correction during cleaning.

**Temporal Alignment Challenges:**  
As mentioned, school-year and calendar-year data do not align perfectly. The simplification strategy used—matching the NCES school year to the corresponding calendar year—is common in data integration workflows but inherently imperfect. This temporal smoothing may obscure short-term fluctuations or lagged relationships.

**Heterogeneous Measurement Scales:**  
Crime rates are normalized per capita, while education staffing totals are absolute counts. Integrating these requires careful attention to interpretation: staffing totals do not account for student enrollment, population size, or differences in administrative structure.

**Overall Integration Assessment:**  
Despite these limitations, the merged dataset maintains internal coherence. The crime indicators are high quality, while the education variable—though noisy—remains usable for broad exploratory purposes. However, any conclusions regarding relationships between crime and education must be tempered by awareness of the data-quality constraints described above.

---

# Findings  

The exploratory analysis conducted on the integrated crime–education dataset reveals several distinct patterns relating to temporal trends, cross-sectional variation, and internal correlation structures. Although the project’s primary purpose is not to test causal hypotheses, the findings offer insight into how these variables behave over time and across states, and they illustrate what can—and cannot—be inferred from the available administrative data.

The most striking pattern observed in the data is the consistent decline in property crime between 2015 and 2021. This trend is robust across states and is visible both in raw crime counts and in per-capita crime rates. The decreases align with long-term national patterns documented by criminologists, suggesting that macro-level social or economic forces may be driving the continued reduction in property offenses. The fact that the decline persists across multiple years and in nearly all states strengthens the conclusion that this is a genuine nationwide trend rather than an artifact of measurement error or inconsistent reporting.

Violent crime, by contrast, exhibits a different pattern. Instead of a consistent upward or downward trajectory, violent crime rates remain comparatively stable over the four selected years. State-to-state variation exists—some states show modest increases while others display slight declines—but no coherent national trend emerges. This stability suggests that violent crime may be influenced by structural factors that change more slowly or unevenly across jurisdictions. Unlike property crime, which is more sensitive to economic conditions and prevention strategies, violent crime often reflects deeper social conditions that may not fluctuate significantly over short time horizons.

The education staffing variable—an aggregated count derived from the NCES SEA files—does not exhibit meaningful temporal structure. Some states appear to experience increases in staffing over time, while others appear to decrease, but these patterns do not correspond to known policy changes or demographic shifts. Instead, the fluctuations are largely attributable to inconsistencies in reporting practices. This aligns with the data quality assessment, which identified sparsity and heterogeneity as major limitations of the NCES dataset. As a result, any interpretation of staffing levels as true indicators of state education system capacity must be made cautiously.

Cross-sectional comparisons further illustrate the limitations of the education data. In any given year, states differ substantially in their total staffing counts, but these differences correlate more strongly with population size than with any identifiable structural factors. Because the dataset lacks variables such as enrollment, expenditures, or teacher–student ratios, the staffing total does not provide enough granularity to support meaningful state-level comparisons beyond noting broad scale differences.

The correlation analysis highlights these constraints. Crime categories correlate strongly with one another, confirming expected internal structure: states with high aggravated assault rates tend to have high overall violent crime rates, and property crime subcategories move in tandem. Education staffing totals, however, show near-zero correlation with crime metrics. This absence of correlation does not imply that education systems have no relationship with crime; rather, it indicates that the available administrative variable is too crude, noisy, or inconsistently measured to capture any underlying relationship. Additionally, the temporal misalignment between school-year and calendar-year data introduces further attenuation in possible correlations.

Taken together, these findings emphasize that while crime data provide clear, interpretable patterns, the education variable’s limitations significantly restrict the analytical depth achievable in this exploratory project. Nonetheless, the integrated dataset succeeds in illustrating broad temporal and structural features of crime across U.S. states and highlights key considerations for future data collection and research design.

---

# Future Work  

The exploratory analysis in this project highlights several areas where future work could substantially improve the interpretability, accuracy, and analytical potential of the integrated crime–education dataset. Because the current dataset relies on highly aggregated staffing totals and simplified temporal alignment, the next steps should focus on expanding data richness, strengthening methodological rigor, and improving the structural integrity of the integration process.

One immediate avenue for improvement involves incorporating more detailed educational indicators. The staffing totals used here provide only a coarse snapshot of state education systems and do not reflect important factors such as student–teacher ratios, instructional expenditures, class sizes, or staff distribution across districts. Adding variables from NCES district-level datasets—such as finance data, enrollment counts, or teacher retention rates—would capture more substantive variation and allow for deeper investigation into the relationship between education systems and crime. Because educational environments vary widely within states, these more granular indicators could reveal patterns obscured by the aggregated staffing variable.

A second direction for future work involves integrating socioeconomic variables from external sources such as the U.S. Census Bureau or the American Community Survey. Crime and education outcomes are deeply intertwined with broader social and economic conditions, including poverty rates, unemployment, population density, and demographic composition. Without controlling for these structural factors, any observed relationships between crime and education risk being confounded. Incorporating these variables into the merged dataset would make any subsequent modeling efforts more valid and informative.

Another important enhancement concerns temporal alignment. The present analysis matches academic-year staffing data to calendar-year crime data through a simple correspondence, but this approximation introduces measurement noise. Future work could explore lag structures—where education metrics from one school year are linked to crime outcomes in the following year—or weighted averaging across overlapping years. These refinements would improve temporal specificity and help uncover whether changes in education systems precede shifts in crime levels.

Increasing geographic resolution represents another promising direction. State-level analysis is useful for identifying broad trends, but it masks substantial within-state variation. Shifting to county-level crime data (available through NIBRS or state reporting systems) and district-level education data would produce a more nuanced dataset capable of detecting local patterns. This would also support more rigorous causal designs by increasing the number of observations and capturing heterogeneity across communities.

Finally, future work could incorporate more advanced modeling frameworks. With improved data quality and additional variables, statistical approaches such as fixed-effects panel models, multilevel hierarchical models, or structural time-series models could be used to examine how changes within states relate to changes in crime and education over time. At present, the limitations of the staffing dataset prevent such modeling from being meaningful, but the integrated structure developed in this project provides a foundation that can be extended as better data become available.

In summary, future improvements should focus on expanding the educational data, integrating socioeconomic context, refining temporal matching, increasing geographic detail, and applying more robust analytical methods. These enhancements would significantly deepen the insights that can be drawn from the dataset and allow for a more comprehensive understanding of the complex relationship between crime and public education systems.

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
