# Fat-and-Happy

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