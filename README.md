# RML Assignments Overview

### Basic Information

1. Person or organization developing model: Patrick Hall (jphall@gwu.edu), Aditya Gupta (aditya.gupta@gwmail.gwu.edu)
 2. Model date: August, 2021-2025
 3. Model version: 1.0
 4. License:  Apache License, Version 2.0
 5. Model implementation code: [Group_9_Model_Used.ipynb](https://github.com/adityagupta050899/RML/blob/9eb41877f070398e2e717baa029ac9b378968cf7/Templates/assign_5_template.ipynb) 

## Intended Use

### Business Value
My best remediated model helps mortgage lenders flag loans likely to be classified as *high-priced* (APR ≥ 1.5%) while minimizing demographic bias across race, gender, and age categories. Built on real HMDA data, the model provides actionable insights for identifying inequities in lending decisions and serves as a replicable, auditable framework for responsible AI and compliance reporting.

### How It Is Designed to Be Used
The model is intended to be used **offline** in batch settings for:
- Predicting high-priced mortgage risk from applicant features (Assignment 1)
- Auditing fairness through AIR comparisons and remedial actions (Assignment 3)
- Exploring global and local feature contributions (Assignment 2)
- Testing resilience through adversarial examples and red teaming (Assignment 4)
- Evaluating robustness through recession scenarios and error profiling (Assignment 5)

### Intended Users
- Mortgage risk analysts and compliance officers
- Fair lending auditors and regulatory reporting teams
- Students and researchers focused on interpretable machine learning

### Additional Usage Limitations
This model is **not intended** for use in production systems or high-stakes decisions such as:
- Final loan approvals
- Insurance assessments
- Employment screening
- Criminal justice predictions

Deployment in such contexts would require extensive retraining, domain validation, and regulatory oversight to ensure fitness for purpose.

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
   * How training data was divided into training and validation data: 70% training, 30% validation
   <pre>
   np.random.seed(SEED)
   split_ratio = 0.7 
   split = np.random.rand(len(data)) < split_ratio
   train = data[split]
   valid = data[~split]
   </pre>
   
   * **Number of rows in training and validation data:**  
     * Training rows  = **112,253**  
     * Validation rows = **48,085**


## Test Data
   * Source of test data: Processed HMDA dataset (2023)
   * Number of rows in test data: 19,831
   * State any differences in columns between training and test data: The test file has the identical column schema as the training set (all feature columns present); the only distinction is that the target label high_priced is withheld.

## Model details
   * Columns used as inputs in the final model: 'debt_to_income_ratio_missing', 'debt_to_income_ratio_std', 'property_value_std', 'term_360', 'no_intro_rate_period_std'
   * Column(s) used as target(s) in the final model: 'HIGH_PRICED'
   * Type of model: Explainable Boosting Machine (EBM)
   * Software used to implement the model: Python, scikit-learn
   * Version of the modeling software: 0.22.2.post1
   * Hyper‑parameters / settings  
<pre>
  rem_params = {
      'max_bins': 1024,
      'interactions': 10,
      'outer_bags': 16,
      'learning_rate': 0.05,
      'n_jobs': 4,
      'early_stopping_rounds': 100,
      'random_state': 12345
  }
</pre>

## Assignment‑wise Results Summary

#### Assignment 1: Model Training on Explainable Models
   * Objective: Train and compare GLM, Monotonic XGBoost, and EBM models on HMDA data
   * Key Models: GLM, XGBoost (monotonic), EBM
   * Train AUCs: GLM - 0.7652 | XGBoost - 0.80008 | EBM - 0.8314
   * Validation AUCs: GLM - 0.7538 | XGBoost - 0.7916 | EBM - 0.8250
   * Observations: EBM achieved the best performance among explainable models. GLM served as baseline.

#### Assignment 2: Model Explanation and Feature Importance
   * Objective: Use global/local importance and partial dependence to compare model behaviors
   * Approach: Extract SHAP values, regression coefficients, and EBM scores
   * Visuals: Global bar plots, PDPs for key variables, local explanations at 10th/50th/90th percentiles
   * Observations: Models showed consistent directionality on top features, but differed in strength and interaction detection

#### Assignment 3: Fairness Testing and Remediation (AIR)
   * Objective: Test models for discrimination using AIR; improve without reducing AIR below 0.8
   * Adverse impact ratio for: Black vs White - 0.802 | Asian vs White - 1.158 | Female vs Male - 0.957
   * Validation AUC: 0.7767 (no performance drop from original EBM)
   * Observations: Grid search helped improve fairness while retaining model performance

#### Assignment 4: Red Teaming and Adversarial Testing
   * Objective: Simulate adversarial attacks on model via model extraction and counterexamples
   * Methods: Decision tree model extraction, adversarial input generation, attack simulation
   * Outcomes: Exposed model vulnerabilities to feature flipping and response skew
   * Observations: EBM showed resilience to mild attacks but lacked protection against well-crafted inputs

#### Assignment 5: Debugging and Robustness (Final Remediated EBM)
   * Objective: Stress test EBM under recession and improve residual error patterns
   * Stress Test Result: AUC degraded under recession conditions
   * Residual Fixes: Outlier removal and reweighting improved stability
   * Final AUCs: Train - 0.7984 | Validation - 0.8005 | True Test - 0.7801
   * Final AIRs: Black vs White - 0.981 | Asian vs White - 1.110 | Female vs Male - 1.008
   * Observations: Final model achieved optimal tradeoff between fairness and performance


## Quantitative Analysis
Models were assessed primarily with AUC and AIR. See details below:

| Metric    | Test AUC | Validation AUC |
|:--------:|:---------:|:--------------:|
| AUC Score | 0.7802    | 0.7739         |
    
###### Table 1. AUC values across data partitions.
    
| Group               | Validation AIR |
|:------------------:|:--------------:|
| Black vs. White     | 0.787          |
| Asian vs. White     | 1.157          |
| Female vs. Male     | 0.958          |
    
###### Table 2. Validation AIR values for race and sex groups.
 

## Figures 

* Assignment 1

![image](https://github.com/user-attachments/assets/3bde93c3-9e85-4ba3-9d58-afdf3ff6768a)

Figure 1. Histograms data exploration.

![image](https://github.com/user-attachments/assets/5509c352-3f74-4185-851f-3f241cd33f8b)

Figure 2. Heatmaps correlations.

* Assignment 2

![image](https://github.com/user-attachments/assets/52804666-89ee-4ab0-9172-75f08d00f5f3)

Figure 3. Global feature importance.

![image](https://github.com/user-attachments/assets/491d1399-613e-4da3-a9f1-a5a56ec8a6c5)

Figure 4. Local feature importance

![image](https://github.com/user-attachments/assets/bb860789-ef80-4205-806b-b0761212c2d6)

Figure 5. Partial dependence feature

* Assignment 3

![image](https://github.com/user-attachments/assets/1d3c8db5-3369-4cb5-bd2e-1467fd8e9ce6)

Figure 6. AIR V/S AUC EBM

* Assignment 4

![image](https://github.com/user-attachments/assets/6d3b72fc-93af-4f99-993e-0434f092f6fd)

Figure 7. Simulated data

![image](https://github.com/user-attachments/assets/ca7a066e-48ea-41bf-8e6b-397fc4b3153d)

Figure 8. Stolen model

![image](https://github.com/user-attachments/assets/561077de-83e4-4331-921e-79abe7c0fe37)

Figure 9. Distributed random forest

* Assignment 5

![image](https://github.com/user-attachments/assets/95cfa5cc-cbb2-4cd2-b068-659d98dfe640)

Figure 10. Simulate recession conditions in validation data

![image](https://github.com/user-attachments/assets/71eb3c14-bbb0-40aa-ad43-e397ac389fc8)

Figure 11. Global Logloss Residuals 

## Alternative models considered

* **Elastic Net GLM**: We implemented a penalized logistic regression using H2O’s GLM function, which automatically tuned the alpha parameter across a grid of values via 10-fold cross-validation and lambda search. The model used the same set of standardized features as our final EBM and was trained on H2OFrame objects with seed-controlled reproducibility. This approach yielded a validation AUC of 0.7538, offering strong interpretability but limited capacity to capture nonlinear effects. Its transparent coefficients and standardized pipeline made it a solid baseline.

* **Monotonic XGBoost**: We applied a custom monotonic XGBoost grid search using a manually defined monotonicity constraint vector and extensive hyperparameter tuning over 50 randomized configurations. The implementation employed XGBoost v1.7 and evaluated models on AUC using early stopping. This model reached a validation AUC of 0.7916, outperforming the GLM in terms of discrimination. However, due to its complexity and lower interpretability, it was not chosen as the final model. Its strength lay in its flexibility, but it lacked the auditability of EBM.

### Ethical Considerations

**Potential Negative Impacts:**
- **Math/Software Precision:** The EBM’s histogram binning (max_bins = 256) may cause rounding inconsistencies near the decision threshold (e.g., 0.22 cutoff), affecting classification outcomes.
- **Random Grid Sensitivity:** The fairness grid search in Assignment 3 produced models with noticeable AIR variation across different random seeds.
- **Software Dependency Drift:** Future changes to the `interpret` or `xgboost` packages may alter default behavior, requiring careful version control and regression testing.
- **Security Exposure:** The red-teaming exercise (Assignment 4) demonstrated that a simple surrogate model could mimic the remediated EBM and be exploited via crafted inputs, enabling adversarial steering.
- **Fairness Scope:** While AIR parity was achieved for race (Black/Asian/White) and gender (Female/Male), we did not audit fairness across other dimensions like age or region.
- **Real-World Risks:** In stress testing (Assignment 5), the AUC dropped from 0.72 to ~0.59 under mild recession simulation. This degradation may result in unjustified denials or high-cost loans to sensitive populations.

**Potential Uncertainties:**
- **Distribution Shift:** Changes in lending practices or economic shocks could push features like `income_std` or `property_value_std` beyond training bounds, degrading model reliability.
- **Model Monitoring:** No continuous monitoring or drift detection has been implemented; unexpected feature shifts could go unnoticed.
- **Metric Sufficiency:** AIR compliance does not guarantee equal precision or recall across groups. Disparate impact may persist despite parity on one metric.

**Unexpected Results During Training:**
- Raising the decision threshold from 0.19 to 0.22 improved Black vs. White AIR from 0.768 to 0.824 with negligible AUC loss (<0.3pp).
- Removing extreme log-loss outliers improved validation AUC from 0.7202 to 0.7243 in Assignment 5.
- The feature `no_intro_rate_period_std` emerged with disproportionately high importance in some EBM runs, potentially acting as a proxy variable.
- A mild simulated recession caused a steep AUC decline to ~0.592, indicating fragility to macroeconomic shifts.
