# Predicting 30-Day Diabetic Patient Readmissions

### Goal: 
we used a medical claims dataset (description below), to answer these questions:

1. What factors are the strongest predictors of hospital readmission in diabetic patients?

2. How well can we predict hospital readmission in this dataset with limited features?

# Background - Why hospital readmissions matter? 
A hospital readmission is when a patient who is discharged from the hospital, gets re-admitted again within a certain period of time. Hospital readmission rates for certain conditions are now considered an indicator of hospital quality, and also affect the cost of care adversely. For this reason, Centers for Medicare & Medicaid Services established the Hospital Readmissions Reduction Program which aims to improve quality of care for patients and reduce healthcare spending by applying payment penalties to hospitals that have more than expected readmission rates for certain conditions. Although diabetes is not yet included in the penalty measures, the program is regularly adding new disease conditions to the list, now totalling 6 for FY2018. In 2011, American hospitals spent over $41 billion on diabetic patients who got readmitted within 30 days of discharge. Being able to determine factors that lead to higher readmission in such patients, and correspondingly being able to predict which patients will get readmitted can help hospitals save millions of dollars while improving quality of care.
 
### Data: 
We picked a publicly available dataset from UCI repository (link) containing de-identified diabetes patient encounter data for 130 US hospitals (1999–2008) containing 101,766 observations over 10 years. The dataset has over 50 features including patient characteristics, conditions, tests and 23 medications. Only diabetic encounters are included (i.e. at least one of three primary diagnosis was diabetes).  
https://archive.ics.uci.edu/ml/datasets/diabetes+130-us+hospitals+for+years+1999-2008

### Techniques: 

##### What preprocessing and feature engineering techniques should be applied?
Before we can get to actual modeling, some wrangling with the data is almost always needed. We applied three types of methods here:

1. Cleaning tasks such as dropping bad data, dealing with missing values.
2. Modification of existing features e.g. standardization, log transforms etc.
3. Creation or derivation of new features, usually from existing ones.

The individual steps are described in detail below. 

###### Dealing with missing values
First we checked to see how many missing values are. This gives us a long list but the following variables had missing values:
  race 2273
  weight 98569
  payer_code 40256
  medical_specialty 49949
  diag_1 21
  diag_2 358
  diag_3 1423
  gender 3

Weight is missing in over 98% records. Owing to the poor interpretability of missing values and little predictive generalizability to other patients, best thing is to just drop it.

Payer code and Medical Specialty of treating physician also have 40–50% missing values. We decided to fill the missing values based on age. Younger ones with Insurance and older patients with Medicaid.

Primary (diag_1), Secondary (diag_2) and Additional (diag_3) diagnoses were have very few missing values. Technically, if all three are missing, that’s bad data. So we only drop those records where all three diagnoses are missing.

Gender has only 3 missing or invalid values so we decided to drop these records.

Also, one more cleaning step that depends on understanding the data and some common sense: since we are trying to predict readmissions, those patients who died during this hospital admission, have zero probability of readmission. So we removed those records.

We binned larger categories into smaller set of categories.


## Creating and/or Recoding New Features
 
Number of medication changes: The dataset contains 23 features for 23 drugs (or combos) which indicate for each of these, whether a change in that medication was made or not during the current hospital stay of patient. Medication change for diabetics upon admission has been shown by previous research to be associated with lower readmission rates. We decided to count how many changes were made in total for each patient, and declared that a new feature. The reasoning here was to both simplify the model and possibly discover a relationship with number of changes regardless of which drug was changed. We encoded the “medication change” feature from “No” (no change) and “Ch” (changed) into 0 and 1.  
 
We also reduced both A1C test result and Glucose serum test result into categories of Normal, Abnormal and Not tested.

 
Recoding the outcome variable: The outcome we are looking at is whether the patient gets readmitted to the hospital within 30 days or not. The variable actually has < 30, > 30 and No Readmission categories. To reduce our problem to a binary classification, we combined the readmission after 30 days and no readmission into a single category.

 
Collapsing of Multiple Encounters for same patient

Some patients in the dataset had more than one encounter. We could not count them as independent encounters because that bias the results towards those patients who had multiple encounters. We decided to use first encounters of patients with multiple encounters. This resulted in dataset being reduced to about 70,000 encounter.
###### Data Balancing
Data was highly imbalanced with respect to readmissions (only 10% records for 30-day readmissions), leading to high accuracy. Moreover, the high accuracy could be attributed not to the generalizability of our model to diverse patient records but to the baseline accuracy of 90%: predicting that no patient would be readmitted. This was evident from the poor precision and recall of our model in predicting patient readmissions. We used synthetic minority over-sampling technique (SMOTE) to oversample our underrepresented class of readmissions and obtain equal representation of our overrepresented and underrepresented classes.

# Methods :

We performed LCA on the data to understand Patient segmentation.

#### Models
Our choice of models is governed primarily by our aim to understand the most important factors, along with their relative effects on medication change and readmission. Thus, while model accuracy is important, model interpretability in order to devise corrective measures is a key criterion for our model selection. The models that we implemented include:

1. Logistic regression: With the starting assumption that the impact of factors and their interactions can be modeled as a log likelihood of outcome, logistic regression can help us understand the relative impact and statistical significance of each factor on the probability of readmission.

2. Random Forests: By considering more than one decision tree and then doing a majority voting, random forests helped in being more robust predictive representations than trees as in the previous case. 

#### Comparing model performance
RandomForests and Logistic regression(s) gave 70% accuracy. Besides accuracy, recall is important here since hospitals get penalized and incur additional costs both for the patient and the insurance agencies if a patient expected not to be readmitted shows up in 30 days.

### Most Important Factors

Our Random forest indicates highest importance of num of lab procedures and number of medications used. Logistic regression model suggests number of inpatient days and discharge disposition are key factors for the prediction.
