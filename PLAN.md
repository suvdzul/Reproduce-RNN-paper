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
Gastric Gastric Tube | 226571,226573,226575,226576 | First 48 hrs after admission |  | outputevents | 
Stool Out Stool | 226579| First 48 hrs after admission |  | outputevents | 
Urine Out Incontinent | 226627| First 48 hrs after admission |  | outputevents | 
Ultrafiltrate|226583 | First 48 hrs after admission |  | outputevents | 
Foley| | First 48 hrs after admission |  | outputevents | 
Void| |First 48 hrs after admission  |  | outputevents | 
Condom Cath| | First 48 hrs after admission |  | outputevents | 
Fecal Bag|226580, 227701 | First 48 hrs after admission |  | outputevents | 
Ostomy (Output)| 226582| First 48 hrs after admission |  | outputevents | 
Chest Tube #1|226588 |First 48 hrs after admission  |  | outputevents | 
Chest Tube #2|226589 | First 48 hrs after admission |  | outputevents | 
Jackson Pratt #1|226599 | First 48 hrs after admission |  | outputevents | 
OR EBL|226626 | First 48 hrs after admission |  | outputevents | 
Pre-Admission|226633 | First 48 hrs after admission |  | outputevents | 
TF Residual|227510, 227511 | First 48 hrs after admission |  | outputevents | 
Albumin 5%|220864 | First 48 hrs after admission |  | inputevents | 
Dextrose 5%| | First 48 hrs after admission |  | inputevents | 
Fresh Frozen Plasma| 220970|First 48 hrs after admission  |  | inputevents | 
Lorazepam (Ativan)| 221385| First 48 hrs after admission |  | inputevents | 
Calcium Gluconate| 221456| First 48 hrs after admission |  | inputevents | 
Midazolam (Versed)|221668 | First 48 hrs after admission |  | inputevents | 
Phenylephrine| 221749| First 48 hrs after admission |  | inputevents | 
Furosemide (Lasix)| 221794| First 48 hrs after admission |  | inputevents | 
Hydralazine| 221828| First 48 hrs after admission |  | inputevents | 
Norepinephrine| 221906| First 48 hrs after admission |  | inputevents | 
Magnesium Sulfate|222011 |First 48 hrs after admission  |  | inputevents | 
Nitroglycerin| 222056| First 48 hrs after admission |  | inputevents | 
Insulin - Regular| 223258, 223262| First 48 hrs after admission |  | inputevents | 
Insulin - Glargine| | First 48 hrs after admission |  | inputevents | 
Insulin - Humalog| | First 48 hrs after admission |  | inputevents | 
Heparin Sodium| | First 48 hrs after admission |  | inputevents | 
Morphine Sulfate| 225154| First 48 hrs after admission |  | inputevents | 
Potassium Chloride|225166 | First 48 hrs after admission |  | inputevents | 
Packed Red Blood Cells| 225168|  First 48 hrs after admission|  | inputevents |
Gastric Meds| 225799| First 48 hrs after admission |  | inputevents | 
D5 1/2NS|225823 |First 48 hrs after admission  |  | inputevents | 
LR| 225828| First 48 hrs after admission |  | inputevents | 
K Phos|225834 | First 48 hrs after admission |  | inputevents | 
Solution|225943 | First 48 hrs after admission |  | inputevents | 
Sterile Water|225944 | First 48 hrs after admission |  | inputevents | 
Metoprolol| | First 48 hrs after admission |  | inputevents | 
Piggyback| 226089|First 48 hrs after admission  |  | inputevents | 
OR Crystalloid Intake|226364 | First 48 hrs after admission |  | inputevents |
OR Cell Saver Intake| 226372| First 48 hrs after admission |  | inputevents | 
PO Intake|226452 |  First 48 hrs after admission|  | inputevents | 
GT Flush|226453 | First 48 hrs after admission |  | inputevents | 
KCL (Bolus)|227522 |First 48 hrs after admission  |  | inputevents | 
Magnesium Sulfate (Bolus)|222011, 227523, 227524 | First 48 hrs after admission |  | inputevents |
Hematocrit|51221 | First 48 hrs after admission |  | labevents |
White Blood Cells| |First 48 hrs after admission  |  | labevents |
Platelet Count|51265 |First 48 hrs after admission  |  | labevents |
Hemoglobin|51222 | First 48 hrs after admission |  | labevents |
MCHC|51249 |First 48 hrs after admission  |  | labevents |
MCH|51248 |First 48 hrs after admission  |  | labevents |
MCV| 51250|First 48 hrs after admission  |  | labevents |
Red Blood Cells|51279 |  First 48 hrs after admission|  | labevents |
RDW|51277 |  First 48 hrs after admission|  | labevents |
Potassium| | First 48 hrs after admission |  | labevents |
Sodium| | First 48 hrs after admission |  | labevents |
Chloride|50902 | First 48 hrs after admission |  | labevents |
Bicarbonate| | First 48 hrs after admission |  | labevents |
Anion Gap| 50868|First 48 hrs after admission  |  | labevents |
Urea Nitrogen| | First 48 hrs after admission |  | labevents |
Creatinine|50912 |First 48 hrs after admission  |  | labevents |
Glucose|50931 | First 48 hrs after admission |  | labevents |
Magnesium|50960 |First 48 hrs after admission  |  | labevents |
Total Calcium| 50893|First 48 hrs after admission  |  | labevents |
Phosphate|50970 |First 48 hrs after admission  |  | labevents |
INR(PT)| 51237| First 48 hrs after admission |  | labevents |
PT| 51274| First 48 hrs after admission |  | labevents |
PTT|51275 |First 48 hrs after admission  |  | labevents |
Lymphocytes|51244 | First 48 hrs after admission |  | labevents |
Monocytes|51254 | First 48 hrs after admission |  | labevents |
Neutrophils|51256 |First 48 hrs after admission  |  | labevents |
Basophils|51146 | First 48 hrs after admission |  | labevents |
Eosinophils|51200 |First 48 hrs after admission  |  | labevents |
Total Bilirubin| | First 48 hrs after admission |  | labevents |
PH| 50820| First 48 hrs after admission |  | labevents |
Base Excess|50802 |First 48 hrs after admission  |  | labevents |
Calculated Total CO2| 50804|First 48 hrs after admission  |  | labevents |
PO2| |First 48 hrs after admission  |  | labevents |
PCO2| 50818|First 48 hrs after admission  |  | labevents |
PH| 51491| First 48 hrs after admission |  | labevents |
Specific Gravity|51498 | First 48 hrs after admission |  | labevents |
Lactate|50813 | First 48 hrs after admission |  | labevents |
Alanine Aminotransferase (ALT)| 50861|First 48 hrs after admission  |  | labevents |
Asparate Aminotransferase (AST)|50878 |First 48 hrs after admission  |  | labevents |
Alkaline Phosphatase| 50863| First 48 hrs after admission |  | labevents |
Albumin|50862 |First 48 hrs after admission  |  | labevents |
Aspirin|ASA325, ASA81 |First 48 hrs after admission  |  | prescription |
Bisacodyl| BISA10R, BISA5|First 48 hrs after admission  |  | prescription |
Docusate Sodium| DOCU100| First 48 hrs after admission |  | prescription |
DSW|HEPBASE | First 48 hrs after admission |  | prescription |
Humulin-R Insulin| INHRIV, INSULIN|First 48 hrs after admission  |  | prescription |
Potassium Chloride|KCL40I, MICROK10 | First 48 hrs after admission |  | prescription |
Magnesium Sulfate|MAG2PM |First 48 hrs after admission  |  | prescription |
Metoprolol Tartrate|METO25, METO50 | First 48 hrs after admission |  | prescription |
Sodium Chloride 0.9% Flush| 	NACLFLUSH, NS1000| First 48 hrs after admission |  | prescription |
Pantoprazole|	PANT40, PANT40I | First 48 hrs after admission |  | prescription |

If unsure about the source, write all possibilities, and justify them in the notes.
Also include in the notes whether outliers were processed (and how), as well as the approach for missing data.

### Outcome(s)

List the outcome(s) used in the study, e.g. 28-day mortality, with similar detail as the above variables.

Variable name | Description | Timing | Aggregation | Source | Notes
--- | --- | --- | --- | --- | ---
ICD Code | Codes for diagnoses/procedures during a stay | Anytime during the first 48 hours after admission | Group them accordingly into 20 categories | diagnoses_icd and procedures_icd | Create binary variables for each category indicating whether the patient was diagnosed or had a procedure for that certain category
in-hospital mortality | Whether the patient died in the hospital | Anytime during the hospital stay for that admission | Deathtime not null indicates mortality | admissions | 
48 hr < mortality |Whether the patient died 48 hours after admission | Anytime during their stay but 48 hours after admission | deathtime - admittime > 48 or not if deathtime not null| admissions | 
