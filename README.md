# RML Assignments Overview

### Basic Information

1. Person or organization developing model: Aditya Gupta (aditya.gupta@gwmail.gwu.edu)
 2. Model date: August, 2021-2025
 3. Model version: 2.0
 4. License: MIT
 5. Model implementation code: [DNSC_6301_Example_Project.ipynb](https://github.com/jphall663/GWU_DNSC_6301_project/blob/main/DNSC_6301_Example_Project.ipynb) 

## Intended Use
   * Primary intended uses: Educational project to demonstrate interpretable and fair ML modeling on HMDA data
   * Primary intended users: Students, researchers, and practitioners interested in responsible machine learning.
   * Out-of-scope use cases: Real-world mortgage decisioning without regulatory compliance checks or human oversight.

## Training Data
   ###### Data Dictionary
|       Name       | Modeling Role | Measurement Level |                        Description                         |
|:---------------:|:-------------:|:-----------------:|:---------------------------------------------------------:|
|     term_360     |     input     |        int        | Binary indicator if loan term is 360 months |
|    conforming    |     input     |        int        | Flag for conforming loan status |
| debt_to_income_ratio_missing | input | int | Flag if DTI was missing |
| loan_amount_std  |     input     |       float       | Standardized loan amount       |
| loan_to_value_ratio_std |  input  |      float       | Standardized LTV ratio      |
| no_intro_rate_period_std | input | float | Standardized flag for no intro rate     |
| intro_rate_period_std | input | float | Standardized intro rate period    |
| property_value_std |  input      |      float        | Standardized property value |
|   income_std     |     input     |       float       | Standardized borrower income   |
| debt_to_income_ratio_std | input | float | Standardized DTI ratio |
|   high_priced    |    target     |       float       | Label: 1 = high-priced, 0 = not |

   * Source of training data: Processed HMDA dataset (2023)
   * How training data was divided into training and validation data: 50% training, 25% validation, 25% test
   * Number of rows in training and validation data:
     Training rows: 112253
     Validation rows: 48085

## Test Data
   * Source of test data: Processed HMDA dataset (2023)
   * Number of rows in test data: 19831
   * State any differences in columns between training and test data: None

## Model details
   * Columns used as inputs in the final model: 'TERM_360', 'CONFORMING', 'DEBT_TO_INCOME_RATIO_MISSING', 'LOAN_AMOUNT_STD', 'LOAN_TO_VALUE_RATIO_STD', 'NO_INTRO_RATE_PERIOD_STD', 'INTRO_RATE_PERIOD_STD', 'PROPERTY_VALUE_STD', 'INCOME_STD', 'DEBT_TO_INCOME_RATIO_STD'
   * Column(s) used as target(s) in the final model: 'HIGH_PRICED'
   * Type of model: Explainable Boosting Machine (EBM)
   * Software used to implement the model: Python, scikit-learn
   * Version of the modeling software: 0.22.2.post1
   * Hyperparameters or other settings of your model : ['loan_amount_std', 'no_intro_rate_period_std', 'term_360', 'income_std', 'debt_to_income_ratio_missing', 'intro_rate_period_std', 'property_value_std']

## Quantitative Analysis
Models were assessed primarily with AUC and AIR. See details below:

| Metric    | Train AUC | Validation AUC |
|:--------:|:---------:|:--------------:|
| AUC Score | 0.7802    | 0.7739         |
    
###### Table 1. AUC values across data partitions.
    
| Group               | Validation AIR |
|:------------------:|:--------------:|
| Black vs. White     | 0.787          |
| Asian vs. White     | 1.157          |
| Female vs. Male     | 0.958          |
    
###### Table 2. Validation AIR values for race and sex groups.
 

#### Figures 

* Assignment 1


Figure 1. Histograms data exploration.



Figure 2. Heatmaps correlations.

* Assignment 2



Figure 3. Global feature importance.



Figure 4. Local feature importance



Figure 5. Partial dependence feature

* Assignment 3



Figure 6. AIR V/S AUC EBM

* Assignment 4



Figure 7. Simulated data




Figure 8. Stolen model



Figure 9. Distributed random forest

* Assignment 5



Figure 10. Simulate recession conditions in validation data



Figure 11. Global Logloss Residuals 

## Ethical Considerations

### Potential Negative Impacts:
* The remediated EBM model may still encode indirect bias despite AIR parity improvements.
* Overreliance on fairness metrics like AIR can mask other disparities (e.g., precision, recall across groups).
* Software issues (e.g., float precision errors or binning instability) could lead to inconsistent scoring.
* Real-world risk: borrowers may be unfairly classified as high-risk during economic downturns, affecting credit access.

### Potential Uncertainties:

* Shifts in borrower behavior or policy changes (e.g., interest rate ceilings) could affect generalizability.
* Unknown interaction effects between features might behave unexpectedly under real-time deployment conditions.
* Model drift or adversarial manipulation could arise if integrated into production systems without monitoring.

### Unexpected Results:

* Remediated EBM showed slightly lower AUC but improved AIR, highlighting a tradeoff between accuracy and fairness.
* Feature no_intro_rate_period_std had unexpectedly high influence in certain EBM iterations.
