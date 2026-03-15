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

To create a single working dataset, a **left merge** was performed of the recipes dataset with the interactions dataset on `id` and `recipe_id`. By using left merge it ensures every recipe is kept in the dataset, even if it has never recieved a review or rating.

After mergeing, all ratings with 0 was replaced with `NaN` because Food.com uses a 1-5 scale for ratings and 0 means that the user left a review without submitting a rating. Then another column was added called `average_rating` which consists of the average rating per recipe.

The columns most relevant to the central question are:

| Column | Description |
| --- | --- |
| `nutrition` | This is the most critical column for our analysis so I can extract **protein (PDV)**, **carbohydrates (PDV)**, **sugar (PDV)** from it to measure how "healthy" a recipe is. With High protein and low carboydrates being the central defenition of a healthy recipe. |
| `average_rating` | This is the outcome variable — to see if healthier recipes score higher or lower on average. |
| `name` | Helps identify and verify specific recipes when exploring patterns in the data. |
| `tags` | groups recipes into categories (e.g. "low-carb", "high-protein", "low-sugar", "vegetarian") to see if certain types of healthy recipes are rated differently. |
| `minutes` | Cooking time may be correlated with healthiness — for example, quick meals may tend to be less nutritious. Including it helps us explore confounding factors. |
| `n_steps` | Similarly, the complexity of a recipe may relate to both its healthiness and its rating — more complex recipes might be healthier but rated lower due to difficulty. |

### Univariate Analysis
In this section, We look at the distribution of protein, carbohydrates, and sugar among all the recipes to see how relevant they are in the dataframe and can figure out where the cutoff should be when taking to account outliers.

<iframe
src= "assests/protein_distribution.html"
width="800"
height="600"
frameborder="0"
></iframe>
