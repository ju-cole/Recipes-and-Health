# Recipes-and-Health

**Author:** Jude Cole

**Website:** [https://ju-cole.github.io/Recipes-and-Health/](https://ju-cole.github.io/Recipes-and-Health/)

---

## Overview

This data science project explores whether healthier recipes — those higher in protein 
and lower in carbohydrates and sugar — tend to receive different ratings than less 
healthy ones, using data from Food.com.

---

## Dataset

Two datasets from Food.com were used:
- **Recipes** — 1,005,384 rows, each representing a unique recipe with nutritional 
  information, cooking time, and number of steps
- **Interactions** — 3,659,635 rows, each representing a user rating and review of 
  a recipe

---

## Project Steps

### 1. Introduction
Introduced the datasets and established the central question: do healthier recipes 
(high protein, low carbohydrates, low sugar) receive different ratings than less 
healthy ones?

### 2. Data Cleaning and EDA
- Merged the two datasets using a left merge on recipe ID
- Replaced ratings of 0 with NaN and calculated average rating per recipe
- Extracted nutritional columns (protein, carbohydrates, sugar, calories) from the 
  nutrition column
- Performed univariate analysis on protein, carbohydrates, sugar, and average rating
- Performed bivariate analysis comparing healthy vs less healthy recipes against 
  average ratings, and number of ingredients vs number of steps

### 3. Assessment of Missingness
- Identified `description` as NMAR
- Performed permutation tests on `average_rating` missingness against `protein` 
  (does not depend, p=0.19) and `n_ingredients` (does depend, p=0.0000)

### 4. Hypothesis Testing
- Tested whether healthy recipes (high protein, low carbs, low sugar) are rated 
  differently than less healthy ones
- Used a permutation test with difference in means as the test statistic
- Result: p-value of 0.8430, failed to reject the null hypothesis — healthy and 
  less healthy recipes are rated similarly

### 5. Framing a Prediction Problem
- Chose to predict `n_steps` (number of steps in a recipe) as a regression problem
- Switched from predicting `average_rating` since hypothesis testing showed nutrition 
  and ratings are largely unrelated
- Evaluation metric: RMSE

### 6. Baseline Model
- Used Linear Regression with `n_ingredients` and `minutes` as features
- Both features scaled with StandardScaler inside a sklearn Pipeline
- Train RMSE: 5.6, Test RMSE: 5.6

### 7. Final Model
- Switched to Random Forest Regressor
- Added `calories`, `protein`, `sugar`, and `is_healthy` as additional features
- Used GridSearchCV to tune `max_depth` and `n_estimators`
- Best params: max_depth=10, n_estimators=200
- Train RMSE: 4.91, Test RMSE: 5.31 — improvement over baseline

### 8. Fairness Analysis
- Split recipes into high calorie and low calorie groups (median: 305.20 calories)
- Permutation test on difference in RMSE between groups
- Result: p-value of 0.0000 — model performs significantly worse on high calorie 
  recipes (RMSE: 5.74) than low calorie recipes (RMSE: 4.86)

---

## Tools Used
- Python (Pandas, NumPy, Plotly, Scikit-learn)
- Jupyter Notebook
- GitHub Pages
