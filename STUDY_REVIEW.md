# Study Review

Review the chosen study as if you were an academic reviewer.

## Study information

### Title

Recurrent Neural Networks for Multivariate Time Series with Missing Values
DOI:10.1038/s41598-018-24271-9

### Objective(s) of the study

The objective of this study is to develop a deep learning model based on GRU that exploits 2 informative missing patterns, masking and time interval (using a decay term) which would outperform state-of-the-art ML (LR, SVM, RF and GRU) classification methods with imputation as well as RNN (LSTM and GRU) methods that handles missing values without imputation. 

### Dataset(s) used

Synthetic dataset
    Gesture phase segmentation dataset (Gesture)
    * Link: https://archive.ics.uci.edu/ml/datasets/gesture+phase+segmentation
    * Citation: Madeo, R. C., Lima, C. A. & Peres, S. M. Gesture unit segmentation using support vector machines: segmenting gestures from rest positions. In SAC (2013).
    
Real world datasets
    PhysioNet Challenge 2012 dataset (PhysioNet)
    * Link: https://physionet.org/content/challenge-2012/1.0.0/
    * Silva, I., Moody, G., Scott, D. J., Celi, L. A. & Mark, R. G. Predicting in-hospital mortality of icu patients: The physionet/computing in cardiology challenge 2012. In CinC (2012).
    
    MIMIC-III dataset (MIMIC-III)
    * Link: available upon request at https://physionet.org/content/mimiciii/1.4/
    * Citation: Johnson, A. et al. Mimic-iii, a freely accessible critical care database. Sci. Data (2016).
    
Supplementary information with more data generating and preprocessing steps found at S3 of https://www.nature.com/articles/s41598-018-24271-9

## Summarize the paper

The goal of this study was to develop a deep learning model based on Gated Recurrent Unit (GRU) that exploits the missing patterns during model training. The proposed model's predictive performance was compared with RNN baselines (GRU-Mean, GRU-Forward, GRU-Simple and LSTM-Mean) which don't require explicit missing value imputation and other non-RNN baselines which require explicit missing data imputation using several baseline imputation methods. GRU model was compared with by performing missing data imputation using the other baseline imputation approaches as well.
The proposed model outperformed all the baseline prediction models, including non-RNN classification methods (LR, RF, SVM) and RNN classification models (GRU, LSTM), with most competitive ones being the RNN baselines.


### Strength(s) of the work

* The proposed model not only takes advantage of the long-term temporal dependencies, but also utilizes the patterns of missingness for improved prediction results.
* The model captures the observations and their dependencies while training the model using backpropagation, therefore, it would be less computationally expensive than other RNN models which requires missing value imputation first.
* This study utilized 3 datasets, one synthetic and two real-world, and the proposed model showed promising results on all 3 datasets, therefore, the application of this model is diversified

### Weakness(es) of the work

* The proposed method is not explicitly designed for filling in the missing values in the data, and can not be directly used in unsupervised settings without prediction labels.
* If the missingness is not informative at all, or the inherent correlation between the missing patterns and the prediction tasks are not clear, the model may gain limited improvements or even fail.
* The decay mechanisms need to be explicitly designed where informative missingness presents.

### Anticipated reproducibility challenges

Gesture dataset extraction and generating process are pretty detailed in the supplementary sectoin, so I don't see much challenge there.

Not sure how PhysioNet dataset size reduced from 8000 to 4000, and does the 554 mortality correspond to out of 4000 ICU admissions or 8000. The number of patients with in-hospital mortality, length-of-stay less than 3 days, whether the patient had a cardiac condition, and whether the patient was recovering from surgery are not stated in the paper or supplementary information. Not sure how they merged NIDiasABP, NIMAP, NISysABP to DiasABP, MAP, SysABP. 

As for MIMIC-III Dataset, they said that they chose the top 50 items (i.e. features/variables), but the chosen variables for the 4 tables are all less than 50. What was the unit of measurement for each variable? They also aggregated the multiple readings of a feature at a single time stamp based on the feature type, but how did they aggregate for each variable? For each of the admission records, they queried the database tables to get the ICD-9 diagnosis codes. One admission record can be associated with multiple ICD-9 codes, did they count the occurence of each code (or code group), or capped at maximum of 1 occurence during a stay. Also, it is unclear how they calculated admission years.

## Data extraction

Gesture
Only using 18 raw features with timestamps (other 32 didn't have timestamps), first generated non-overlapping time series with same label from the original data, which has 9900 time stamps in total. From the beginning of each new phase, truncated the time series by 30 time steps until the end of the phase segment. They ignored the last extracted time series if it was shorter than 7 time steps. After this step, they got 378 time series with different lengths. The numbers of time series of the 5 labels are 65, 115, 76, 49, 73, respectively. They then generated several synthetic datasets by manually introducing missing values with a goal of average missing rates around 50% for each dataset. First, for each feature d∈{1, · · · , 18},they randomly sampled a number s(d)∈{−1, 1} with equal probabilities to indicate whether the missing rate of that feature has positive or negative correlations with the labels. Then for each sample i, the randomly chose a missing rate r(i,d) from a uniform distribution U[0.3 + C · s(d) · y(i) , 0.7 + C · s(d) ·y(i)], where y(i) = {1, · · · , 5} is for sample i, and C is a constant parameter for that synthetic dataset. They selected a proper value of C to control the average absolute values of the Pearson correlation between missing rate for each feature r(d) and the label y. They then randomly introduced missing values based on the corresponding missing rate. The above steps were repeated to generate 4 synthetic datasets, with the average absolute correlation value to be 0, 0.2, 0.5, 0.8.

PhysioNet
The original dataset consist of records with 42 variables. 6 of them are general descriptors collected on admission, and they only took the other 36 variables which are time series. Among the 36 variables, they further merged NIDiasABP, NIMAP, NISysABP to DiasABP, MAP, SysABP, respectively, and got 33 final variables. 11 of them are vital signs and 22 of them are lab measurements. 

MIMIC-III
The admission table contains over 58,000 hospital admission records of 38,645 adults and 7,875 neonates.They chose four tables namely inputevents-mv (fluids into patient), outputevents (fluids out of the patient), labevents (lab test results) and prescription events (drugs prescribed by doctors) to collect the patient data recorded in critical care units and hospital record systems. They used 19,714 admission records collected during 2008-2012. From each of the four tables, they chose the top 50 items (i.e. features/variables) since these items are present in many of the patients’ records. To avoid/reduce ambiguity and noisy observations, they ensured that all the measurements for a particular variable has only one unit of measurement. They also aggregated the multiple readings of a feature at a single time stamp based on the feature type. For instance, some inputevents features should be averaged while others need to be summed up. This resulted in 99 variables being extracted from the four tables for 19,714 patient admission records. 
For each of the admission records, they collected both the variable value x(t) and the time-stamp of observation s(t). In addition, for each admission record they queried the database tables to get the ICD-9 diagnosis codes. One admission record can be associated with multiple ICD-9 codes. They also queried the discharge time and death time from the Admissions table of MIMIC-III to find the mortality label for each admission record. The ICD-9 diagnosis code were grouped into 20 categories according to the information from the Thomson
Reuters webpage.

### Variables

Gesture
*  Used all 18 numerical attribtues with timestamps - names of those attributes are not included in the paper or the supplementary info

PhysioNet
* ALP, ALT, AST, Albumin, BUN, Bilirubin, Cholesterol, Creatinine, (NI)DiasABP, FiO2, GCS, Glucose, HCO3, HCT, HR, K, Lactate, (NI)MAP, Mg, Na, PaCO2, PaO2, Platelets, Resp Rate, SaO2, (NI)SysABP, Temp, Troponin-I, Troponin-T, Urine, WBC, Weight, pH

MIMIC-III
* Output (15 variables) - 1. Gastric Gastric Tube, 2. Stool Out Stool, 3. Urine Out Incontinent, 4. Ultrafiltrate, 5. Foley, 6. Void, 7. Condom Cath, 8. Fecal Bag, 9. Ostomy (Output), 10. Chest Tube #1, 11. Chest Tube #2, 12. Jackson Pratt #1, 13. OR EBL, 14. Pre-Admission, 15. TF Residual
* Input (33 variables) - 16. Albumin 5%, 17. Dextrose 5%, 18. Fresh Frozen Plasma, 19. Lorazepam (Ativan), 20. Calcium Gluconate, 21. Midazolam (Versed), 22. Phenylephrine, 23. Furosemide (Lasix), 24. Hydralazine, 25. Norepinephrine, 26. Magnesium Sulfate, 27. Nitroglycerin, 28. Insulin - Regular, 29. Insulin - Glargine, 30. Insulin - Humalog, 31. Heparin Sodium, 32. Morphine Sulfate, 33. Potassium Chloride, 34. Packed Red Blood Cells, 35. Gastric Meds, 36. D5 1/2NS, 37. LR, 38. K Phos, 39. Solution, 40. Sterile Water, 41. Metoprolol, 42. Piggyback, 43. OR Crystalloid Intake, 44. OR Cell Saver Intake, 45. PO Intake, 46. GT Flush, 47. KCL (Bolus), 48. Magnesium Sulfate (Bolus)
* Lab Test (41 variables) - 49. Hematocrit, 50. White Blood Cells, 51. Platelet Count, 52. Hemoglobin, 53. MCHC, 54. MCH, 55. MCV, 56. Red Blood Cells, 57. RDW, 58. Potassium, 59. Sodium, 60. Chloride, 61. Bicarbonate, 62. Anion Gap, 63. Urea Nitrogen, 64. Creatinine, 65. Glucose, 66. Magnesium, 67. Total Calcium, 68. Phosphate, 69. INR(PT), 70. PT, 71. PTT, 72. Lymphocytes, 73. Monocytes, 74. Neutrophils, 75. Basophils, 76. Eosinophils, 77. Total Bilirubin, 78. PH, 79. Base Excess, 80. Calculated Total CO2, 81. PO2, 82. PCO2, 83. PH, 84. Specific Gravity, 85. Lactate, 86. Alanine Aminotransferase (ALT), 87. Asparate Aminotransferase (AST), 88. Alkaline Phosphatase, 89. Albumin
* Prescription (10 variables) - 90. Aspirin, 91. Bisacodyl, 92. Docusate Sodium, 93. D5W, 94. Humulin-R Insulin, 95. Potassium Chloride, 96. Magnesium Sulfate, 97. Metoprolol Tartrate, 98. Sodium Chloride 0.9% Flush, 99. Pantoprazole

### Outcome(s)

Gesture 
* A class variable, phase, with 5 possible phases (rest, preparation, hold, stroke, and
retraction).

PhysioNet
* Mortality task: Predict whether the patient dies in the hospital, which is a binary classification problem.
* All 4 tasks:  in-hospital mortality, length-of-stay less than 3 days, whether the patient had a cardiac condition, and whether the patient was recovering from surgery. This is a multi-task classification problem.

MIMIC-III
* Mortality task: Predict whether the patient dies in the hospital after 48 hours, which is a binary classification problem.
* ICD-9 Code tasks: Predict 20 ICD-9 diagnosis categories (e.g., respiratory system diagnosis) for each admission as a multi-task classification problem. The 20 diagnoses can be found in Table 2, Section 3.4 of Supplementary information.

### Inclusion/Exclusion criteria

For PhysioNet, ICU admissions are included as default in the study. 

For Mimic III, hospital admissions at Beth Israel Deaconess Medical Center from 2008 to 2012 and patients who are alive in the first 48 hours after admission are included. In MIMIC III, there are no standard cohorts (i.e. no standard patient and variable inclusion criteria)

### Outlier handling

N/A

### Missing data handling

For baseline classificaton models, they used several baseline imputation methods. Imputation methods include Mean, Forward, Simple, SoftImpute, KNN, CubicSpline, MICE, MF, PCA, and MissForest.

For the proposed model, they exploited the missing patterns using decay terms. 

## Results

Table 1 and 2 provides info about AUC scores (mean ± std) of all models including the proposed as well as baseline models for all prediction tasks of PhysioNet and MIMIC III datasets. Fig 4 contains classification performance for Gesture dataset.

The proposed GRU-D performance: 

MIMIC III
* Mortality 0.8527 ±0.003
* ICD-9 20 tasks average 0.7123 ±0.003

PhysioNet
* Mortality 0.8424 ±0.012
* All 4 tasks average 0.8370±0.012


The proposed model outperformed the models with similar data settings by 1.27% AUROC score. 
The most competitive and relevant prediction baselines are the RNN methods. They also used logistic regression, SVM, and random forest as baseline prediction models and showed relative improvement of 2.2% AUROC score on MIMIC-III dataset from the proposed models over the best
of these baselines.

### Population summary

Gesture
* n=378

PhysioNet
* N=8000 ICU records
* n=4000, which has 554 mortalities.

MIMIC III 
* N=58000 admission records
* n= 19714, which has 1716 mortalities. To evaluate the model performance with different training dataset sizes, they subsampled three smaller datasets of 2,000 and 10,000 admissions while keeping the same mortality rate.

### Analysis method

Baseline Imputation Methods
* Mean - replace each missing observation with the mean of the variable across the training examples.
* Forward - any missing value is the same as its last measurement 
* Simple - indicates which variables are missing and how long they have been missing as a part of input by concatenating the measurement with masking and time interval.
• SoftImpute - uses matrix completion via iterative soft-thresholded Singular Value Decomposition (SVD) to impute missing values.
• KNN - uses k-nearest neighbor to find similar samples and imputed unobserved data by weighted average of similar observations.
• CubicSpline - uses cubic spline to interpolate each feature at different time steps.
• MICE - uses chain equations to create multiple imputations for variables of different types.
• MF - uses matrix factorization (MF) to fill the missing items in the incomplete matrix by factorizing the matrix into two low-rank matrices.
• PCA - imputes the missing values with the principal component analysis (PCA) model.
• MissForest - this is a non-parametric imputation

Baseline Prediction methods
* Non-RNN Baselines: logistic regression (LR), support vector machines (SVM) and random forest (RF). They used all baseline imputation methods to fill in the missing values before using these prediction methods. The non-RNN models cannot directly handle time series of different lengths. They regularly sampled the time-series data to get a fixed length input and perform all baseline imputation methods to fill in the missing values. For concatenation method (Simple) of the non-RNN methods, they concatenated the masking vector along with the measurements of the regularly sampled time series. On PhysioNet dataset they sampled the time series on an hourly basis and propagated measurements forward (or backward) in time to fill gaps, and on MIMIC-III dataset they considered two hourly samples (in the first 48 hours) and did forward (or backward) imputation. Preliminary experiments showed 2-hourly samples obtains better performance than one-hourly samples for MIMIC-III. They chose Gaussian radial basis function (RBF) kernel for SVM since it performs better than other kernels. They used the scikit-learn for the non-RNN model implementation and tuned the parameters by cross-validation.
* RNN Baselines: 4 approaches that handle missing values without imputation, which are GRU-Mean, GRU-Forward, GRU-Simple, and LSTM-Mean are the RNN baselines. They also tested GRU prediction models on the imputed data as well. For RNN models, they used a one layer RNN to model the sequence unless otherwise stated, and then apply a soft-max regressor on top of the last hidden state h(T) to do classification. They used 100 and 64 hidden units in GRU-Mean for MIMIC-III and PhysioNet datasets, respectively. In order to fairly compare the capacity of all GRU-RNN models, they built each model in proper size so they share similar number of parameters. In addition, having comparable number of parameters also makes all the prediction models have number of iterations and training time close in the same scale in all the experiments. Batch normalization and dropout of rate 0.5 are applied to the top regressor layer. They trained all the RNN models with the Adam optimization method and used early stopping to find the best weights on the validation dataset. All RNN models are implemented with Keras and Theano libraries in Python.

Proposed method
* GRU-D model with trainable decays - A decay mechanism is designed for the input variables and the hidden states. They introduced decay rates in the model to control the decay mechanism by considering the following important factors. First, each input variable in health care time series has its own meaning and importance in medical applications. The decay rates should differ from variable to variable based on the underlying properties associated with the variables. Second, since lots of missing patterns are informative and potentially useful in prediction tasks but unknown and possibly complex, they aimed at learning decay rates from the training data rather than fixed a priori and train jointly with all the other parameters of the GRU. They chose the exponentiated negative rectifier in order to keep each decay rate monotonically decreasing in a reasonable range between 0 and 1. 
The proposed GRU-D model incorporates two different trainable decay mechanisms to utilize the missingness directly with the input feature values and implicitly in the RNN states. First, for a missing variable, they used an input decay to decay it over time toward the empirical mean and when decaying the input variable directly, they constrained Wγ to be diagonal, which effectively makes the decay rate of each variable independent from the others. In order to capture richer knowledge from missingness, they also have a hidden state decay γh in GRU-D.
In the final prediction model, they used the proposed GRU-D component at each time step, and applied a fully connected prediction layer, which has sigmoid activation for binary classification task or soft-max activation for multi-class classification tasks, on top of the last GRU component. The network architecture is shown in Fig. 3(c).

All the input variables are normalized to be of 0 mean and 1 standard deviation. They reported the results from 5-fold cross validation in terms of area under the ROC curve (AUC score).

### Power calculations (if present)

N/A.

### Evaluation measures

They reported the results from 5-fold cross validation in terms of area under the ROC curve (AUC score).
AUC for binary classification, Average AUC for multi-task prediction

### Sensitivity Analyses

To validate GRU-D model and demonstrate how it utilizes informative missing patterns, they took the PhysioNet mortality prediction as a study case. Fig. 5(a) which plots the learned input decay rate, the decay rate is almost constant for the majority of variables. However, a few variables have large decay which means that the value of the observation at the current time step is very important for prediction, and the model relies less on the previous observations.
In Fig. 5(b), histograms of the hidden state decay parameters (weights Wγh) corresponding to the input variables that has the highest (the 5 left subfigures) and the lowest (the 5 left subfigures) missing rates among all features. They find that the absolute parameter values are larger for variables with lower missing rate.

