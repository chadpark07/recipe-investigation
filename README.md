# Investigation on the Relationship Between the Number of Ingredients and Asian Recipes
UC San Diego DSC 80 Final Project

Author: Chanyoung Park


## Introduction
The question that will be explored in this project will be: "Do the number of steps and ingredients in asian recipes affect its rating when compared to other world cuisines?"

The datasets I will be using in this project contain recipes and ratings from food.com scraped 
from the authors of the paper "Generating Personalized Recipes from Historical User Preferences". 

The first dataset which contain recipes has 83792 rows with relevant columns such as:
- n_steps: Numbers of steps in the recipe
- steps: Step by step instructions of the recipe
- n_ingredients: Number of ingredients in the recipe
- ingredients: Ingredients needed for the recipe

The second dataset which contain ratings has 731927 rows with relevant columns such as:
- recipe_id: Id for a recipe that can be used to merge with the first dataset
- rating: The rating that a given user has given a specific recipe

All of the columns in the two datasets are shown below:

Recipes Dataset

| Column | Description |
|---------------|-----------------------------------------------------------------------------|
| `name` | Recipe name |
| `id` | Recipe ID |
| `minutes` | Minutes to prepare recipe |
| `contributor_id` | User ID who submitted this recipe |
| `submitted` | Date recipe was submitted |
| `tags` | Food.com tags for recipe |
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `n_steps` | Number of steps in recipe |
| `steps` | Text for recipe steps, in order |
| `description` | User-provided description |

Ratings Dataset

| Column | Description |
|------------|-----------------------------|
| `user_id` | User ID |
| `recipe_id` | Recipe ID |
| `date` | Date of interaction |
| `rating` | Rating given |
| `review` | Review text |

The results of the question being explored in this project will be important in determining how 
simplicity (n_steps) and resources needed (n_ingredients) for a certain cuisine affects 
the rating of a recipe. This information can be used by future recipe creators when considering the 
number of steps and ingredients to use in a recipe for a specific cuisine as users could 
rate recipes lower due to the number of steps and ingredients required.

## Data Cleaning and Exploratory Data Analysis



## Assessment of Missingness



## Hypothesis Testing



## Framing a Prediction Problem



## Baseline Model



## Final Model



## Fairness Analysis



