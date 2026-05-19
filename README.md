This repository contains an end-to-end QSAR (Quantitative Structure–Activity Relationship) workflow for predicting the bioactivity of small molecules using machine learning approaches.

The pipeline was developed to:

•	Train QSAR models using compounds with known experimental MIC values 

•	Convert MIC values into pMIC values 

•	Generate molecular descriptors from SMILES 

•	Perform descriptor preprocessing and feature selection 

•	Build and validate QSAR models using LOOCV 

•	Predict pMIC values for external compounds with unknown activity

•	Evaluate model robustness using Y-randomization and applicability domain analysis 

The objective of the pipeline is to identify relationships between molecular descriptors and biological activity, develop a statistically robust predictive model, and estimate the pMIC values of unexplored compounds.
Execute the workflow step-by-step in Google Colab or run the notebook in jupyter notebook.

Dataset Description

Training Set

•	33 compounds 

•	Experimental MIC values available 

•	Used for QSAR model training, development and validation

Test Set

•	11 compounds 

•	Only SMILES structures available 

•	Used for external pMIC prediction 

Workflow and Scientific Interpretation

1. MIC to pMIC Conversion: 
MIC values are usually highly skewed and span multiple orders of magnitude. Direct modeling of MIC values often results in poor statistical performance.

Converting MIC to pMIC:

•	normalizes the activity scale 

•	improves linearity 

•	enhances QSAR model stability 

Formula:

pMIC=−log⁡10(MIC  in  M)

Interpretation: Higher pMIC values indicate stronger biological activity.

2. Descriptor Generation: 
Machine learning models cannot directly interpret SMILES strings. Molecular descriptors convert chemical structures into numerical representations.

Descriptors encode:

•	topology 

•	electronic properties 

•	constitutional properties 

•	molecular complexity 

•	hydrophobicity 

•	atom distribution

Tools Used:

•	RDKit 

•	Mordred descriptors (2D) 

Interpretation: Descriptors represent structural and physicochemical characteristics responsible for biological activity.

3. Descriptor Preprocessing
   
Removal of invalid descriptors

Missing value imputation- Some descriptors cannot be calculated for all molecules, leading to missing values.
   
Median imputation:

•	prevents loss of compounds 

•	stabilizes model training 

•	avoids bias caused by removing molecules

Interpretation: Ensures a complete descriptor matrix suitable for machine learning.

Correlation filtering- Highly correlated descriptors contain redundant information.
   
Using correlated descriptors:

•	increases multicollinearity 

•	destabilizes regression coefficients 

•	reduces interpretability 

•	increases overfitting risk 

Interpretation: Retains only independent structural information important for activity prediction.

Low-variance filtering - Descriptors with extremely low variance contribute little to model learning because they remain nearly constant across molecules.
Interpretation: Keeps only informative descriptors capable of differentiating active and inactive molecules.

4. Feature Selection

QSAR datasets typically contain hundreds of descriptors but only a limited number of compounds.

Recursive Feature Elimination (RFE) : 

•	selects the most relevant descriptors 

•	removes irrelevant variables 

•	improves interpretability 

•	reduces overfitting 

Interpretation: Identifies structural features most strongly associated with biological activity.

5. Descriptor Scaling

Descriptors exist on different numerical scales.

Standardization:

•	prevents bias toward large-valued descriptors 

•	improves regression performance

•	stabilizes optimization 

Interpretation:  Allows all descriptors to contribute equally during model development.

6. QSAR Model Development
   
Models evaluated:

1.	Ridge Regression: Ridge regression performs well for small QSAR datasets with correlated descriptors.

•	reduces coefficient instability 

•	minimizes overfitting 

•	improves generalization

Interpretation: Determines linear relationships between molecular features and biological activity.

2.	Support Vector Regression (SVR): SVR captures nonlinear relationships between descriptors and activity.
   
Interpretation: Useful when biological activity depends on complex structural interactions.

3.	Random Forest Regression: Random Forest captures nonlinear and interaction effects without strict assumptions.

Interpretation: Identifies complex descriptor–activity relationships through ensemble learning.

7. Validation

Leave-One-Out Cross-Validation (LOOCV): The dataset contains only 33 compounds, making LOOCV suitable for maximizing training data usage.

In LOOCV:

•	one compound is removed 

•	model is trained on remaining compounds 

•	prediction is performed for the excluded compound 

•	repeated for all compounds 

Interpretation:  Evaluates predictive robustness and generalization capability.

Model Evaluation Metrices

•	Q² (LOOCV) : Measures predictive ability during cross-validation. Higher Q² values indicate better predictive performance.

•	RMSEcv: Measures prediction error magnitude. Lower RMSEcv indicates better prediction accuracy. 

•	MAEcv: Measures average absolute prediction error. Lower MAE values indicate smaller prediction deviations.

Y-randomization test: A good QSAR model should not arise from random correlations.

In Y-randomization:

•	activity values are randomly shuffled 

•	models are rebuilt repeatedly 

•	predictive performance is recalculated 

Interpretation:  Low or negative randomized Q² values indicate that the developed model is statistically reliable and not due to chance.

8. Applicability Domain
    
Predictions are reliable only for compounds chemically similar to the training set.

Applicability domain analysis identifies:

•	reliable predictions 

•	extrapolated compounds 

•	structural outliers

•	Leverage approach 

Williams plot: Evaluates:

•	leverage values 

•	standardized residuals 

Interpretation:

•	High leverage → structurally influential compounds 

•	Large residuals → poorly predicted compounds 

•	Compounds within threshold → reliable predictions

PCA chemical space analysis: Principal Component Analysis (PCA) visualizes chemical similarity between training and test molecules.If test compounds cluster near training compounds, predictions are considered more reliable.

9. External Prediction

•	The validated QSAR model was used to predict pMIC values for compounds lacking experimental activity data.

•	Predicted compounds with higher pMIC values may represent promising bioactive candidates for further experimental validation.

Final Model

Selected Model

•	Ridge Regression + RFE 

Performance
Metric	Value
Q² (LOOCV)	0.633

RMSEcv	0.426

MAEcv	0.321

Y-Randomization

Metric	Value

Mean Random Q²	-1.741

Maximum Random Q²	0.148

The negative randomized Q² values indicate that the developed model is not due to chance correlation.

Required Python Packages

Main packages used:

•	pandas 

•	numpy 

•	scikit-learn 

•	matplotlib 

•	rdkit 

•	Mordred

Applications

This workflow can be adapted for:

•	Antimicrobial activity prediction 

•	Drug discovery 

•	Virtual screening 

•	Bioactivity modeling 

•	Small molecule prioritization



