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

First create a full cohort table for ICU admissions including subject id, stay id, admission id, ICU admission time (time stamp), ICU admission year, mortality status or in-hospital mortality (deathtime is not null), as well as mortality time. Then apply the following exclusions/inclusions to derive the final cohort table:
* Only include patients admitted in the ICU between 2008 and 2012 (exclude_admityear)
* Exclude non-first admissions (exclude_stay)
* Exclude patients with mortality under 48h (exclude_mortality_under48) where deathtime - intime <=48h.

The final cohort table after applying inclusion/exclusion criterias will have the following columns: subject id, stay id, admission id, admission year, hours until death as well as mortality label which is one of the classification task.

Since the other classification task is predicting ICD-9 codes with 20 categories, another table with subject id, stay id, admission id, and 20 columns for each category of the 20 categories of ICD-9 codes as per Table 2  in Section 3.4 of the Supplementary information needs to be created. Each column would indicate whether or not the patient was billed for that diagnoses group during their ICU stay, therefore creating a binary variable for each group of codes, thus only a maximum of one case/billing per diagnoses group would be considered. If there are any patient stays with no ICD-9 code/group, they would be excluded as well.

Next, using item id list from Variables section in the PLAN for inputevents, outputevents, labevents, and prescription tables. For labels with duplicate item ids, get number of units of each itemid and choose the major unit as the target of unit conversion and convert the minorities, as well as replace the label as the majority label. I will also replace the label as the majority label when itemids are duplicate. For each of the ICU admission records, collect both the variable value and the time-stamp of observation. Records for the first 48 hours after admission are included only, thus exclude_after48h column will be added which indicates whether the observation is after the first 48 hours:
* Inputevents: intime - starttime > 48, discard events without starttime or amount or amountuom or when amount is 0
* Outputevents: intime - charttime > 48, discard events without charttime or value or valueuom or when value is 0
* Labevents: intime - charttime > 48, discard events without charttime or value/valuenum or valueuom or when value/valuenum is 0
* Prescriptions: intime - starttime > 48, discard events without starttime or amount, discard none value

Then I need to create an hour variable that calculates the time difference of the event and the admission to represent the timestep of each record, which needs to be aggregated for each variable, for each timestep, for each stay as well.

### Variables

For all the variables listed below, the value and the timestamp will be extracted for each admission id recorded for the first 48 hours after first ICU admission. Aggregation methods are TBD (inputevents and outputevents will be averaged as it was not clear how each variable was aggregated in the original study, it makes sense for me to average them). Item ids in bold are manually derived from MIMIC-IV, the rest are collected from https://github.com/USC-Melady/Benchmarking_DL_MIMICIII/blob/master/Codes/mimic3_mvcv/config/99plusf.csv.

Variable name | Description (item id/drug) | Timing | Aggregation | Source | Notes
--- | --- | --- | --- | --- | ---
Gastric Gastric Tube | 226571 (21.6%),226573 (3.8%),226575 (30.9%),226576 (43.7%) | First 48 hrs after admission |  | outputevents | 
Stool Out Stool | 226579| First 48 hrs after admission |  | outputevents | 
Urine Out Incontinent | 226627| First 48 hrs after admission |  | outputevents | 
Ultrafiltrate|226583 | First 48 hrs after admission |  | outputevents | 
Foley|****226559**** | First 48 hrs after admission |  | outputevents | 
Void|****226560**** |First 48 hrs after admission  |  | outputevents | 
Condom Cath| ****226561****| First 48 hrs after admission |  | outputevents | 
Fecal Bag|226580 (35%), 227701 (65%) | First 48 hrs after admission |  | outputevents | 
Ostomy (Output)| 226582| First 48 hrs after admission |  | outputevents | 
Chest Tube #1|226588 |First 48 hrs after admission  |  | outputevents | 
Chest Tube #2|226589 | First 48 hrs after admission |  | outputevents | 
Jackson Pratt #1|226599 | First 48 hrs after admission |  | outputevents | 
OR EBL|226626 | First 48 hrs after admission |  | outputevents | 
Pre-Admission|226633 | First 48 hrs after admission |  | outputevents | 
TF Residual|227510 (87%), 227511 (13%) | First 48 hrs after admission |  | outputevents | 
Albumin 5%|220864 | First 48 hrs after admission |  | inputevents | 
Dextrose 5%|****220949**** | First 48 hrs after admission |  | inputevents | 
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
Insulin - Regular| 223258 (86.5%), 223262 (13.5%)| First 48 hrs after admission |  | inputevents | 
Insulin - Glargine| ****223260**** | First 48 hrs after admission |  | inputevents | 
Insulin - Humalog|****223261****  | First 48 hrs after admission |  | inputevents | 
Heparin Sodium|****225152**** | First 48 hrs after admission |  | inputevents | 
Morphine Sulfate| 225154| First 48 hrs after admission |  | inputevents | 
Potassium Chloride|225166 | First 48 hrs after admission |  | inputevents | 
Packed Red Blood Cells| 225168|  First 48 hrs after admission|  | inputevents |
Gastric Meds| 225799| First 48 hrs after admission |  | inputevents | 
D5 1/2NS|225823 |First 48 hrs after admission  |  | inputevents | 
LR| 225828| First 48 hrs after admission |  | inputevents | 
K Phos|225834 | First 48 hrs after admission |  | inputevents | 
Solution|225943 | First 48 hrs after admission |  | inputevents | 
Sterile Water|225944 | First 48 hrs after admission |  | inputevents | 
Metoprolol|  ****225974**** | First 48 hrs after admission |  | inputevents | 
Piggyback| 226089|First 48 hrs after admission  |  | inputevents | 
OR Crystalloid Intake|226364 | First 48 hrs after admission |  | inputevents |
OR Cell Saver Intake| 226372| First 48 hrs after admission |  | inputevents | 
PO Intake|226452 |  First 48 hrs after admission|  | inputevents | 
GT Flush|226453 | First 48 hrs after admission |  | inputevents | 
KCL (Bolus)|227522 |First 48 hrs after admission  |  | inputevents | 
Magnesium Sulfate (Bolus)|222011 (58.9%), 227523 (40.9%), 227524 (0.2%) | First 48 hrs after admission |  | inputevents |
Hematocrit|51221 | First 48 hrs after admission |  | labevents |
White Blood Cells|****51755 (0%), 51756 (0%), 51301 (100%)**** |First 48 hrs after admission  |  | labevents |
Platelet Count|51265 |First 48 hrs after admission  |  | labevents |
Hemoglobin|51222 | First 48 hrs after admission |  | labevents |
MCHC|51249 |First 48 hrs after admission  |  | labevents |
MCH|51248 |First 48 hrs after admission  |  | labevents |
MCV| 51250|First 48 hrs after admission  |  | labevents |
Red Blood Cells|51279 |  First 48 hrs after admission|  | labevents |
RDW|51277 |  First 48 hrs after admission|  | labevents |
Potassium|****50833 (0%), 50971 (100%), 52610 (0%)**** | First 48 hrs after admission |  | labevents |
Sodium| ****50983 (100%), 52623 (0%)****| First 48 hrs after admission |  | labevents |
Chloride|50902 | First 48 hrs after admission |  | labevents |
Bicarbonate|****50882**** | First 48 hrs after admission |  | labevents |
Anion Gap| 50868|First 48 hrs after admission  |  | labevents |
Urea Nitrogen| ****51006 (100%), 52647 (0%)**** | First 48 hrs after admission |  | labevents |
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
Total Bilirubin| ****50885**** | First 48 hrs after admission |  | labevents |
PH| 50820| First 48 hrs after admission |  | labevents |
Base Excess|50802 |First 48 hrs after admission  |  | labevents |
Calculated Total CO2| 50804|First 48 hrs after admission  |  | labevents |
PO2| ****52042 (0%), 50821 (100%)****  |First 48 hrs after admission  |  | labevents |
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

ICD-9 Codes were categorized into 20 classes (20 binary variables, i.e., one-hot-encoding.. kind of?) as per Table 2 Supplementary Info. Mortality after 48 hrs is binary.

Variable name | Description | Timing | Aggregation | Source | Notes
--- | --- | --- | --- | --- | ---
ICD Code | Codes for diagnoses during a stay | Anytime during the stay for patients who were alive during the first 48 hours after admission | Categorize them accordingly into 20 categories and aggegrate by MAX occurence (1 billing for each diagnosis group) for each stay | diagnoses_icd | Create binary variables for each category indicating whether the patient was diagnosed for that certain category - One admission can have several diagnoses during a stay (multi-task classification)
in-hospital mortality | Whether the patient died in the hospital | Anytime during the hospital stay for that admission | Deathtime not null indicates mortality | admissions | 
48 hr < mortality |Whether the patient died 48 hours after admission | Anytime during their stay but 48 hours after admission | deathtime - admittime > 48 or not if deathtime not null| admissions | 
