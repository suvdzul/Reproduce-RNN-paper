# Conclusion for reproduction

The following is a logbook/ultimate conclusion for a reproduction of a published scientific study. Feel free to add/remove sections as you find them useful.

## Title

Original Study:
* Title: Recurrent Neural Networks for Multivariate Time Series with Missing Values
* DOI:10.1038/s41598-018-24271-9

Reproduction/Replication Study:
* Title: Replicate-RNN-paper: RNN missing value
* DOI:10.5281/zenodo.6678012


## Known differences

Specify changes to the data processing and/or methodology which are known to you. For each difference, describe: (1) the original study approach, (2) the reproduction approach, (3) the justification for the change. If possible, classify the differences as major (could impact the result of the study) or minor (unlikely to change the result of the study).

* The original study used MIMIC-III, I used MIMIC-IV.
* The original study included data from transfers table, I only used ICU stays. 
* The paper stated that they compiled all itemids and sorted them by occurence to filter to only 50 top variables with the highest occurence from inputevents, outputevent, labevents, prescriptions, chartevents and microbiologyevents tables, however, in the Supplementry Info there are 15 variables gathered from outputevents, 33 from inputevents, 41 from labevents and 10 from prescriptions. Thus I only used those selected 99 variables as covariates.
* Since I'm using a different version of MIMIC, the itemids have changed as compared to the original study (https://github.com/USC-Melady/Benchmarking_DL_MIMICIII/blob/master/preprocessing/config/99plusf.csv), so I gathered only the relevant itemids from their list, which is listed in the Variable section of my PLAN https://github.com/suvdzul/Reproduce-RNN-paper/blob/main/PLAN.md. 
* The research question was mortality during 48hrs after admission, however, they've categorized mortality as 48h, 74h, 30d, and 1 yr. I categorized mine as binary variable indicating mortality after 48hrs for patients who were alive during the first 48hrs.
* They used deathtime and dischtime to from admissions table to find the mortality label, I used deathtime only.
* For labels with duplicate itemids, or duplicate measurements, they've took the units/occurrences of each itemids and discarded the minorities if the major one constituted >90% of the total, otherwise converted to the majority itemid's UoM. I haven't discarded any, instead I converted all of them to the target UoM, which is of the majority, and replaced the labels as the majority's label (so a few covariate names might not be consistent with the original study - Supplementary Info).


## Unknown differences

* There were some variables with missing itemids in(https://github.com/USC-Melady/Benchmarking_DL_MIMICIII/blob/master/preprocessing/config/99plusf.csv) , in those cases, I gathered the missing itemids manually, which are highlighted in bold in the Variable section of my PLAN https://github.com/suvdzul/Reproduce-RNN-paper/blob/main/PLAN.md. 
* Not sure how they calculated ICU stay admission years, but I calculated as the median of anchor_year_group + year of intime - anchor_year. 
* Not sure if they excluded non-first admissions, but I only included first admission in the ICU. 
* The aggregation for ICD-9 codes were unclear, for instance, if a patient was billed three times for ICD codes belonging to the same diagnosis group, how was that aggregated by SUM/COUNT or MAX? I chose MAX as it made more sense for multi-task classification problem.
* Itemid = 223262 - Label Insulin-Humalog 75/25 was listed as Insulin-Regular in the original study, so I kept it that way.
* Perhaps it was because of the way I calculated admityear, but there was no admission record for 2008 included in the final cohort.
* It wasn't clear in the orignial study, how each covariate was aggregated, it makes sense for me to average them, so I averaged them for each hour (time-step) of each covariate, for each stay.
* I am not sure when the conversion of UoM occurred in the original study (which was done by finding the majority UoM), I did it after applying the exclusions, so it is specific to our cohort.
* Not sure if zero value/amount/valuenums were excluded from inputevents, outputevents and labevents, but I excluded them.


Specify changes to the data processing and/or methodology which *may* have occurred, but you are unable to confirm due to ambiguity in the original material studied. For each difference, describe (1) the most specific reference to the approach in the original study, if possible, and (2) the approach taken in the reproduction.

## Comparison of population

A table comparing the population measures between the original and the reproduction.

Population measure | Original Study | Reproduction
--- | --- | ---
Admission Records between 2008 and 2012 | 19714 | 21894
Mortality |1716 | 1492
ICD-9 Codes| 19714 | 21892

I'm not sure how, but after I compiled ICD-9 codes for all the admission records, then categorized and aggregated (Max = 1 occurrence), it was reduced from 21894 to 21892. However, the distribution of the ICD-9 codes look the same as the original study.

## Comparison of results

A table of the evaluation measures comparing the results in the original study and the reproduction. Also include the final size of the cohort, proportion of individuals excluded, and other important summary measures for the study.

Evaluation measure | Original Study | Reproduction
--- | --- | ---
TBD | | 

**Cohort**
The original study mentioned that the MIMIC-III dataset contains 58,000 hospital admission records of 38,645 adults and 7,875 neonates. After applying exclusions, they had 19714 admission records. My reproduction originally had 421558 admission records for patients who stayed in the ICU, after applying the following exclusions, the final cohort was 21894 ICU stays:
- include first ICU stays only
- include admissions between 2008 and 2012
- include patients who were alive for the first 48h after admission

Each admission record is associated with 1 or more ICD-9 diagnoses codes, in total there were 5280351 diagnoses codes billed for the 21894 admission records in our cohort. After assigning the ICD-9 codes into their corresponding groups as per the 20 categories listed in Table 2 of Section 3.4 in the Supplementary Info, there were 306094 billings by ICD-9 code group. Then I aggregated the ICD-9 code groups for each admission record, by max occurence (so one billing max per admission for each code group), I had 158974 unique billings for 21892 admission records.

**Covariates**
For inputevents, I originally had 1449459 records for the cohort. The following exclusions were applied:
- exclude records that are 48h after admission - 860522 records
- exclude N/A starttime - none
- exclude N/A amount - none
- exclude N/A amountuom - none
- exclude 0 amount - 3988

After the exclusions were applied I had 587844 records, which was then aggregated by averaging for each variable, for each time step, for each stay. After aggregation, there were a total of 493605 observations for the cohort of 21894 admissions.

For outputevents, I originally had 1290736 records for the cohort. The following exclusions were applied:
- exclude records that are 48h after admission - 786978 records
- exclude N/A charttime - none
- exclude N/A value - none
- exclude N/A valueuom - none
- exclude 0 value - 43706

After the exclusions were applied I had 487369 records, which was then aggregated by averaging for each variable, for each time step, for each stay. After aggregation, there were a total of 471553 observations for the cohort of 21894 admissions.

For labevents, I originally had 6330384 records for the cohort. The following exclusions were applied:
- exclude records that are 48h after admission - 4152560 records
- exclude N/A charttime - none
- exclude N/A value and valuenum- 8374
- exclude N/A valueuom
- exclude 0 value and valuenum

After the exclusions were applied I had 2169450 records, which was then aggregated by averaging for each variable, for each time step, for each stay. After aggregation, there were a total of  observations for the cohort of 21894 admissions.

## Conclusion(s) regarding reproducibility

Highlight specific challenges faced during the reproduction attempt which could be improved upon in the future.
