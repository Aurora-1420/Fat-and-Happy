# Fat-and-Happy

## Introduction
&emsp;This project uses two datasets that contain recipes and ratings food.com (recipes and interactions datasets). The recipes dataset contains 83,782 rows, where each row represents a unique recipe, and the interactions dataset contains 731927 rows, where each row represents an interaction with a review (a rating, some with reviews). After merging the two datasets, we cleaned the merged dataset, which involved replacing 0-star ratings with NaN and computing average ratings per recipe. In the end, our working dataset has 81,173 rows and 23 columns.


&emsp;Our final data science question was: Are higher calorie recipes rated more highly than lower calorie recipes by users?
Our question is important because users of food.com likely will care about if higher calorie recipes are rated higher, in order to determine whether it is worth it to put in effort towards a more indulgent recipe. If lower calorie recipes are equally as highly rated, then the effort is likely to be seen as not worth it. Analyzing and understanding this pattern could show if higher calorie recipes’ traits (such as richness) influence how food is rated (or perceived).

Relevant Columns:
* Calories: Total calorie count of the recipe, extracted from the nutrition field which contains calorie, fat, etc 
* Avg_rating: Mean rating (1–5 stars) across all user reviews
* Highly_rated: 1 if avg_rating ≥ 4, else 0 (derived)

Hypothesis Testing
Null Hypothesis: On average, the rating of higher calorie recipes is the same as the rating of lower calorie recipes. 

Alternative Hypothesis: On average, the rating of higher calorie recipes is different from the rating of lower calorie recipes.

Test Statistic: Absolute difference in mean average ratings between higher calorie recipes and lower calorie recipes. 
This is a good choice for answering our question because our alternative hypothesis is two-sided and does not predict a direction. 

Significance level: 0.05

p-value: 0.075

Conclusion: We fail to reject the null hypothesis at the 0.05 significance level with a p-value of 0.075. This means we don't have enough evidence to show that calories are associated with the average rating of recipes.

