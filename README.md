# Socioeconomic factors and high school ACT/SAT Scores

## Project overview
Average ACT/SAT scores differ widely between U.S. high schools. This project asks whether those differences follow the socioeconomic characteristics of the communities that schools serve, as a way of studying inequality of educational opportunity. It also investigates whether charter and non-charter high schools differ in average scores after accounting for socioeconomic context and state.

The analysis combines EdGap test-score and socioeconomic data with NCES school information. The cleaned sample contains 7,227 high schools in 20 states. Socioeconomic predictors explain about 63% of the variation in school average scores, and the share of students eligible for free or reduced-price lunch is by far the strongest predictor. Charter schools score about 1.3 points lower on average, but the gap becomes small and statistically uncertain after adjusting for socioeconomic factors.
* **Objective:** Determine whether school-level ACT/SAT performance is predicted by socioeconomic factors, and whether charter status adds information beyond them.
* **Domain:** Education / Public Policy
* **Key Techniques:** Data cleaning and merging, imputation, exploratory data analysis, ordinary least squares regression, model comparison (ANOVA), cross-validation
  
## Project structure
- `data/` — Raw and processed data produced by the notebook
- `code/` — Jupyter notebooks and Python scripts
- `reports/` — Generated reports and visualizations
- `requirements.txt` — Dependencies
- `README.md` — Project documentation

## Data source and description
* **Source:**
  * EdGap.org data (2016), `EdGap_data.xlsx`: [DATA 5100 GitHub repository](https://github.com/brian-fischer/DATA-5100)
  * NCES school information, `ccd_sch_029_1617_w_1a_11212017.csv`: [Dropbox download](https://www.dropbox.com/scl/fi/fkafjk8902sq8ptxh94r2/ccd_sch_029_1617_w_1a_11212017.csv?rlkey=gucrdz5f6e38bezz2y3yalxbw&dl=0)
* **Description:** 
The EdGap data contain average ACT (or SAT-equivalent) scores for schools along with census-tract median household income, unemployment rate, percentage of adults with a college degree, percentage of children in married-couple families, and the percentage of students eligible for free or reduced-price lunch. The NCES data contain basic identifying information for each school (name, state, zip code, level, charter status). The two are joined on the NCES school ID (NCESSCH).
* **Clean data:** 
The clean data file is [`data/education_clean.csv`](data/education_clean.csv). It contains 7,227 high schools with valid outcome scores and imputed numerical predictors. The charter comparison uses 7,028 of these schools: 170 charter and 6,858 non-charter. Schools with charter status "Not applicable" are excluded from that comparison.

## Analysis
All data preparation, exploratory analysis, modeling, and evaluation are in one notebook, [`code/Education.ipynb`](code/Education.ipynb). 

The major steps are:
1. Load the raw data sets, convert the school identifier to a common type, rename columns, and left-join the NCES information onto the EdGap records.
2. Set invalid values (negative lunch shares and ACT scores below 1) to missing, keep high schools, and remove schools with a missing average ACT score.
3. Impute missing values in the five numerical socioeconomic predictors with `IterativeImputer`, derive a charter indicator (`is_charter`), and export `education_clean.csv`.
4. Explore distributions, outliers, correlations, and differences between charter and non-charter schools.
5. Fit ordinary least squares models using the lunch share alone, a quadratic lunch term, all five socioeconomic predictors, and a reduced set of predictors. Compare nested models and inspect residuals.
6. Standardize the reduced predictors to compare the size of their effects, and evaluate the reduced model with five-fold cross-validation.
7. Estimate the charter score difference before adjustment, after socioeconomic adjustment, and after adding state indicators.
The analysis uses Python with pandas, NumPy, Matplotlib, seaborn, Plotly, statsmodels, and scikit-learn.

## Results
* **Socioeconomic prediction:** The five socioeconomic predictors explain 62.8% of the variation in school average ACT scores (R-squared 0.628). A reduced model with unemployment, college-degree share, and lunch eligibility performs almost identically: R-squared 0.628, MAE 1.145 ACT points, and RMSE 1.530 ACT points.
* **Strongest predictor:** Lunch eligibility alone explains 61.4% of the variation. In the reduced model, a 10-percentage-point increase in eligibility is associated with a 0.759-point lower ACT average, holding the other predictors fixed.
* **Model complexity:** Adding median income and the married-couple share to the reduced model does not significantly improve the fit (p = 0.807). A quadratic lunch term gives only a very small improvement in prediction accuracy.
* **Validation:** The mean five-fold cross-validated R-squared is 0.600. Imputation and predictor selection were done before cross-validation, so this estimate has preprocessing and selection limitations. The MAE and RMSE above are in-sample measures.
* **Charter comparison:** Charter schools average 19.02 ACT points versus 20.36 for non-charter schools, and they serve more disadvantaged communities. The estimated charter minus non-charter gap shrinks from -1.341 points to -0.186 after socioeconomic adjustment (95% CI: -0.423 to 0.051; p = 0.124), and to -0.095 after adding state controls (95% CI: -0.318 to 0.127; p = 0.401).

These findings describe associations, not causal effects. The data cover only 20 states, and school and tract averages cannot establish relationships for individual students. The adjusted charter results do not show that the two school types are equivalent: with only 170 charter schools, a small difference cannot be ruled out.

## Authors
Quynh Giang Tran

## License
The original code and documentation in this repository are licensed under the [MIT License](LICENSE). Third-party data sets remain subject to the terms of their sources.
