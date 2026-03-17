<h1 style="text-align:center; font-size:70px;">Recipe Ratings Analysis</h1>
Giselle Carames  |  DSC 80 Final Project

## Introduction

Eating is a central component of daily life, and many people rely on online platforms to discover and evaluate recipes. On websites like Food.com, users can rate recipes after trying them, creating a large dataset of community feedback. This project investigates whether there is a relationship between the **number of calories in a recipe** and the **average rating it receives**.

The main question explored in this analysis is:

**Is there a relationship between the number of calories in a recipe and the average rating it receives?**

To answer this question, I used two datasets from Food.com: a dataset of recipes and a dataset of user interactions and ratings.

### Recipes Dataset (`RAW_recipes.csv`)

This dataset contains **83,782 recipes** and **10 columns** describing each recipe.

**Relevant columns include:**

- **`name`** – Recipe name  
- **`id`** – Unique recipe identifier  
- **`minutes`** – Number of minutes required to prepare the recipe  
- **`contributor_id`** – User ID of the person who submitted the recipe  
- **`submitted`** – Date the recipe was submitted  
- **`tags`** – Food.com tags associated with the recipe  
- **`nutrition`** – Nutrition information in the format  
  `[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`  
  *(PDV = Percentage of Daily Value)*  
- **`n_steps`** – Number of preparation steps in the recipe  
- **`steps`** – Text instructions for recipe preparation  
- **`description`** – User-provided description of the recipe  

### Interactions Dataset (`interactions.csv`)

This dataset contains **731,927 user interactions** where users rated and reviewed recipes.

**Relevant columns include:**

- **`user_id`** – Unique identifier for the user submitting the rating  
- **`recipe_id`** – Unique identifier for the recipe being rated  
- **`date`** – Date the user rated or reviewed the recipe  
- **`rating`** – Rating given by the user  
- **`review`** – Text review written by the user  

---

## Data Cleaning and Exploratory Data Analysis

To prepare the data for analysis, I first cleaned the ratings data and then extracted usable nutrition values from the recipes dataset.

The `rating` column in the merged dataset originally included `0` values. Since a rating of `0` does not represent a real recipe score, I replaced those values with `NaN` so they would not incorrectly affect averages.

Next, I grouped the merged dataset by recipe `id` and computed the mean rating for each recipe. This produced an `avg_rating` column that represents the average score each recipe received across all user ratings. I then merged these average ratings back into the recipes dataset using a left merge on `id`, so that each recipe retained its original information along with its average rating.

The `nutrition` column also required cleaning because its values were stored as strings representing lists rather than as separate numeric columns. I split this column into seven individual numeric columns:

- `calories`
- `total_fat_pdv`
- `sugar_pdv`
- `sodium_pdv`
- `protein_pdv`
- `sat_fat_pdv`
- `carbs_pdv`

While it did increase the number of columns, this cleaning step made it possible to directly analyze calories and other nutritional variables as quantitative features.

Finally, I removed several columns that would not be used throughout the analysis to make the head of the dataframe more readable and clean.

### Head of Cleaned DataFrame

| minutes | avg_rating | calories | total_fat_pdv | sugar_pdv | sodium_pdv | protein_pdv | sat_fat_pdv | carbs_pdv |
|--------|------------|----------|---------------|-----------|------------|-------------|-------------|-----------|
| 40 | 4.0 | 138.4 | 10.0 | 50.0 | 3.0 | 3.0 | 19.0 | 6.0 |
| 45 | 5.0 | 595.1 | 46.0 | 211.0 | 22.0 | 13.0 | 51.0 | 26.0 |
| 40 | 5.0 | 194.8 | 20.0 | 6.0 | 32.0 | 22.0 | 36.0 | 3.0 |
| 120 | 5.0 | 878.3 | 63.0 | 326.0 | 13.0 | 20.0 | 123.0 | 39.0 |
| 90 | 5.0 | 267.0 | 30.0 | 12.0 | 12.0 | 29.0 | 48.0 | 2.0 |

### HeatMap Analysis

This heatmap shows the correlations between the nutritional variables extracted from the `nutrition` column to become multiple columns. As expected, it can be seen that calories are strongly correlated with each broken down nutritional component. As this project investigates the relationship between calorie content and recipe ratings, these relationships provide context for how nutritional components translate into the calories, which will then be studied in relation to ratings.

![Correlation Between Nutrition Variables](HeatMap_NutritionVariables.png)

### Distribution of Calories (Outliers Dropped)

Before generating this graph, the IQR of the calories distribution was calculated in order to generate new data without the outliers skewing the values. 

![Calorie Distribution](Calorie_Distribution_(outliers_dropped).png)

### Average Ratings Distribution

Here the general distribution of average ratings can be viewed to take into consideration when any skews are viewed later on as ratings will become a central variable in this analysis. 

![Average Ratings Distribution](Average_Ratings_Distribution.png)

### Bivariate Data Analysis

Before generating this graph, the IQR of the calories distribution was calculated in order to generate new data without the outliers skewing the values. 

![Calorie Distribution](Calories_vs_Average_Rating_(outliers_dropped).png)

This scatter plot shows the relationship between recipe calorie content and average rating after removing extreme calorie outliers. Ratings appear to remain relatively high across the full range of calorie values, and there is no clear linear trend. This suggests that calorie content alone does not strongly determine how highly a recipe is rated.

![Calorie Distribution](Distribution_of_Average_Ratings_(high_vs_low_calorie_recipes).png)

This histogram compares the distribution of average ratings for high-calorie and low-calorie recipes. Both groups show very similar rating patterns, with most recipes receiving ratings between 4 and 5. This indicates that recipes with higher calorie content do not receive systematically higher or lower ratings than lower-calorie recipes. Similarly to the previous graph, there appears to be no strong correlation.

### Grouped Table - Average Recipe Ratings by Calorie Range

| calorie_bin | mean_rating | median_rating | count |
|-------------|-------------|---------------|-------|
| (0, 200] | 4.63 | 5.0 | 24868 |
| (200, 400] | 4.62 | 5.0 | 27305 |
| (400, 600] | 4.62 | 5.0 | 14771 |
| (600, 800] | 4.62 | 5.0 | 6556 |
| (800, 1000] | 4.63 | 5.0 | 2946 |
| (1000, 1500] | NaN | NaN | 0 |
| (1500, 3000] | NaN | NaN | 0 |

The grouped table summary supports the plot's implication that there is likely no meaningful relationship between calories and ratings (Specifically analyzing mean and median rating).

---

## Assessment of Missingness

To investigate whether rating missingness depends on calorie content, I performed a permutation test comparing the mean calories of recipes with missing ratings and those with observed ratings. The test statistic was defined as the difference in mean calories between the two groups.

The histogram below shows the permutation distribution of this statistic under the null hypothesis that rating missingness is independent of calories. After performing 1000 permutations, the resulting p-value was approximately **0.0**.

Because the p-value is extremely small (< 0.05), we reject the null hypothesis. This suggests that rating missingness is related to calorie content, indicating that the probability of a rating being missing may be dependent on the number of calories found in the recipe.

![Permutation Distribution - Calories vs Rating](Permutation_Distribution-Calories_vs_Rating_Missingness.png)

A similar permutation test was conducted to examine whether rating missingness depends on specifically protein content. Recall that the ingredients list was previously broken down to include more components, including protein. The test compared the mean protein values for recipes with missing ratings and those with observed ratings.

The permutation distribution shown below represents the distribution of the test statistic under the null hypothesis that rating missingness is independent of protein content. The resulting p-value was approximately **0.213**.

Because this p-value is relatively large (> 0.05), we fail to reject the null hypothesis. This suggests there is little evidence that rating missingness depends on the protein content of the recipe.

![Permutation Distribution - Protein vs Rating](Permutation_Distribution-Protein_vs_Rating_Missingness.png)

Overall, these results suggest that rating missingness may depend on some observed variables (calorie content) but not others (protein levels). This indicates that the missingness mechanism may be **Missing At Random (MAR)** with respect to certain nutritional features such as protein.

---

## Hypothesis Testing

To investigate whether calorie content is associated with recipe ratings, I conducted a permutation test comparing the average ratings of high-calorie and low-calorie recipes.

**Null Hypothesis (H₀):**  
There is no difference in the average rating between high-calorie and low-calorie recipes.

**Alternative Hypothesis (H₁):**  
There is a difference in the average rating between high-calorie and low-calorie recipes.

To define the groups, recipes were split into **high-calorie** and **low-calorie** categories based on the median calorie value calculated from the dataset. The test statistic used was the **difference in mean average rating** between the two calorie groups. The initial observed difference was approximately **0.0083**

A permutation test was then conducted with **1000 permutations** by randomly shuffling the calorie group labels and recalculating the difference in mean ratings. This generated a permutation distribution representing the differences we would expect if calorie group and rating were unrelated (test under the null hypothesis).

The resulting **p-value was approximately 0.065**. Using a significance level of **α = 0.05**, this p-value is not small enough to reject the null hypothesis.

Therefore, there is **not sufficient evidence to conclude that high-calorie and low-calorie recipes have different average ratings**. While there may be a small difference, the results of this test suggest that calorie content alone does not strongly influence how users rate recipes.

---

## Framing a Prediction Problem

**Prediction Problem:**  
Predict whether a recipe will receive a high average rating based on characteristics of the recipe.

**Prediction Type:**  
Binary classification.

**Response Variable:**  
`high_rating`, a binary variable indicating whether a recipe’s average rating is greater than or equal to **4.5**.

**Time of Prediction:**  
At the time of prediction, information such as **calories, preparation time (minutes), and nutritional values** (protein_pdv, carbs_pdv, total_fat_pdv) are already known because they are part of the recipe information, therefore they can be used to train the model without data leakage.

**Evaluation Metric:**  
The model is evaluated using **accuracy**, which measures the proportion of correctly classified recipes.

**Justification for Metric:**  
Accuracy can be considered appropriate because the dataset does not appear to have a severe class imbalance between high-rated and lower-rated recipes. 

---

## Baseline Model

For the baseline model, I trained a **logistic regression classifier** to predict whether a recipe receives a high rating.

### Features Used
The model uses two features:

| Feature | Type | Description |
|------|------|------|
| `calories` | Quantitative | Total calories in the recipe |
| `minutes` | Quantitative | Time required to prepare the recipe |

Since both features are **quantitative numeric variables**, no encoding was required at this point.

### Response Variable
The response variable is **`high_rating`**, a binary variable indicating whether a recipe's average rating is **greater than or equal to 4.5**.

### Model
A **logistic regression model** was was used to train features. The dataset was split into **training and testing sets**, with **70% used for training and 30% used for testing** in order to evaluate the model on unseen data.

In regards to the model performance, it achieved an **accuracy of approximately 0.73** on the test set (rounded to the nearest 100th place decimal).

### Model Analysis
An accuracy of approximately 0.73 indicates that the model correctly classifies about **73% of recipes** as high-rated or not high-rated. Since the model only uses two basic features, its predictive ability is limited, however it is a good baseline. More feature engineering could likely improve the performance of the model.

---

## Final Model

To see if accuracy could be improved, I engineered additional features including **`calories_per_minute`** and **`log_minutes`**.

### Features Used

| Feature | Type | Description |
|-------|------|------|
| `calories` | Quantitative | Total calories in the recipe |
| `minutes` | Quantitative | Time required to prepare the recipe |
| `steps_count` | Quantitative | Number of steps in the recipe instructions |
| `log_minutes` | Quantitative | Log-transformed preparation time |

The feature **`calories_per_minute`** was created by dividing calories by minutes and replacing any null values with zero. This feature could potentially imply a measure for the effort that goes into a recipe. Since higher calories often correlate to more ingredients and therefore more time or difficult in the recipe. This is then measured in tangent with the number of minutes.

The feature **`log_minutes`** was created using a log transformation of preparation time. Applying a log transformation helps stabilize the distribution so the model can better capture differences.

All features are **quantitative**, so no categorical encoding was required.

### Final Model and Hyperparameters

The final model uses **logistic regression**, the same algorithm as the baseline model. To improve model performance, I performed **hyperparameter tuning using GridSearchCV with 5-fold cross-validation**.

The hyperparameter tuned was the regularization strength **C** (one divided by lambda), with the following values tested: 0.01, 0.1, 1, 10  

### Model Performance

The final model achieved an **accuracy of approximately 0.73** (rounded to the nearest hundreth place) on the test set.

### Comparison to the Baseline Model

The baseline model also achieved an accuracy of approximately **0.73**, so unfortunately there was little to no impovement on the model's accuracy. Although the accuracy did not improve much, the final model provides a slightly richer representation of the recipe characteristics than the baseline model did.

---

## Fairness Analysis

To evaluate whether the model behaves differently across types of recipes, I conducted a fairness analysis comparing **short recipes** and **long recipes**.

### Groups

Recipes were divided into two groups based on the **median preparation time (`minutes`)**:

- **Group X (Short Recipes):** preparation time < median  
- **Group Y (Long Recipes):** preparation time >= median

### Evaluation Metric

The fairness metric used was **precision**. Precision measures the proportion of recipes predicted to be high-rated that are in fact high-rated (only looking at the positive class of predictions).

### Hypotheses

**Null Hypothesis (H₀):**  
The precision of the model is the same for short recipes and long recipes.

**Alternative Hypothesis (H₁):**  
The precision of the model differs between short recipes and long recipes.

### Test Statistic

The test statistic used was the **difference in precision between the two groups**:

precision(short) - precision(long)

The observed difference in precision was approximately **0.038**.

### Significance Level

The significance level used for the test was **α = 0.05**.

### Permutation Test

To determine whether the observed difference could occur by chance, a **permutation test** was used to evaluate chances under the null hypothesis. 

### Fairness Analysis Conclusion

The observed difference in precision between short and long recipes is very large (approximately **0.928**). Therefore, the model’s predictive performance is fairly similar across the two groups (short and long recipes). Based on this result, using an alpha level of 0.05, there is **no strong evidence that the model behaves unfairly with respect to recipe preparation time**.
