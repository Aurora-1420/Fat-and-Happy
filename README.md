# Fat and Happy

## Introduction
&emsp;This project uses two datasets that contain recipes and ratings food.com (recipes and interactions datasets). The recipes dataset contains 83,782 rows, where each row represents a unique recipe, and the interactions dataset contains 731927 rows, where each row represents an interaction with a review (a rating, some with reviews). After merging the two datasets, we cleaned the merged dataset, which involved replacing 0-star ratings with NaN and computing average ratings per recipe. In the end, our working dataset has 81,173 rows and 23 columns.


&emsp;Our final data science question was: Are higher calorie recipes rated more highly than lower calorie recipes by users?
Our question is important because users of food.com likely will care about if higher calorie recipes are rated higher, in order to determine whether it is worth it to put in effort towards a more indulgent recipe. If lower calorie recipes are equally as highly rated, then the effort is likely to be seen as not worth it. Analyzing and understanding this pattern could show if higher calorie recipes’ traits (such as richness) influence how food is rated (or perceived).

Relevant Columns:
* Calories: Total calorie count of the recipe, extracted from the nutrition field which contains calorie, fat, etc 
* Avg_rating: Mean rating (1–5 stars) across all user reviews
* Highly_rated: 1 if avg_rating ≥ 4, else 0 (derived)

## Data Cleaning and Exploratory Data Analysis
#### Data Cleaning Steps
1. Parsed nutrition column because the raw nutrition field was a string like [138.4, 10.0, 50.0, ...], with each calories, total_fat, sugar, sodium, protein, saturated_fat, and carbohydrates each being represented by a number (respectively). Stripped the brackets, split on commas, and cast to float, assigned the 7 values as separate columns, made using only one of the 7 values much easier.
2. Merged recipes with interactions: left merge of RAW_recipes onto interactions on recipe ID, preserving all recipes.
3. Replaced 0-star ratings with NaN: a rating of 0 indicates a missing rating, so replaced because they are not genuine low ratings (would have skewed rating distributions incorrectly).
4. Computed average rating per recipe: grouped by recipe ID and computed the mean rating, then merged this back onto the recipes dataset as avg_rating.
5. Dropped rows missing calories or avg_rating: recipes without a calorie value or any ratings were removed (81,173 rows final).
6. Created highly_rated column: a binary indicator (1 if avg_rating ≥ 4, else 0) helpful for use in later classification tasks.

| | name | calories | total_fat | sugar | sodium | protein | saturated_fat | carbohydrates | avg_rating | highly_rated |
|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 1 brownies in the world best ever | 138.4 | 10.0 | 50.0 | 3.0 | 3.0 | 19.0 | 6.0 | 4.0 | 1 |
| 1 | 1 in canada chocolate chip cookies | 595.1 | 46.0 | 211.0 | 22.0 | 13.0 | 51.0 | 26.0 | 5.0 | 1 |
| 2 | 412 broccoli casserole | 194.8 | 20.0 | 6.0 | 32.0 | 22.0 | 36.0 | 3.0 | 5.0 | 1 |
| 3 | millionaire pound cake | 878.3 | 63.0 | 326.0 | 13.0 | 20.0 | 123.0 | 39.0 | 5.0 | 1 |
| 4 | 2000 meatloaf | 267.0 | 30.0 | 12.0 | 12.0 | 29.0 | 48.0 | 2.0 | 5.0 | 1 |

#### Univariate Analysis
<iframe src="assets/cals_dist.html" width="800" height="600" frameborder="0"></iframe>
The distribution of calories is heavily right-skewed, with most recipes falling below 500 calories and the tail of high-calorie outliers (long tail). To help modeling,  log-transforming calories is helpful (more later).

#### Bivariate Analysis
<iframe src="assets/cal_v_rating.html" width="800" height="600" frameborder="0"></iframe>
The scatter plot of calories vs. average rating doesn't have a clear linear trend. Recipes tend to cluster near 4–5 stars (regardless of calories), which suggests that calories by itself may not be a strong predictor of rating.

#### Intresting Aggregates

| calorie_bin | num_recipes | mean_rating | pct_highly_rated |
|---|---|---|---|
| <200 | 24,868 | 4.634 | 0.935 |
| 200-400 | 27,305 | 4.621 | 0.934 |
| 400-600 | 14,771 | 4.621 | 0.936 |
| 600-800 | 6,556 | 4.621 | 0.933 |
| 800-1000 | 3,034 | 4.632 | 0.929 |
| 1000+ | 4,412 | 4.618 | 0.929 |

This table shows that mean ratings and the proportion of highly-rated recipes are relatively consistent across all calorie recipes (bins). They all lie around 4.62 stars and around 93% highly rated. This might suggest that calorie content has little to no relationship with how users rate recipes.


## Assessment of Missingness

#### NMAR Analysis

We think avg_rating could be NMAR, since recipe users may be less likely to leave a rating when they had a neutral or less extreme experience. In general, people tend to put in the effort to rate things when they feel strongly (either very satisfied or very dissatisfied). So as a result, the missingness of avg_rating might be related to the rating value itself (NMAR). If we obtained the additional data of notation of whether the user viewed the recipe without cooking it, or time spent on the page, avg_rating could become MAR. 

#### Missingness Dependency

We analyzed whether the missingness of avg_rating depends on other columns using permutation tests with the KS statistic.

##### avg_rating missingness vs calories (dependent!)
<iframe src="assets/ks_calories.html" width="800" height="600" frameborder="0"></iframe>

The observed KS statistic was 0.062 with a p-value of 0.000. We reject the null hypothesis that the missingness of avg_rating doesn't depend on calories. Recipes with missing ratings have a somewhat significant different calorie distribution than those that have ratings.

##### avg_rating missingness vs sodium (independent)
<iframe src="assets/ks_sodium.html" width="800" height="600" frameborder="0"></iframe>

The observed KS statistic was 0.024 with a p-value of 0.078. We fail to reject the null hypothesis, so it seems like the sodium content of a recipe does not appear to be related to whether it has a rating or not.

## Hypothesis Testing
Null Hypothesis: On average, the rating of higher calorie recipes is the same as the rating of lower calorie recipes. 


Alternative Hypothesis: On average, the rating of higher calorie recipes is different from the rating of lower calorie recipes.


Test Statistic: Absolute difference in mean average ratings between higher calorie recipes and lower calorie recipes. 

This is a good choice for answering our question because our alternative hypothesis is two-sided and does not predict a direction. 


Significance level: 0.05


p-value: 0.075


Conclusion: We fail to reject the null hypothesis at the 0.05 significance level with a p-value of 0.075. This means we don't have enough evidence to show that calories are associated with the average rating of recipes.

## Framing a Prediction Problem

&emsp;We performed binary classification to predict if a recipe will be highly rated (4+ stars). Our response variable is highly_rated, which is a binary column that we derived from avg_rating, where 1 means the recipe has an average rating of 4 or above and 0 means it does not. We chose this variable because it directly connects to our central question about if characteristics like calories influence how users rate them.

&emsp;Our evaluation metric is F1-score rather than accuracy. Since most of the recipes in our dataset are highly rated, a model that simply predicts "highly rated" for every recipe would be highly accurate, but it wouldn’t be useful. F1-score accounts for this by balancing precision and recall, which makes it a more meaningful measure of the model’s performance when the data is not balanced. 

&emsp;When we made predictions, we only used features that would be known before a recipe receives any ratings, such as calories, n_steps, n_ingredients, and minutes. These are all properties of the recipe that are set when the recipe is first submitted to food.com. We excluded any features derived from user interactions such as avg_rating, since those would not be available when trying to predict how a brand new recipe will be rated. 


## Baseline Model

Our baseline model is a Decision Tree Classifier with a maximum depth of 6. We implemented it in a single sklearn Pipeline that standardizes the features using a StandardScaler before fitting the classifier.

The model uses two features, which are both quantitative: calories (the total calorie count of the recipe) and n_steps (the number of steps in the recipe). There are no ordinal or nominal features in this model, so we didn’t need any other encodings. 

The model’s performance:

| Metric | Train | Test |
|---|---|---|
| Accuracy | 0.935 | 0.932 |
| F1-score | 0.966 | 0.965 |