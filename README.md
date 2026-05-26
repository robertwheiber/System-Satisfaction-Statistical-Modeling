# System-Satisfaction-Statistical-Modeling
This project investigates the specific extent to which various socioeconomic factors such as economic hardship, institutional trust, and perceived corruption explain the variance in political system satisfaction.

# The Analytical Problem
Understanding the abstract drivers of "satisfaction" (whether with a system, product, or institution) is a complex challenge that requires moving beyond basic averages into rigorous statistical inference. This capstone project investigates the specific extent to which various socioeconomic factors—such as economic hardship, institutional trust, and perceived corruption—explain the variance in political system satisfaction. By applying advanced regression techniques to large-scale survey data, this project models how these relationships behave across different environmental regimes.

# The Tech Stack
Language: Python

o  Statistical Modeling: statsmodels (Ordinary Least Squares & Weighted Least Squares)

o  Data Engineering & Math: pandas, numpy, scipy.stats

o  Data Visualization: matplotlib, seaborn

## Methodology & Code Highlights

1. Data Cleaning & Preprocessing
Standardizing messy survey data prior to modeling.

import numpy as np
import pandas as pd

## Standardize missing value codes across the dataset
missing_codes = [-1, -2, -3, -4, -5, 99]

for col in cols_to_clean:
    if col in df.columns:
        df[col] = df[col].replace(missing_codes, np.nan)

## Drop missing values for the strict regression dataset (N=70,646)

df_reg4 = df[['Q252_DV', 'Individual_Trust', 'Corrupt_Transpc', 'Interaction', 
              'Polity_c'] + controls].dropna()

2. Core Regression Modeling (WLS)
Defining complex regression formulas and applying Weighted Least Squares (WLS) to account for sample variance.

import statsmodels.formula.api as smf

## Defining the comprehensive regression formula including demographic controls
formula_str = """Q252_DV ~ Q49_SATLIF + Q50_SATFIN + Q51_WF_r + Q54_WI_r +
                Q70_CJ_r + Q71_CG_r + Q73_CP_r + Q112_POC +
                polity_c + corrupttransp +
                SEX + Q262_AGE + Q275_EDU + Q287_SC + Q288_HI"""

## Executing Weighted Least Squares (WLS) 
model_w = smf.wls(
    formula_str, 
    data=df_reg, 
    weights=df.loc[df_reg.index, 'WEIGHT']
).fit(cov_type='HC3') # Applying heteroskedasticity-consistent standard errors

3. Sub-population & Interaction Analysis
Splitting datasets to isolate independent variables based on environmental regimes (similar to enterprise A/B testing logic).

## Segmenting data for Subgroup Analysis (Democracy vs Non-Democracy)
df_demo    = df[df['demo_bin'] == 1]
df_nondemo = df[df['demo_bin'] == 0]

## Fitting models independently with robust standard errors (HC3)
m_demo    = smf.ols(formula, data=df_demo).fit(cov_type='HC3')
m_nondemo = smf.ols(formula, data=df_nondemo).fit(cov_type='HC3')

## Comparing the Transparency Beta Coefficients across regimes
print(f"Democracies Transparency Beta: {m_demo.params['corrupttransp']:.3f}")
print(f"Non-Democracies Transparency Beta: {m_nondemo.params['corrupttransp']:.3f}")

# The impact & Data Storytelling
o  Successfully quantified how subjective factors drive measurable outcomes, providing a framework for root-cause analysis on complex, high-noise datasets.

o  Synthesized dense statistical outputs into clear visualizations and actionable insights, bridging the gap between rigorous data science and strategic business reporting.

o  Demonstrated advanced proficiency in hypothesis testing, experimental design, and bias mitigation.
