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
* 


## Unknown differences

* There were some variables with missing itemids in(https://github.com/USC-Melady/Benchmarking_DL_MIMICIII/blob/master/preprocessing/config/99plusf.csv) , in those cases, I gathered the missing itemids manually, which are highlighted in bold in the Variable section of my PLAN https://github.com/suvdzul/Reproduce-RNN-paper/blob/main/PLAN.md. 
* Not sure how they calculated ICU stay admission years, but I calculated as the median of anchor_year_group + year of intime - anchor_year. 
* Not sure if they excluded non-first admissions, but I only included first admission in the ICU. 
* The aggregation for ICD-9 codes were unclear, for instance, if a patient was billed three times for ICD codes belonging to the same diagnosis group, how was that aggregated by SUM/COUNT or MAX? I chose MAX as it made more sense for multi-class classification task.
* Itemid = 223262 - Label Insulin-Humalog 75/25 was listed as Insulin-Regular in the original study, so I kept it that way.


Specify changes to the data processing and/or methodology which *may* have occurred, but you are unable to confirm due to ambiguity in the original material studied. For each difference, describe (1) the most specific reference to the approach in the original study, if possible, and (2) the approach taken in the reproduction.

## Comparison of population

A table comparing the population measures between the original and the reproduction.

Population measure | Original Study | Reproduction
--- | --- | ---
TBD | | 

## Comparison of results

A table of the evaluation measures comparing the results in the original study and the reproduction. Also include the final size of the cohort, proportion of individuals excluded, and other important summary measures for the study.

Evaluation measure | Original Study | Reproduction
--- | --- | ---
TBD | | 

## Conclusion(s) regarding reproducibility

Highlight specific challenges faced during the reproduction attempt which could be improved upon in the future.
