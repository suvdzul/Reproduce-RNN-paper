# Reproduction template

The goal of this template is to guide documentation of a reproduction of a study in an electronic health record database. Reproductions are assumed to be retrospective observational studies.

This template is based on material from the OSF, as well as from Brandt et al., 2013.

## Title of the study

Original Study:
* Title: Recurrent Neural Networks for Multivariate Time Series with Missing Values
* DOI:10.1038/s41598-018-24271-9

Reproduction/Replication Study:
* Title: Replicate-RNN-paper: RNN missing value
* DOI:10.5281/zenodo.6678012

## Dataset(s) used

The original dataset used for the study was MIMIC-III, which is different than the one that will be used for replication.

The dataset to be used for the replication:
* Dataset name and version: MIMIC-IV v0.3
* DOI (or link if no DOI available): https://doi.org/10.13026/a3wn-hq05
* Citation: 
1. Johnson, A., Bulgarelli, L., Pollard, T., Horng, S., Celi, L. A., & Mark, R. (2020). MIMIC-IV (version 0.4). PhysioNet. https://doi.org/10.13026/a3wn-hq05.
2. Goldberger, A., Amaral, L., Glass, L., Hausdorff, J., Ivanov, P. C., Mark, R., ... & Stanley, H. E. (2000). PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals. Circulation [Online]. 101 (23), pp. e215–e220.
* Other relevant information (link to dataset documentation, etc): https://physionet.org/content/mimiciv/0.4/

## Data extraction

### Inclusion/Exclusion criteria

Provide the column names for your proposed "cohort" table, which will apply all inclusion/exclusion criteria. Include the description of the criteria, the table in the dataset you will use, and how missing data will be interpreted (e.g. missing values will be assumed to include the patient).

First create an admission list for admissions in the ICU between 2008 and 2012, excluding non-first admissions and create "admission id" column. Next, get item id list from inputevents, outputevents, labevents, and prescription tables. For each item id in the table, get number of units of each itemid and choose the major unit as the target of unit conversion and discard the observations with minority unit of measurement if the top one has more than 90% of the total, otherwise convert. For each of the ICU admission records, collect both the variable value and the time-stamp of observation. Records for the first 48 hours after admission are included only, thus exclude_timediff_above48 column will be added which indicates whether the observation is after the first 48 hours:
* Inputevents: admittime - starttime > 48, discard events without starttime or amount/amountuom
* Outputevents: admittime - charttime > 48, discard events without charttime or value/valueuom
* Labevents: admittime - charttime > 48, discard events without charttime or value/valueuom/valuenum
* Prescriptions: admittime - starttime > 48, discard events without starttime or amount/amountuom, discard none values

For non-temporal outcome variables, get each patient's ICD-9 Codes for the admission and their mortality labels. 

For mortality, 3 columns:
* in-hospital mortality: deathtime is not null
* 48 < mortality: deathtime - admittime > 48 hrs if deathtime not null
* Patients who were alive for the first 48 hours after admission are included, thus exclude_mortality_under48 column will be added which would be an indicator variable for deathtime - admittime <= 48. 

Group the ICD-9 Codes as the Table 2 in Section 3.4 of the Supplementary Information and create columns for each of the 20 categories, indicating whether the patient had the diagnoses or the procedures during their stay in the ICU.

### Variables

List out the planned source for all covariates and exposures extracted for the study, e.g. admission source.
If describing a time-varying covariate, be specific regarding the aggregation and the time window (e.g. "lowest mean arterial pressure during the first 24 hours of the ICU stay."). The following template is a useful guide.

For all the variables listed below, the value and the timestamp will be extracted for each admission id recorded for the first 48 hours after first ICU admission.

Variable name | Description | Timing | Aggregation | Source | Notes
--- | --- | --- | --- | --- | ---
Gastric Gastric Tube | Insertion of a tube into the stomach |  |  | outputevents | 
Stool Out Stool | |  |  | outputevents | 
Urine Out Incontinent | |  |  | outputevents | 
Ultrafiltrate| |  |  | outputevents | 
Foley| |  |  | outputevents | 
Void| |  |  | outputevents | 
Condom Cath| |  |  | outputevents | 
Fecal| |  |  | outputevents | 
Bag| |  |  | outputevents | 
Ostomy (Output)| |  |  | outputevents | 
Chest Tube #1| |  |  | outputevents | 
Chest Tube #2| |  |  | outputevents | 
Jackson Pratt #1| |  |  | outputevents | 
OR EBL| |  |  | outputevents | 
Pre-Admission| |  |  | outputevents | 
TF Residual| |  |  | outputevents | 
Albumin 5%| |  |  | inputevents | 
Dextrose 5%| |  |  | inputevents | 
Fresh Frozen Plasma| |  |  | inputevents | 
Lorazepam (Ativan)| |  |  | inputevents | 
Calcium Gluconate| |  |  | inputevents | 
Midazolam (Versed)| |  |  | inputevents | 
Phenylephrine| |  |  | inputevents | 
Furosemide (Lasix)| |  |  | inputevents | 
Hydralazine| |  |  | inputevents | 
Norepinephrine| |  |  | inputevents | 
Magnesium Sulfate| |  |  | inputevents | 
Nitroglycerin| |  |  | inputevents | 
Insulin - Regular| |  |  | inputevents | 
Insulin - Glargine| |  |  | inputevents | 
Insulin - Humalog| |  |  | inputevents | 
Heparin Sodium| |  |  | inputevents | 
Morphine Sulfate| |  |  | inputevents | 
Potassium Chloride| |  |  | inputevents | 
Packed Red Blood Cells| |  |  | inputevents |
Gastric Meds| |  |  | inputevents | 
D5 1/2NS| |  |  | inputevents | 
LR| |  |  | inputevents | 
K Phos| |  |  | inputevents | 
Solution| |  |  | inputevents | 
Sterile Water| |  |  | inputevents | 
Metoprolol| |  |  | inputevents | 
Piggyback| |  |  | inputevents | 
OR Crystalloid Intake| |  |  | inputevents |
OR Cell Saver Intake| |  |  | inputevents | 
PO Intake| |  |  | inputevents | 
GT Flush| |  |  | inputevents | 
KCL (Bolus)| |  |  | inputevents | 
Magnesium Sulfate (Bolus)| |  |  | inputevents |
Hematocrit| |  |  | labevents |
White Blood Cells| |  |  | labevents |
Platelet Count| |  |  | labevents |
Hemoglobin| |  |  | labevents |
MCHC| |  |  | labevents |
MCH| |  |  | labevents |
MCV| |  |  | labevents |
Red Blood Cells| |  |  | labevents |
RDW| |  |  | labevents |
Potassium| |  |  | labevents |
Sodium| |  |  | labevents |
Chloride| |  |  | labevents |
Bicarbonate| |  |  | labevents |
Anion Gap| |  |  | labevents |
Urea Nitrogen| |  |  | labevents |
Creatinine| |  |  | labevents |
Glucose| |  |  | labevents |
Magnesium| |  |  | labevents |
Total Calcium| |  |  | labevents |
Phosphate| |  |  | labevents |
INR(PT)| |  |  | labevents |
PT| |  |  | labevents |
PTT| |  |  | labevents |
Lymphocytes| |  |  | labevents |
Monocytes| |  |  | labevents |
Neutrophils| |  |  | labevents |
Basophils| |  |  | labevents |
Eosinophils| |  |  | labevents |
Total Bilirubin| |  |  | labevents |
PH| |  |  | labevents |
Base Excess| |  |  | labevents |
Calculated Total CO2| |  |  | labevents |
PO2| |  |  | labevents |
PCO2| |  |  | labevents |
PH| |  |  | labevents |
Specific Gravity| |  |  | labevents |
Lactate| |  |  | labevents |
Alanine Aminotransferase (ALT)| |  |  | labevents |
Asparate Aminotransferase (AST)| |  |  | labevents |
Alkaline Phosphatase| |  |  | labevents |
Albumin| |  |  | labevents |
Aspirin| |  |  | prescription |
Bisacodyl| |  |  | prescription |
Docusate Sodium| |  |  | prescription |
DSW| |  |  | prescription |
Humulin-R Insulin| |  |  | prescription |
Potassium Chloride| |  |  | prescription |
Magnesium Sulfate| |  |  | prescription |
Metoprolol Tartrate| |  |  | prescription |
Sodium Chloride 0.9% Flush| |  |  | prescription |
Pantoprazole| |  |  | prescription |

If unsure about the source, write all possibilities, and justify them in the notes.
Also include in the notes whether outliers were processed (and how), as well as the approach for missing data.

### Outcome(s)

List the outcome(s) used in the study, e.g. 28-day mortality, with similar detail as the above variables.

Variable name | Description | Timing | Aggregation | Source | Notes
--- | --- | --- | --- | --- | ---
ICD Code | Codes for diagnoses/procedures during a stay | Anytime during the first 48 hours after admission | Group them accordingly into 20 categories | diagnoses_icd and procedures_icd | Create binary variables for each category indicating whether the patient was diagnosed or had a procedure for that certain category
in-hospital mortality | Whether the patient died in the hospital | Anytime during the hospital stay for that admission | Deathtime not null indicates mortality | admissions | 
> 48 hr mortality |Whether the patient died 48 hours after admission | Anytime during their stay but 48 hours after admission | deathtime - admittime > 48 or not if deathtime not null| admissions | 
