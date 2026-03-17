# Do Healthier Recipes Get Rated Differently?
**Author:** Jude Cole
## Overview
Everyone loves food, but not all food. Ingrediants in many recipes have been known to cause many health issues like causing diabetes, heart-probelms, and many more health issues. People tend to say that eating healthy is less enjoyable because of the taste. But does eating healthy actually makes food less enjoyable or do people rate nutritious recpies just as highly or even higher than indulgent ones?

This project explored data from [Food.com](https://www.kaggle.com/datasets/shuyangli94/food-com-recipes-and-user-interactions?select=RAW_interactions.csv), a large recipe-sharing platform where users submit recipes and leave ratings and reviews. Our dataset contains two merged datasets: a recipes dataset with 1,005,384 rows, and an interactions dataset with 3,659,635 rows, which we merge together to analyzie recipes alongside with the user ratings.

The central question for this project is: ***Do healthier recipes-those with higher protein, lower carbohydrates, and low sugar-tend to recieve different ratings than less healthy ones?***

This an important question as it gets at a common assumption that healthy food is less satisfying or enjoyable. If the data reveals that high-protein and low-carb recipes are rated just as high, or higher than unhealthy recipes, it is encourages anyone to eat better without having to sacrifice the taste.

### The Datasets
**Recipes** — this dataset contains 1,005,384 rows, where each row represents a unique recipe.

| Column | Description |
| --- | --- |
| `name` | Recipe name |
| `id` | Recipe ID |
| `minutes` | Minutes to prepare recipe |
| `contributor_id` | User ID who submitted this recipe |
| `submitted` | Date recipe was submitted |
| `tags` | Food.com tags for recipe |
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for "percentage of daily value" |
| `n_steps` | Number of steps in recipe |
| `steps` | Text for recipe steps, in order |
| `description` | User-provided description |

**Interactions** — this dataset contains 3,659,635 rows, where each row represents a user's interaction (rating and/or review) with a recipe.

| Column | Description |
| --- | --- |
| `user_id` | User ID |
| `recipe_id` | Recipe ID |
| `date` | Date of interaction |
| `rating` | Rating given (1–5) |
| `review` | Review text |

---
Now we are going to have to clean these two datasets into one concise dataframe so we can analyze the data in a clean enviornment.




### Merging the Datasets

To create a single working dataset, a **left merge** was performed of the recipes 
dataset with the interactions dataset on `id` and `recipe_id`. A left merge ensures 
every recipe is kept in the dataset, even if it has never received a review or rating.

After merging, all ratings of 0 were replaced with `NaN` because Food.com uses a 
1–5 scale and a rating of 0 simply means the user left a review without submitting 
a rating — including it would unfairly lower average scores. A new column called 
`average_rating` was then added, containing the average rating per recipe across 
all user interactions.

After merging, we also dropped columns that are not relevant to any part of our 
analysis — `contributor_id`, `submitted`, `steps`, `description`, and `tags` — 
to keep the dataset clean and focused. The remaining columns most relevant to our 
central question are:

| Column | Description |
| --- | --- |
| `nutrition` | The most critical column for our analysis — we extract **protein (PDV)**, **carbohydrates (PDV)**, and **sugar (PDV)** from it to measure how healthy a recipe is. High protein, low carbohydrates, and low sugar form our definition of a healthy recipe. |
| `average_rating` | Our outcome variable — used to see if healthier recipes score higher or lower on average. |
| `name` | Helps identify and verify specific recipes when exploring patterns in the data. |
| `minutes` | Cooking time may be correlated with recipe complexity and healthiness. Also used as a feature in our prediction model. |
| `n_steps` | The number of steps in a recipe — used both as a way to explore recipe complexity and as the response variable in our prediction model. |
| `n_ingredients` | The number of ingredients in a recipe — used as a feature in our prediction model since more ingredients generally implies more steps. |
| `calories` | Extracted from `nutrition` — used in our prediction model and fairness analysis. |
| `protein` | Extracted from `nutrition` — one of the three nutrients used to define a healthy recipe. |
| `sugar` | Extracted from `nutrition` — one of the three nutrients used to define a healthy recipe. |
| `carbohydrates` | Extracted from `nutrition` — one of the three nutrients used to define a healthy recipe. |
| `is_healthy` | *Added later to express based off protein, sugar, and carbohydrates if recipe is healthy or not.* |

Here is the head of **Recipes**
<table>
  <thead>
    <tr>
      <th></th>
      <th>name</th>
      <th>minutes</th>
      <th>nutrition</th>
      <th>n_steps</th>
      <th>n_ingredients</th>
      <th>average_rating</th>
      <th>calories</th>
      <th>sugar</th>
      <th>protein</th>
      <th>carbohydrates</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1 brownies in the world best ever</td>
      <td>40</td>
      <td>[138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]</td>
      <td>10</td>
      <td>9</td>
      <td>4.0</td>
      <td>138.4</td>
      <td>50.0</td>
      <td>3.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1 in canada chocolate chip cookies</td>
      <td>45</td>
      <td>[595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]</td>
      <td>12</td>
      <td>11</td>
      <td>5.0</td>
      <td>595.1</td>
      <td>211.0</td>
      <td>13.0</td>
      <td>26.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>412 broccoli casserole</td>
      <td>40</td>
      <td>[194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]</td>
      <td>6</td>
      <td>9</td>
      <td>5.0</td>
      <td>194.8</td>
      <td>6.0</td>
      <td>22.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>millionaire pound cake</td>
      <td>120</td>
      <td>[878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0]</td>
      <td>7</td>
      <td>7</td>
      <td>5.0</td>
      <td>878.3</td>
      <td>326.0</td>
      <td>20.0</td>
      <td>39.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2000 meatloaf</td>
      <td>90</td>
      <td>[267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]</td>
      <td>17</td>
      <td>13</td>
      <td>5.0</td>
      <td>267.0</td>
      <td>12.0</td>
      <td>29.0</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>

### Univariate Analysis
In this section, We look at the distribution of protein, carbohydrates, and sugar among all the recipes to see how relevant they are in the dataframe and can figure out where the cutoff should be when taking to account outliers.

<iframe
src= "assests/protein_distribution.html"
width="800"
height="600"
frameborder="0"
></iframe>
<iframe
src= "assests/carbs_distribution.html"
width="800"
height="600"
frameborder="0"
></iframe>
<iframe
src= "assests/sugar_distribution.html"
width="800"
height="600"
frameborder="0"
></iframe>

**Protein:** The distribution of protein (PDV) is heavily right-skewed, meaning 
the majority of recipes on Food.com have relatively low protein content. This makes 
sense as most everyday recipes are not specifically designed to be high in protein.

**Carbohydrates:** The distribution of carbohydrates (PDV) is also right-skewed, 
with most recipes having low to moderate carbohydrate content. The long tail suggests 
a smaller number of recipes — likely baked goods or pasta dishes — have very high 
carbohydrate levels.

**Sugar:** Similar to protein and carbohydrates, the distribution of sugar (PDV) 
is right-skewed. Most recipes contain low amounts of sugar, while a smaller number 
of recipes — likely desserts and sweet dishes — have very high sugar content.

### Bivariate Analysis

For this analysis, I examined the distribution of the rating of the recipe conditioned between the healthy and non-healthy recipes (based on sugar, carbohydrate, and protein levels). The graph below shows that recipes rated as 5/5 and 3/5 were more unhealthy than healthy ones, but 4/5 were actually primarily healthy recipes. We are going to dive into the deeper relationship between the proportions later on.

<iframe
src= "assests/Distribution_of_rating_based_on_healthiness.html"
width="800"
height="600"
frameborder="0"
></iframe>

### Interesting Aggregates

For this section, we investigated the relationship between the average protein, carbohydrates, sugar, and calories over the distribution of the ratings. To do this, we created a smaller dataframe 'nutrition_by_rating', which gave the each rating 1-5 as well as the mean and median of each nutrition detail. We identified the outliers using the IQR method and after grouping the rating and means and medians of the nutritonal details, we created a data visualization to understand it better.

<table>
  <thead>
    <tr>
      <th rowspan="2">Rating</th>
      <th colspan="2">Protein</th>
      <th colspan="2">Carbohydrates</th>
      <th colspan="2">Sugar</th>
      <th colspan="2">Calories</th>
    </tr>
    <tr>
      <th>Mean</th>
      <th>Median</th>
      <th>Mean</th>
      <th>Median</th>
      <th>Mean</th>
      <th>Median</th>
      <th>Mean</th>
      <th>Median</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1.0</td>
      <td>29.62</td>
      <td>12.0</td>
      <td>14.94</td>
      <td>9.0</td>
      <td>76.75</td>
      <td>26.5</td>
      <td>447.96</td>
      <td>276.55</td>
    </tr>
    <tr>
      <td>2.0</td>
      <td>30.80</td>
      <td>16.0</td>
      <td>15.04</td>
      <td>10.0</td>
      <td>73.99</td>
      <td>26.0</td>
      <td>445.26</td>
      <td>308.00</td>
    </tr>
    <tr>
      <td>3.0</td>
      <td>33.49</td>
      <td>19.0</td>
      <td>15.22</td>
      <td>9.0</td>
      <td>84.45</td>
      <td>24.0</td>
      <td>442.25</td>
      <td>312.25</td>
    </tr>
    <tr>
      <td>4.0</td>
      <td>34.88</td>
      <td>21.0</td>
      <td>13.51</td>
      <td>9.0</td>
      <td>61.36</td>
      <td>22.0</td>
      <td>422.67</td>
      <td>311.60</td>
    </tr>
    <tr>
      <td>5.0</td>
      <td>32.47</td>
      <td>17.0</td>
      <td>13.63</td>
      <td>8.0</td>
      <td>69.22</td>
      <td>23.0</td>
      <td>427.67</td>
      <td>301.50</td>
    </tr>
  </tbody>
</table>

In this grouped bar chart the mean nutritional content (protein, carbohydrates, sugar, and calories) for recipes at each rating level. The chart shows that there isn't that big of ranges of average protein, carbohydrates, sugar, and caloreis. 

<iframe
src= "assests/Mean_nutritional_content_by_recipe_rating.html"
width="800"
height="600"
frameborder="0"
></iframe>

## Assessment of Missingness
Three columns, `description`,`average_rating`, and `rating_rounded` all had a significant amount of missing values in the merged dataset, so we decided to asses them. It is also important to address that `name` also had 1 missing value but we expected it to a minor error in previous code so it was ignored and just changed to its proper name.

### NMAR Analysis

Looking at the columns with missing values, we expected `description` and `average_rating` to both be NMAR (Not Missing At Random). The missingness of 70 `description` values is most likely related ot the value itself. Contributors who didn't write a description probably felt their recipe was self-explanatory or simply chose not to. This means more basic recipes are more likely to have missing descriptions, and the missingess is related to teh nature of the recipe itself, which we cannot directly observe in the dataset. To make this MAR, we would want additional data such as the contributor's activity level on Food.com (total number of recipes submitted). More active reviewers are more likely to right descriptions which would explain the missingness through an observable column rather than the value itself.

### Missingness Dependency
The missingness of 2609 `average_rating`is going to be investigated in this section, as we are going to look whether the missingness in the column depends on the propotion of protien or `protein`, which is the amount of protein in the recipe, or `n_steps`, the number of steps of the recipe.

**Null Hypothesis:** The missingness of average_rating does not depend on the amount of protein in the recipe.

**Alternate Hypothesis:** The missingess of average_rating does depend on the proportion of protein in the recipe.

**Test Statistic:** The absolute difference of mean in the proportion of protein of the distribution of the group without missing ratings and the distribution of the group with missing ratings.

**Signficance Level:** 0.05

<iframe
src= "assests/Distribution_of_protein_by_missingness.html"
width="800"
height="600"
frameborder="0"
></iframe>
Next we ran a permutation test by shuffling the missingness of rating 1000 times to simulate the mean differences in the two distributions as described in the test statistic.
<iframe
src= "assests/Empirical_distribution_protein_vs_rating_missingness.html"
width="800"
height="600"
frameborder="0"
></iframe>

The **observed statistic** of **1.2974** is seen in the red line of the histogram and the **p_value** comes out to **0.1680** which is greater than our 0.05 signficance level so we fail to reject the null hypothesis meaning we the amount of protein doesn't not necessarly depend if a review is a missing or not.

**N Ingredients and Average Rating Missingness**

**Null Hypothesis:** The missingness of `average_rating` does not depend on the 
number of ingredients in the recipe — the two groups (missing and not missing) 
come from the same distribution.

**Alternate Hypothesis:** The missingness of `average_rating` does depend on the 
number of ingredients in the recipe.

**Test Statistic:** The absolute difference in mean `n_ingredients` between recipes 
with missing `average_rating` and recipes without missing `average_rating`.

**Significance Level:** 0.05

**Observed Statistic:** 0.2542

**P-value:** 0.0000

**Conclusion:** Since our p-value of 0.0000 is less than our significance level 
of 0.05, we reject the null hypothesis. The missingness of `average_rating` does 
depend on the number of ingredients in a recipe. Recipes with fewer ingredients 
are more likely to be unrated, which makes intuitive sense — simpler recipes with 
fewer ingredients may be less interesting or noteworthy to users, making them less 
likely to attract ratings and reviews.

<iframe
src= "assests/fig_missingness2.html"
width="800"
height="600"
frameborder="0"
></iframe>

## Hypothesis Testing
**Question:** Do healthier recipes (high protein, low carbohydrates, low sugar) 
tend to receive different average ratings than less healthy recipes?

We define a recipe as "healthy" if it meets all three conditions:
- Protein content is at or above the median protein level
- Carbohydrate content is at or below the median carbohydrate level
- Sugar content is at or below the median sugar level

**Null Hypothesis:** Healthy and less healthy recipes are rated on the same scale. 
Any observed difference in average ratings between the two groups is due to random 
chance.

**Alternative Hypothesis:** Healthy recipes receive different average ratings than 
less healthy recipes.

**Test Statistic:** The difference in mean average rating between healthy recipes 
and less healthy recipes (healthy minus less healthy). We chose this over the 
absolute difference because it tells us the direction of any effect — whether 
healthy recipes are rated higher or lower.

**Significance Level:** 0.05

<iframe
src= "assests/Empirical_distribution_under_null.html"
width="800"
height="600"
frameborder="0"
></iframe>

**Results:** We observed a difference of 0.0013 in mean average rating between 
healthy and less healthy recipes (healthy mean: 4.6264, less healthy mean: 4.6252). 
After running 1000 permutations, we obtained a p-value of 0.8430.

**Conclusion:** Since our p-value of 0.8430 is much greater than our significance 
level of 0.05, we fail to reject the null hypothesis. This suggests that healthy 
recipes (high protein, low carbohydrates, low sugar) do not appear to be rated 
differently than less healthy recipes on Food.com. The observed difference of 
0.0013 stars is extremely small and is consistent with what we would expect to 
see under random chance alone. Note that this does not prove that healthiness has 
absolutely no effect on ratings — it only suggests that based on our data, any 
observed difference could plausibly be due to random chance.

## Framing a Prediction Problem
We are predicting `n_steps`, the number of steps in a recipe. This will involve **regression** since `n_steps` is a continuous numerical variable. Originally we intended to predict `average_rating` since our project centers around the relationship between healthy recipes and ratings. However, the hypothesis test conlcuded that healthy or not, the ratings are nearly identical - with means of 4.6264 and 4.6252 respectively and p-val of 0.8430. This means predicting average_variable with tehse would be very complex and uninteresting. Instead, we decided to go a different route and want to predict `n_steps` as our response variable since it is a natural measure of recipe complexity and has more meaningful relationship with other features in the dataset of recipes.

We will evaluate our model using **RMSE (Root Mean Squared Error)** because `n_steps` is a continuous variable and will be the same units as `n steps` meaning an RMSE of 3 means our predictions are off by 3 steps on average. We chose RMSE over MAE because it will take outliers into account better. We would want to know if a 3 step recipe was predicted to be 15 steps off rather than off by 1 or 2. 

The features used in this model are known at the time of prediciton - `n_ingredients`, `minutes`, and `calories`. All these features are submitted when the recipe is submitted, long before any user rates or reviews it. `average_rating` is not used as a feature because ratings are only available after users have interacted with the recipe.

## Baeline Model

### Performance
<table>
  <thead>
    <tr>
      <th></th>
      <th>RMSE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Train</td>
      <td>5.6618</td>
    </tr>
    <tr>
      <td>Test</td>
      <td>5.6682</td>
    </tr>
  </tbody>
</table>

Our baseline model achieves a Train and Test RMSE of approximately 5.6, meaning 
our predictions are off by about 5.7 steps on average. Given that the average 
recipe has around 9-10 steps, this is a relatively large error and suggests our 
baseline model is not yet a good predictor of recipe complexity. However, the 
similar Train and Test RMSE values indicate the model is not overfitting and 
generalizes well to unseen data. We expect to significantly improve this in our 
final model by switching to a Random Forest Regressor and adding more features.

## Final Model

Our final model uses a **Random Forest Regressor** with the following features:

**Kept from Baseline:**
- `n_ingredients` — **quantitative**. The number of ingredients in a recipe is the strongest predictor of complexity. For example if a recipe requires 20 ingredients is almost always requires more steps than a recipe with only 5 ingredients. Scaled with StandardScaler
- `minutes` — **quantitative**. Cooking time, scaled with StandardScaler. Cooking time is directly related to complexity as the longer it takes for a recipe to be made the more steps it would have then a quick recipe with shorter steps.

**Added in Final Model:**
- `calories` — **quantitative**. Higher calorie recipes tend to be more complex because of cooking techniques such as frying, layering, or combining multiple components, all of which would increase the number of steps and is scaled with StandardScaler.
- `sugar` — **quantitative**. With higher sugar recipes such as baked goods and desserts typically require precise mult-step processes like creaming butter, folding batter, or tempering choclate which would increase n_steps. Nutritional complexity indicator, scaled with StandardScaler.
- `is_healthy` — **nominal**. Healthy recipes may follow a distinct pattern of preparation compared to less healthy ones for examplehealthy recipes might need more minimally processed ingredients requiring less steps than less-healthy foods. Encoded with OneHotEncoder.

We used a `ColumnTransformer` inside a single `sklearn` `Pipeline` to apply 
`StandardScaler` to all numerical features and `OneHotEncoder` to `is_healthy`.

### Hyperparameter Tuning
We used `GridSearchCV` with 5-fold cross validation to tune two hyperparameters:
- `max_depth` — controls how deep each tree grows, preventing overfitting
- `n_estimators` — number of trees in the forest

The best parameters found were `max_depth=10` and `n_estimators=200`.

### Performance
<table>
  <thead>
    <tr>
      <th></th>
      <th>RMSE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Baseline Train</td>
      <td>5.6618</td>
    </tr>
    <tr>
      <td>Baseline Test</td>
      <td>5.6682</td>
    </tr>
    <tr>
      <td>Final Train</td>
      <td>4.91</td>
    </tr>
    <tr>
      <td>Final Test</td>
      <td>5.31</td>
    </tr>
  </tbody>
</table>

Our final model improved on the baseline by reducing Test RMSE from 5.6 to 5.31, 
a reduction of about 0.3 steps. While the improvement is modest, it shows that 
adding nutritional features and using a more powerful non-linear model does help. 
The gap between Train RMSE (4.91) and Test RMSE (5.31) is small and expected, 
indicating the model is not significantly overfitting and generalizes reasonably 
well to unseen data.

The modest overall improvement suggests that `n_steps` is inherently difficult 
to predict from nutritional and time features alone — recipe complexity is likely 
influenced by factors not captured in our dataset, such as cooking technique, 
cuisine type, or the skill level required.

## Fariness Analysis

We investigated whether our final model performs fairly across two groups of recipes:
- **Group X:** Low calorie recipes (calories at or below the median of 305.20)
- **Group Y:** High calorie recipes (calories above the median of 305.20)

We chose these groups because calorie content is a fundamental property of recipes 
that separates simple, light dishes from more complex, rich ones. If our model 
performs significantly worse for one group, it would suggest a systematic bias in 
how well it predicts recipe complexity across different types of dishes.

**Evaluation Metric:** RMSE — since this is a regression model we use RMSE to 
measure prediction error for each group separately.

**Null Hypothesis:** Our model is fair. Its RMSE for low calorie and high calorie 
recipes are roughly the same, and any observed difference is due to random chance.

**Alternative Hypothesis:** Our model is unfair. Its RMSE for high calorie recipes 
is different from its RMSE for low calorie recipes.

**Test Statistic:** Difference in RMSE between high calorie and low calorie recipes 
(high calorie RMSE minus low calorie RMSE).

**Significance Level:** 0.05

**Results:**
- High Calorie RMSE: 5.7387
- Low Calorie RMSE: 4.8641
- Observed Difference: 0.8745
- P-value: 0.0000

**Conclusion:** Since our p-value of 0.0000 is less than our significance level of 
0.05, we reject the null hypothesis. This suggests our model is unfair — it performs 
significantly worse on high calorie recipes (RMSE: 5.74) than on low calorie recipes 
(RMSE: 4.86). A difference of 0.87 steps in RMSE is meaningful given that the average 
recipe has about 10 steps. This disparity likely occurs because high calorie recipes 
tend to be more complex and varied in structure — rich dishes like casseroles, baked 
goods, and multi-component meals are harder to predict in terms of number of steps 
than simpler low calorie dishes. Note that while we reject the null hypothesis, this 
does not prove our model is definitively unfair — it suggests that further 
investigation and potential model improvements targeting high calorie recipes 
may be warranted.

<iframe
src= "assests/Fairness_Accurarcy.html"
width="800"
height="600"
frameborder="0"
></iframe>
