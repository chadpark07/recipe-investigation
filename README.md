# Investigation on the Relationship Between the Number of Ingredients and Asian Recipes
UC San Diego DSC 80 Final Project

Author: Chanyoung Park


## Introduction

The question that will be explored in this project will be: **"Do the number of steps and ingredients in asian recipes affect its rating when compared to other world cuisines?"**

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

**Recipes Dataset**

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

**Ratings Dataset**

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

To get the dataset cleaned and ready for analysis the following steps were applied to the dataset:

1. Split values in the nutrition column to individual columns
	- Some columns, like 'nutrition', contain values that look like lists, but are actually strings that look like lists. For instance, per the data dictionary, each value in the 'nutrition' column contains information in the form "[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)]"
	- Individual columns in my dataset titled 'calories', 'total fat', etc. were created using the ‘.split()’ function and ‘expand’ parameter’ and converted to type floats
	- The original  'nutrition’ column was dropped and these new split features can be used during data analysis as well as hypothesis testing and model tuning

2. Convert the 'tags' column into lists and add a column checking for asian and american recipes
	- The ‘tags’ column is converted to lists using ‘.split()’
	- To investigate the question posed above, columns called ‘is_asian’ and ‘is_american’ containing boolean values will be added to the dataset

3. Left merge the recipes and interactions datasets together
	- The two datasets were left merged using the ‘id’ of the “Recipes” dataset and ‘recipe_id’ of the “Ratings” dataset
	- This merged dataset gives us the ratings and reviews for each recipe

4. In the merged dataset, fill all ratings of 0 with np.nan
	- Ratings are on a scale from 1 to 5 so ratings with 0 in the dataset were replaced with np.nan so that they do not interfere with analysis later on

5. Add Series containing the average rating per recipe to the dataset
	- Getting the average rating per recipe which can have multiple ratings could later be used to understand the general rating across recipes

The cleaned merged dataset has 234429 rows and 27 columns. The ‘dtypes’ and first 5 rows of the cleaned dataset is shown below:

**Columns of the Merged Dataset**

| Column                | Data Type  |
|-----------------------|------------|
| `name`               | object     |
| `id`                 | int64      |
| `minutes`            | int64      |
| `contributor_id`     | int64      |
| `submitted`          | object     |
| `tags`               | object     |
| `n_steps`            | int64      |
| `steps`              | object     |
| `description`        | object     |
| `ingredients`        | object     |
| `n_ingredients`      | int64      |
| `calories (#)`       | float64    |
| `total fat (PDV)`    | float64    |
| `sugar (PDV)`        | float64    |
| `sodium (PDV)`       | float64    |
| `protein (PDV)`      | float64    |
| `saturated fat (PDV)`| float64    |
| `carbohydrates (PDV)`| float64    |
| `is_asian`           | bool       |
| `is_american`        | bool       |
| `user_id`            | float64    |
| `recipe_id`          | float64    |
| `date`               | object     |
| `rating`             | float64    |
| `review`             | object     |
| `avg_rating_x`       | float64    |
| `avg_rating_y`       | float64    |


**Cleaned dataset**

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                                                    |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                    |   n_ingredients |   calories (#) |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) | is_asian   | is_american   |          user_id |   recipe_id | date       |   rating | review                                                                                                                                                                                                                                                                                                                                           |   avg_rating_x |   avg_rating_y |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|---------------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|:-----------|:--------------|-----------------:|------------:|:-----------|---------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------:|---------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'for-large-groups'", "'desserts'", "'lunch'", "'snacks'", "'cookies-and-brownies'", "'chocolate'", "'bar-cookies'", "'brownies'", "'number-of-servings'"] |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting']                                                  | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |          138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 | False      | False         | 386585           |      333281 | 2008-11-19 |        4 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  Even then, the brownies stuck to the foil, and were on the overly moist side and not easy to cut.  They did taste quite rich, though!  Made for My 3 Chefs.                                                                                   |              4 |              4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ["'60-minutes-or-less'", "'time-to-make'", "'cuisine'", "'preparation'", "'north-american'", "'for-large-groups'", "'canadian'", "'british-columbian'", "'number-of-servings'"]                                                                         |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder', 'set aside', 'in another mixing bowl , blend together the sugars , margarine , and salt until light and fluffy', 'add the eggs , water , and vanilla to the margarine / sugar mixture and mix together until well combined', 'add in the flour mixture to the wet ingredients and blend until combined', 'scrape down the sides of the bowl and add the chocolate chips', 'mix until combined', 'scrape down the sides to the bowl again', 'using an ice cream scoop , scoop evenly rounded balls of dough and place of cookie sheet about 1 - 2 inches apart to allow for spreading during baking', 'bake for 10 - 15 minutes or until golden brown on the outside and soft & chewy in the center', 'serve hot and enjoy !'] | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                                                                                                                                            | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 |          595.1 |                46 |           211 |             22 |              13 |                    51 |                    26 | False      | False         | 424680           |      453467 | 2012-01-26 |        5 | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide yard sale, & I made the whole batch & used them as enticements for potential buyers ~ what the hey, a free cookie as delicious as these are, definitely works its magic! Will be making these again, for sure! Thanks for posting the recipe! |              5 |              5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'side-dishes'", "'vegetables'", "'easy'", "'beginner-cook'", "'broccoli'"]                                                                                |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 | False      | False         |  29782           |      306168 | 2008-12-31 |        5 | This was one of the best broccoli casseroles that I have ever made.  I made my own chicken soup for this recipe. I was a bit worried about the tsp of soy sauce but it gave the casserole the best flavor. YUM!                                                                                                                                  |              5 |              5 |
|                                      |        |           |                  |             |                                                                                                                                                                                                                                                         |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |                |                   |               |                |                 |                       |                       |            |               |                  |             |            |          | The photos you took (shapeweaver) inspired me to make this recipe and it actually does look just like them when it comes out of the oven.                                                                                                                                                                                                        |                |                |
|                                      |        |           |                  |             |                                                                                                                                                                                                                                                         |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |                |                   |               |                |                 |                       |                       |            |               |                  |             |            |          | Thanks so much for sharing your recipe shapeweaver. It was wonderful!  Going into my family's favorite Zaar cookbook :)                                                                                                                                                                                                                          |                |                |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'side-dishes'", "'vegetables'", "'easy'", "'beginner-cook'", "'broccoli'"]                                                                                |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 | False      | False         |      1.19628e+06 |      306168 | 2009-04-13 |        5 | I made this for my son's first birthday party this weekend. Our guests INHALED it! Everyone kept saying how delicious it was. I was I could have gotten to try it.                                                                                                                                                                               |              5 |              5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'side-dishes'", "'vegetables'", "'easy'", "'beginner-cook'", "'broccoli'"]                                                                                |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 | False      | False         | 768828           |      306168 | 2013-08-02 |        5 | Loved this.  Be sure to completely thaw the broccoli.  I didn&#039;t and it didn&#039;t get done in time specified.  Just cooked it a little longer though and it was perfect.  Thanks Chef.                                                                                                                                                     |              5 |              5 |


## Assessment of Missingness




## Hypothesis Testing




## Framing a Prediction Problem




## Baseline Model




## Final Model




## Fairness Analysis




