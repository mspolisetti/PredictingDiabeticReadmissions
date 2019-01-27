# PredictingDiabeticReadmissions
# Goal/Purpose: 
Build models predicting 30-day Hospital readmissions and identified key factors that affect readmission risk.

# Background : 
Reducing hospital readmission rates has the potential to improve care.
Hospitalized patients with diabetes may be at higher risk of readmission 
 
# Data: 
Our dataset contains more than 100,000 in-patient diabetic encounters during 10 years. 
https://archive.ics.uci.edu/ml/datasets/diabetes+130-us+hospitals+for+years+1999-2008

# Techniques: 
We filled missing values, binned categorical variables to smaller dimensions, 
deleted deceased and inapplicable records and created new factors to combine a large number of similar factors. 

# Methods :

Built Logistic Regression and Random Forest models predicting patients' 30-day readmission 
with true positive rate over 70 %. Patient segmentation insights through performing LCA.
