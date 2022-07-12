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

Variable name | Description (item id) | Timing | Aggregation | Source | Notes
--- | --- | --- | --- | --- | ---
Gastric Gastric Tube |  | First 48 hrs after admission |  | outputevents | 
Stool Out Stool | | First 48 hrs after admission |  | outputevents | 
Urine Out Incontinent | | First 48 hrs after admission |  | outputevents | 
Ultrafiltrate| | First 48 hrs after admission |  | outputevents | 
Foley| | First 48 hrs after admission |  | outputevents | 
Void| |First 48 hrs after admission  |  | outputevents | 
Condom Cath| | First 48 hrs after admission |  | outputevents | 
Fecal Bag| | First 48 hrs after admission |  | outputevents | 
Ostomy (Output)| | First 48 hrs after admission |  | outputevents | 
Chest Tube #1| |First 48 hrs after admission  |  | outputevents | 
Chest Tube #2| | First 48 hrs after admission |  | outputevents | 
Jackson Pratt #1| | First 48 hrs after admission |  | outputevents | 
OR EBL| | First 48 hrs after admission |  | outputevents | 
Pre-Admission| | First 48 hrs after admission |  | outputevents | 
TF Residual| | First 48 hrs after admission |  | outputevents | 
Albumin 5%| | First 48 hrs after admission |  | inputevents | 
Dextrose 5%| | First 48 hrs after admission |  | inputevents | 
Fresh Frozen Plasma| |First 48 hrs after admission  |  | inputevents | 
Lorazepam (Ativan)| | First 48 hrs after admission |  | inputevents | 
Calcium Gluconate| | First 48 hrs after admission |  | inputevents | 
Midazolam (Versed)| | First 48 hrs after admission |  | inputevents | 
Phenylephrine| | First 48 hrs after admission |  | inputevents | 
Furosemide (Lasix)| | First 48 hrs after admission |  | inputevents | 
Hydralazine| | First 48 hrs after admission |  | inputevents | 
Norepinephrine| | First 48 hrs after admission |  | inputevents | 
Magnesium Sulfate| |First 48 hrs after admission  |  | inputevents | 
Nitroglycerin| | First 48 hrs after admission |  | inputevents | 
Insulin - Regular| | First 48 hrs after admission |  | inputevents | 
Insulin - Glargine| | First 48 hrs after admission |  | inputevents | 
Insulin - Humalog| | First 48 hrs after admission |  | inputevents | 
Heparin Sodium| | First 48 hrs after admission |  | inputevents | 
Morphine Sulfate| | First 48 hrs after admission |  | inputevents | 
Potassium Chloride| | First 48 hrs after admission |  | inputevents | 
Packed Red Blood Cells| |  First 48 hrs after admission|  | inputevents |
Gastric Meds| | First 48 hrs after admission |  | inputevents | 
D5 1/2NS| |First 48 hrs after admission  |  | inputevents | 
LR| | First 48 hrs after admission |  | inputevents | 
K Phos| | First 48 hrs after admission |  | inputevents | 
Solution| | First 48 hrs after admission |  | inputevents | 
Sterile Water| | First 48 hrs after admission |  | inputevents | 
Metoprolol| | First 48 hrs after admission |  | inputevents | 
Piggyback| |First 48 hrs after admission  |  | inputevents | 
OR Crystalloid Intake| | First 48 hrs after admission |  | inputevents |
OR Cell Saver Intake| | First 48 hrs after admission |  | inputevents | 
PO Intake| |  First 48 hrs after admission|  | inputevents | 
GT Flush| | First 48 hrs after admission |  | inputevents | 
KCL (Bolus)| |First 48 hrs after admission  |  | inputevents | 
Magnesium Sulfate (Bolus)| | First 48 hrs after admission |  | inputevents |
Hematocrit| | First 48 hrs after admission |  | labevents |
White Blood Cells| |First 48 hrs after admission  |  | labevents |
Platelet Count| |First 48 hrs after admission  |  | labevents |
Hemoglobin| | First 48 hrs after admission |  | labevents |
MCHC| |First 48 hrs after admission  |  | labevents |
MCH| |First 48 hrs after admission  |  | labevents |
MCV| |First 48 hrs after admission  |  | labevents |
Red Blood Cells| |  First 48 hrs after admission|  | labevents |
RDW| |  First 48 hrs after admission|  | labevents |
Potassium| | First 48 hrs after admission |  | labevents |
Sodium| | First 48 hrs after admission |  | labevents |
Chloride| | First 48 hrs after admission |  | labevents |
Bicarbonate| | First 48 hrs after admission |  | labevents |
Anion Gap| |First 48 hrs after admission  |  | labevents |
Urea Nitrogen| | First 48 hrs after admission |  | labevents |
Creatinine| |First 48 hrs after admission  |  | labevents |
Glucose| | First 48 hrs after admission |  | labevents |
Magnesium| |First 48 hrs after admission  |  | labevents |
Total Calcium| |First 48 hrs after admission  |  | labevents |
Phosphate| |First 48 hrs after admission  |  | labevents |
INR(PT)| | First 48 hrs after admission |  | labevents |
PT| | First 48 hrs after admission |  | labevents |
PTT| |First 48 hrs after admission  |  | labevents |
Lymphocytes| | First 48 hrs after admission |  | labevents |
Monocytes| | First 48 hrs after admission |  | labevents |
Neutrophils| |First 48 hrs after admission  |  | labevents |
Basophils| | First 48 hrs after admission |  | labevents |
Eosinophils| |First 48 hrs after admission  |  | labevents |
Total Bilirubin| | First 48 hrs after admission |  | labevents |
PH| | First 48 hrs after admission |  | labevents |
Base Excess| |First 48 hrs after admission  |  | labevents |
Calculated Total CO2| |First 48 hrs after admission  |  | labevents |
PO2| |First 48 hrs after admission  |  | labevents |
PCO2| |First 48 hrs after admission  |  | labevents |
PH| | First 48 hrs after admission |  | labevents |
Specific Gravity| | First 48 hrs after admission |  | labevents |
Lactate| | First 48 hrs after admission |  | labevents |
Alanine Aminotransferase (ALT)| |First 48 hrs after admission  |  | labevents |
Asparate Aminotransferase (AST)| |First 48 hrs after admission  |  | labevents |
Alkaline Phosphatase| | First 48 hrs after admission |  | labevents |
Albumin| |First 48 hrs after admission  |  | labevents |
Aspirin| |First 48 hrs after admission  |  | prescription |
Bisacodyl| |First 48 hrs after admission  |  | prescription |
Docusate Sodium| | First 48 hrs after admission |  | prescription |
DSW| | First 48 hrs after admission |  | prescription |
Humulin-R Insulin| |First 48 hrs after admission  |  | prescription |
Potassium Chloride| | First 48 hrs after admission |  | prescription |
Magnesium Sulfate| |First 48 hrs after admission  |  | prescription |
Metoprolol Tartrate| | First 48 hrs after admission |  | prescription |
Sodium Chloride 0.9% Flush| | First 48 hrs after admission |  | prescription |
Pantoprazole| | First 48 hrs after admission |  | prescription |

If unsure about the source, write all possibilities, and justify them in the notes.
Also include in the notes whether outliers were processed (and how), as well as the approach for missing data.

### Outcome(s)

List the outcome(s) used in the study, e.g. 28-day mortality, with similar detail as the above variables.

Variable name | Description | Timing | Aggregation | Source | Notes
--- | --- | --- | --- | --- | ---
ICD Code | Codes for diagnoses/procedures during a stay | Anytime during the first 48 hours after admission | Group them accordingly into 20 categories | diagnoses_icd and procedures_icd | Create binary variables for each category indicating whether the patient was diagnosed or had a procedure for that certain category
in-hospital mortality | Whether the patient died in the hospital | Anytime during the hospital stay for that admission | Deathtime not null indicates mortality | admissions | 
48 hr < mortality |Whether the patient died 48 hours after admission | Anytime during their stay but 48 hours after admission | deathtime - admittime > 48 or not if deathtime not null| admissions | 
