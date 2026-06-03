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

| | name | id | minutes | contributor_id | submitted | tags | nutrition | n_steps | steps | description | n_ingredients | calories | total_fat | sugar | sodium | protein | saturated_fat | carbohydrates | avg_rating | highly_rated |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 1 brownies in the world best ever | 333281 | 40 | 985201 | 2008-10-27 | ['60-minutes-or-less', 'time-to-make', 'course'...] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] | 10 | ['heat the oven to 350f and arrange the rack i...'] | these are the most; chocolatey, moist, rich, d... | 9 | 138.4 | 10.0 | 50.0 | 3.0 | 3.0 | 19.0 | 6.0 | 4.0 | 1 |
| 1 | 1 in canada chocolate chip cookies | 453467 | 45 | 1848091 | 2011-04-11 | ['60-minutes-or-less', 'time-to-make', 'cuisine'...] | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] | 12 | ['pre-heat oven the 350 degrees f, in a mixi...'] | this is the recipe that we use at my school ca... | 11 | 595.1 | 46.0 | 211.0 | 22.0 | 13.0 | 51.0 | 26.0 | 5.0 | 1 |
| 2 | 412 broccoli casserole | 306168 | 40 | 50969 | 2008-05-30 | ['60-minutes-or-less', 'time-to-make', 'course'...] | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0] | 6 | ['preheat oven to 350 degrees, spray a 2 qua...'] | since there are already 411 recipes for brocco... | 9 | 194.8 | 20.0 | 6.0 | 32.0 | 22.0 | 36.0 | 3.0 | 5.0 | 1 |
| 3 | millionaire pound cake | 286009 | 120 | 461724 | 2008-02-12 | ['time-to-make', 'course', 'cuisine', 'prepara'...] | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] | 7 | ['freheat the oven to 300 degrees', 'grease a...'] | why a millionaire pound cake? because it's su... | 7 | 878.3 | 63.0 | 326.0 | 13.0 | 20.0 | 123.0 | 39.0 | 5.0 | 1 |
| 4 | 2000 meatloaf | 475785 | 90 | 2202916 | 2012-03-06 | ['time-to-make', 'course', 'main-ingredient'...] | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0] | 17 | ['pan fry bacon and set aside on a paper tow...'] | ready, set, cook! special edition contest entr... | 13 | 267.0 | 30.0 | 12.0 | 12.0 | 29.0 | 48.0 | 2.0 | 5.0 | 1 |