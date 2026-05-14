# BoomBikes Bike Sharing - Multiple Linear Regression

This is my submission for the Linear Regression assignment from the upGrad /
IIIT-B PG Diploma in Machine Learning & AI program.

## Table of Contents
- [About the project](#about-the-project)
- [Problem statement](#problem-statement)
- [Dataset](#dataset)
- [What I did](#what-i-did)
- [Final model and results](#final-model-and-results)
- [Conclusions](#conclusions)
- [Tools used](#tools-used)
- [Files in this folder](#files-in-this-folder)
- [How to run](#how-to-run)
- [Acknowledgements](#acknowledgements)

## About the project
BoomBikes is a US bike sharing company that saw a big drop in revenue during
the COVID-19 lockdown. They want to be ready when the market opens up so they
hired a consulting firm to figure out which factors actually drive daily bike
rental demand. The aim of this project is to build a multiple linear
regression model that can explain how weather and calendar variables affect
the daily count of rentals (`cnt`).

## Problem statement
- Which variables are significant in predicting bike sharing demand?
- How well do those variables explain the demand?

The target variable is `cnt` (total daily rentals = casual + registered).

## Dataset
- File: `day.csv` with 730 rows and 16 columns.
- Source: Capital Bikeshare daily counts for 2018 and 2019.
- Important columns:
  - `season`, `yr`, `mnth`, `holiday`, `weekday`, `workingday`, `weathersit`
    (categorical / binary)
  - `temp`, `atemp`, `hum`, `windspeed` (numeric)
  - `casual`, `registered`, `cnt` (counts - only `cnt` is used as the target,
    casual and registered would leak the answer).

A full data dictionary is in `Readme.txt` which came with the assignment.

## What I did
1. **Cleaning** - dropped `instant`, `dteday`, `casual`, `registered` and
   `atemp`. Dropping atemp because it is almost the same as `temp`
   (correlation ~0.99) and would cause multicollinearity.
2. **Categorical encoding** - mapped `season`, `weathersit`, `mnth`, `weekday`
   to readable string labels and then created dummies with
   `drop_first=True` to avoid the dummy variable trap.
3. **EDA** - pair plots, correlation heatmap and box plots of every
   categorical column against `cnt`.
4. **Train test split** - 70/30 with `random_state=42`.
5. **Scaling** - MinMax scaling on the numerical columns and the target,
   fit on train only.
6. **Feature selection** - used RFE on a `LinearRegression` estimator to pick
   15 features out of 28.
7. **Model building** - iterative OLS using statsmodels. After each fit I
   looked at p-values and VIF, dropped the worst feature and refit. I
   stopped when all p-values were <= 0.05 and all VIFs were below 10.
8. **Residual analysis** - checked the four classical assumptions
   (linearity, normality, constant variance, independence).
9. **Evaluation** - R-squared on the test set and compared it with train
   R-squared to check for overfitting.

## Final model and results
The final model has 14 features (plus the intercept). All p-values are
< 0.05 and all VIFs are below 10.

| Metric | Value |
|---|---|
| Train R-squared | 0.838 |
| Train Adjusted R-squared | 0.834 |
| Test R-squared | 0.827 |
| Durbin-Watson | 2.06 (close to 2 = independent residuals) |

### Top 3 features driving demand
1. `temp` (+0.404) - temperature is the strongest positive driver.
2. `weathersit_light_rain_snow` (-0.279) - bad weather is the strongest
   negative driver.
3. `yr` (+0.242) - 2019 has clearly higher demand than 2018, which means
   the platform is growing year on year.

### Other significant features
- `season_spring` (-), `season_winter` (+)
- `weathersit_mist` (-)
- `windspeed` (-), `workingday` (+), `weekday_sat` (+)
- Months: `mnth_mar` (+), `mnth_sep` (+), `mnth_jul` (-), `mnth_nov` (-),
  `mnth_dec` (-)

## Conclusions
- Bike sharing demand is mostly driven by **weather and season**. BoomBikes
  should plan extra inventory and marketing for fall, the warmer months
  (May-Oct) and clear weather days.
- Demand drops sharply on light rain/snow days and in winter months, so
  reducing supply or running promotions on those days makes sense.
- The positive `yr` coefficient confirms organic year on year growth, so
  investing in extra bikes after the lockdown is justified.
- Train and test R-squared are very close (0.838 vs 0.827), so the model is
  not overfitting.

## Tools used
- Python 3
- pandas, numpy
- matplotlib, seaborn
- scikit-learn (train_test_split, MinMaxScaler, RFE, r2_score)
- statsmodels (OLS, VIF)
- Jupyter Notebook

## Files in this folder
```
.
├── Bike_Sharing_Linear_Regression.ipynb   # Main notebook (with outputs)
├── Subjective_Questions_Answers.pdf       # Answers to the subjective questions
├── day.csv                                # Dataset
└── README.md                              # This file
```

## How to run
1. Clone the repo.
2. (Optional) create a virtual environment.
3. Install the dependencies:
   ```
   pip install pandas numpy matplotlib seaborn scikit-learn statsmodels jupyter
   ```
4. Open the notebook:
   ```
   jupyter notebook Bike_Sharing_Linear_Regression.ipynb
   ```
5. Run all cells (Kernel -> Restart & Run All).

## Acknowledgements
- Dataset citation: Fanaee-T, Hadi, and Gama, Joao. "Event labeling combining
  ensemble detectors and background knowledge", Progress in Artificial
  Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg,
  doi:10.1007/s13748-013-0040-3.
- Assignment provided by upGrad and IIIT Bangalore as part of the PG Diploma
  in Machine Learning and AI.

## Contact
Created by Shulamani for the upGrad Linear Regression assignment.
