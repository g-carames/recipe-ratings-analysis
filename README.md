# Recipe Ratings Analysis
Giselle Carames  
DSC 80 Final Project

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

### Distribution of a Single Column

![Distribution Plot](your_distribution_plot.png)

Write 1–2 sentences here explaining what the distribution shows.

### Relationship Between Two Columns

![Relationship Plot](your_relationship_plot.png)

Write 1–2 sentences here explaining what this plot suggests about the relationship between the variables.

### Grouped Table or Pivot Table

| Group | Value |
|------|------|
| Example | Example |
| Example | Example |

Write 1–2 sentences here explaining why this grouped table matters.
---

## Assessment of Missingness
Explain whether any variables had missing values and whether the missingness appeared random or not.

---

## Hypothesis Testing
Describe the hypothesis test you ran.  
Explain the null hypothesis, alternative hypothesis, and what you concluded.

---

## Framing a Prediction Problem
Explain what you are predicting.  
Example: predicting whether a recipe receives a **high rating**.

Mention:
- response variable
- features used
- evaluation metric

---

## Baseline Model
Describe the first model you built and how it performed.

Example:
- model type
- features used
- evaluation metric
- baseline accuracy

---

## Final Model
Explain how you improved your model.

Mention:
- new features
- model tuning
- final performance

---

## Fairness Analysis
Discuss whether the model performs differently across groups (for example different recipe categories or cooking times).
