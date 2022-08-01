The dataset to be used for the replication:

Dataset name and version: MIMIC-IV v0.3
DOI (or link if no DOI available): https://doi.org/10.13026/a3wn-hq05
Citation:
Johnson, A., Bulgarelli, L., Pollard, T., Horng, S., Celi, L. A., & Mark, R. (2020). MIMIC-IV (version 0.4). PhysioNet. https://doi.org/10.13026/a3wn-hq05.
Goldberger, A., Amaral, L., Glass, L., Hausdorff, J., Ivanov, P. C., Mark, R., ... & Stanley, H. E. (2000). PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals. Circulation [Online]. 101 (23), pp. e215–e220.
Other relevant information (link to dataset documentation, etc): https://physionet.org/content/mimiciv/0.4/

1. For cohort extraction and the first outcome, mortality, go to acquire -> cohort -> Cohort_Mortality. 2 tables were created in this notebook, first one includes all ICU stays in MIMIC-IV. This table shows ICU intime, admission year (year), death status, and death hours (if died in hospital) for all patients admitted to ICU. The second table applies the inclusions/exclusions relevant for this study, which are:
* include first ICU stays only
* include admissions between 2008 and 2012
* include patients who were alive for the first 48h after admission
This table also contains mortality (48h after admission) labels.
2. For ICD-9 codes, go to acquire -> cohort -> Cohort_ICD_Codes, this table contains ICD-9 diagnoses codes for each admission record in the cohort which is grouped as per the Table 2, Section 3.4 of the Supplementary Information of the original study. This table contains 20 columns for each ICD-9 code category indicating presence/absence of the diagnostic group code.
3. For covariates extraction go to acquire -> covariates, which has a separate notebook for each table (inputevents, outputevents, labevents, and prescriptions) extraction. Each of the 99 covariates listed in Table 1, Section 3.4 of the Supplementary Information of the original study were extracted here. For labels with multiple itemids, each of their occurrences were counted and minorities were converted to the majority UoM as well as their labels were replaced as the majority.
For each table generation, the following inclusions/exclusions applied:
* only include records for the first 48hrs after admission 
* exclude records with N/A amount/value/valuenum/dose
* exclude records with N/A UoM
* exclude records with missing charttime or starttime
* exclude records with 0 amount/value/valuenum/dose
5. For analysis on outcome variables, go to analyze -> Primary_Analysis. For exploratory analysis on the covariates, go to analyze -> Exploratory_Analysis.
6. The reproduced or produced graphs are included in analyze - > graphs
