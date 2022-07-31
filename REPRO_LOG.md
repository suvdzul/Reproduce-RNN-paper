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

* The original study used MIMIC-III, I used MIMIC-IV.
* The original study included data from transfers table, I only used ICU stays. 
* The paper stated that they compiled all itemids and sorted them by occurence to filter to only 50 top variables with the highest occurence from inputevents, outputevent, labevents, prescriptions, chartevents and microbiologyevents tables, however, in the Supplementry Info there are 15 variables gathered from outputevents, 33 from inputevents, 41 from labevents and 10 from prescriptions. Thus I only used those selected 99 variables as covariates.
* Since I'm using a different version of MIMIC, the itemids have changed as compared to the original study (https://github.com/USC-Melady/Benchmarking_DL_MIMICIII/blob/master/preprocessing/config/99plusf.csv), so I gathered only the relevant itemids from their list, which is listed in the Variable section of my PLAN https://github.com/suvdzul/Reproduce-RNN-paper/blob/main/PLAN.md. 
* The research question was mortality during 48hrs after admission, however, they've categorized mortality as 48h, 74h, 30d, and 1 yr. I categorized mine as binary variable indicating mortality after 48hrs for patients who were alive during the first 48hrs.
* They used deathtime and dischtime to from admissions table to find the mortality label, I used deathtime only.
* For labels with duplicate itemids, or duplicate measurements, they've took the units/occurrences of each itemids and discarded the minorities if the major one constituted >90% of the total, otherwise converted to the majority itemid's UoM. I haven't discarded any, instead I converted all of them to the target UoM, which is of the majority, and replaced the labels as the majority's label (so a few covariate names might not be consistent with the original study - Supplementary Info).
* Prescriptions table didn't have the itemids that I compiled from the original study, so the names of the drugs were used instead.



## Unknown differences

* There were some variables with missing itemids in(https://github.com/USC-Melady/Benchmarking_DL_MIMICIII/blob/master/preprocessing/config/99plusf.csv) , in those cases, I gathered the missing itemids manually, which are highlighted in bold in the Variable section of my PLAN https://github.com/suvdzul/Reproduce-RNN-paper/blob/main/PLAN.md. 
* Not sure how they calculated ICU stay admission years, but I calculated as the median of anchor_year_group + year of intime - anchor_year. 
* Not sure if they excluded non-first admissions, but I only included first admission in the ICU. 
* The aggregation for ICD-9 codes were unclear, for instance, if a patient was billed three times for ICD codes belonging to the same diagnosis group, how was that aggregated by SUM/COUNT or MAX? I chose MAX as it made more sense for multi-task classification problem.
* Itemid = 223262 - Label Insulin-Humalog 75/25 was listed as Insulin-Regular in the original study, so I kept it that way.
* Perhaps it was because of the way I calculated admityear, but there was no admission record for 2008 included in the final cohort.
* It wasn't clear in the orignial study, how each covariate was aggregated (they just mentioned that some were summed and some were averaged for inputevents), it made sense for me to average them for inputevents and outputevents, so I averaged them for each hour (time-step) of each covariate, for each stay, as for labevents, I took minimum and maximum to aggregate.
* I am not sure when the conversion of UoM occurred in the original study (which was done by finding the majority UoM), I did it after applying the exclusions, so it is specific to our cohort.
* Not sure if zero value/amount/valuenums were excluded from inputevents, outputevents and labevents, but I excluded them.
* Prescriptions table's dosage aggregation was unclear, so I took the upper bound of the dosage if dosage was given as a range and then took the sum to aggregate.

## Comparison of population

A table comparing the population measures between the original and the reproduction.

Population measure | Original Study | Reproduction
--- | --- | ---
Admission Records between 2008 and 2012 | 19714 | 21894
Mortality |1716 | 1492
ICD-9 Codes| 19714 | 21892

I'm not sure how, but after I compiled ICD-9 codes for all the admission records, then categorized and aggregated (Max = 1 occurrence), it was reduced from 21894 to 21892. However, the distribution of the ICD-9 codes look the same as the original study.

## Comparison of results

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

After the exclusions were applied I had 587844 records, which was then aggregated by averaging for each variable, for each time step, for each stay. After aggregation, there were a total of 493605 observations for the cohort of 21894 admissions. After creating 33 time series features for 48 hours (each time step is hourly), 18.5% was missing. 

For outputevents, I originally had 1290736 records for the cohort. The following exclusions were applied:
- exclude records that are 48h after admission - 786978 records
- exclude N/A charttime - none
- exclude N/A value - none
- exclude N/A valueuom - none
- exclude 0 value - 43706

After the exclusions were applied I had 487369 records, which was then aggregated by averaging for each variable, for each time step, for each stay. After aggregation, there were a total of 471553 observations for the cohort of 21894 admissions. After creating 15 time series features for 48 hours (each time step is hourly), 10.7% was missing.

For labevents, I originally had 6330384 records for the cohort. The following exclusions were applied:
- exclude records that are 48h after admission - 4152560 records
- exclude N/A charttime - none
- exclude N/A value and valuenum- 8374
- exclude N/A valueuom - 136832
- exclude 0 value and valuenum - 4752 + 28

After the exclusions were applied I had 2098462 records, which was then aggregated by taking min and max of valuenum for each variable, for each time step, for each stay. After aggregation, there were a total of 2072415 observations for the cohort of 21894 admissions. After creating 41 time series features for 48 hours (each time step is hourly), 64.1% was missing.


For prescriptions, I originally had 274422 records for the cohort. The following exclusions were applied:
- exclude records that are 48h after admission - 135524 records
- exclude N/A starttime - 116
- exclude N/A dose - 84
- exclude N/A unit - 84
- exclude 0 dose - 10

After the exclusions were applied I had 138711 records, which was then aggregated by taking the maximum for each variable (for dosage given in ranges the upper bound was taken), for each time step, for each stay. After aggregation, there were a total of 99912 observations for the cohort of 21894 admissions. After creating 10 time series features for 48 hours (each time step is hourly), 14.4% was missing.

## Conclusion(s) regarding reproducibility

- The original study was very vague about the steps they've taken to extract the data, I had to assume a lot of things. For instance, I was not sure how each variable was aggregated, so inputevents and outputevents were averaged while labevents I took min and max, and prescriptions I took max.
- Some of the covariates with multiple itemids that I gathered from the original study needs to be double checked.
- Prescription dosage per hour can be calculated precisely using the starttime and endtime, which was not performed in the original study since it was an update in MIMIC-IV, however, this would give more accurate results. 
- Another inconsistency was prescriptions table no longer had codes/itemids, thus I used the names of the drugs to compile data.
- I took labevents time as charttime?.
- Perhaps include data from transfer from the ICU, as well as out of hospital mortality for better prediction. 
- Some of the events' charttime or starttime was before the ICU intime, which was removed, perhaps this was an error or it occurred in hospital before ICU stay.
- Somehow during my calculation some events' hours were rounded up to 49 hour after admission, which was removed.
- Outliers need to be explored further.
- If I am to run the models, I would transfrom the data into a wide format.
