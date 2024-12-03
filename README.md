# **Investigation on the Relationship Between the Number of Ingredients and Asian Recipes**
UC San Diego DSC 80 Final Project

Author: Chanyoung Park


## **Introduction**

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


## **Data Cleaning and Exploratory Data Analysis**

### **Data Cleaning**

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

The cleaned merged dataset has 234429 rows and 27 columns. The ‘dtypes’ and first 5 rows of the cleaned dataset are shown below. For ease of displaying the dataset, all columns except ‘steps’ and ‘review’ will be displayed:

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
|avg_rating            | float64    |

**Cleaned dataset**

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                                                    |   n_steps | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                    |   n_ingredients |   calories (#) |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) | is_asian   | is_american   |          user_id |   recipe_id | date       |   rating |   avg_rating |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|---------------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|:-----------|:--------------|-----------------:|------------:|:-----------|---------:|-------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'for-large-groups'", "'desserts'", "'lunch'", "'snacks'", "'cookies-and-brownies'", "'chocolate'", "'bar-cookies'", "'brownies'", "'number-of-servings'"] |        10 | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |          138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 | False      | False         | 386585           |      333281 | 2008-11-19 |        4 |            4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ["'60-minutes-or-less'", "'time-to-make'", "'cuisine'", "'preparation'", "'north-american'", "'for-large-groups'", "'canadian'", "'british-columbian'", "'number-of-servings'"]                                                                         |        12 | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                                                                                                                                            | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 |          595.1 |                46 |           211 |             22 |              13 |                    51 |                    26 | False      | False         | 424680           |      453467 | 2012-01-26 |        5 |            5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'side-dishes'", "'vegetables'", "'easy'", "'beginner-cook'", "'broccoli'"]                                                                                |         6 | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 | False      | False         |  29782           |      306168 | 2008-12-31 |        5 |            5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'side-dishes'", "'vegetables'", "'easy'", "'beginner-cook'", "'broccoli'"]                                                                                |         6 | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 | False      | False         |      1.19628e+06 |      306168 | 2009-04-13 |        5 |            5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ["'60-minutes-or-less'", "'time-to-make'", "'course'", "'main-ingredient'", "'preparation'", "'side-dishes'", "'vegetables'", "'easy'", "'beginner-cook'", "'broccoli'"]                                                                                |         6 | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 | False      | False         | 768828           |      306168 | 2013-08-02 |        5 |            5 |

### **Univariate Analysis**

The plots below show the distribution of 'n_ingredients' in the merged dataset filtered by Asian recipes and the distribution of 'n_ingredients' in the merged dataset filtered by American recipes. The distribution of Asian recipes is more uniform and has a higher center compared to that of the distribution of American recipes which looks more skewed to the right.

<iframe
  src="assets/univariate_asian_n_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/univariate_american_n_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### **Bivariate Analysis**

The plot below shows a grouped bar plot of ‘n_ingedients’ means per rating in the merged dataset by Asian and American recipes. It looks like the average ‘n_ingedients’ in each rating category (1-5) for Asian recipes are all greater than that of the average ‘n_ingedients’ in each rating category for American recipes. This will be looked further into in the hypothesis testing section of the project.

<iframe
  src="assets/bivariate_mean_n_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### **Interesting Aggregates**

The pivot table shown below shows the mean ratings of recipes that fall into each category of ‘n_ingedients’ and ‘n_steps’ from 1 to 20 for both categories. The pivot table can be used to visualize how ‘n_ingedients’ and ‘n_steps’ has an effect on ratings of recipes. At first glance, it looks like simpler recipes with less ingredients and steps have higher ratings but on a closer look, the results of simpler and more complex recipes don’t seem to have a consistent trend. The majority of cells have average ratings between 4.5 and 5.


|   n_steps (down) | n_ingredients (right) |         1 |       2 |       3 |       4 |       5 |       6 |       7 |       8 |       9 |      10 |      11 |      12 |      13 |      14 |        15 |      16 |        17 |        18 |        19 |        20 |
|-----------------------------------------:|----------:|--------:|--------:|--------:|--------:|--------:|--------:|--------:|--------:|--------:|--------:|--------:|--------:|--------:|----------:|--------:|----------:|----------:|----------:|----------:|
|                                        1 | nan       | 4.69481 | 4.79063 | 4.71509 | 4.76071 | 4.7298  | 4.63761 | 4.60736 | 4.74242 | 4.70435 | 4.76119 | 4.60714 | 4.1     | 4.52941 | nan       | 4.6     |   4.85714 | nan       | nan       | nan       |
|                                        2 |   5       | 4.71605 | 4.7425  | 4.75116 | 4.73818 | 4.68905 | 4.71169 | 4.73163 | 4.71212 | 4.70722 | 4.74528 | 4.57143 | 4.85106 | 4.64    |   4.1875  | 4.45455 | nan       | nan       | nan       |   3       |
|                                        3 |   5       | 4.7411  | 4.67467 | 4.7208  | 4.72533 | 4.7032  | 4.69628 | 4.66798 | 4.7067  | 4.75112 | 4.75186 | 4.74153 | 4.71505 | 4.47761 |   4.81013 | 4.84    |   4.9     |   4.6     |   5       |   4.86667 |
|                                        4 |   4.66667 | 4.78715 | 4.72622 | 4.71084 | 4.74371 | 4.6956  | 4.67366 | 4.68021 | 4.70981 | 4.63636 | 4.67847 | 4.6802  | 4.78019 | 4.70482 |   4.68493 | 4.71429 |   4.625   |   4.8125  |   4.94118 |   4.4375  |
|                                        5 |   4.25    | 4.69295 | 4.71199 | 4.69658 | 4.686   | 4.67106 | 4.64411 | 4.67489 | 4.60464 | 4.61456 | 4.64235 | 4.66562 | 4.67877 | 4.52683 |   4.64804 | 4.63415 |   4.75    |   4.86441 |   4.77778 |   4.9     |
|                                        6 | nan       | 4.82581 | 4.73344 | 4.67742 | 4.66067 | 4.70005 | 4.66212 | 4.65823 | 4.62383 | 4.65816 | 4.67276 | 4.65718 | 4.73189 | 4.67624 |   4.66245 | 4.59483 |   4.78689 |   4.48649 |   4.72    |   4.7619  |
|                                        7 |   4.95    | 4.76106 | 4.75728 | 4.71839 | 4.71042 | 4.68905 | 4.67842 | 4.64894 | 4.66033 | 4.63922 | 4.69657 | 4.68633 | 4.71429 | 4.69683 |   4.66222 | 4.73099 |   4.64    |   4.66667 |   4.8125  |   4.71429 |
|                                        8 | nan       | 4.7377  | 4.76044 | 4.66084 | 4.68699 | 4.65213 | 4.69651 | 4.66034 | 4.65895 | 4.65163 | 4.69258 | 4.6715  | 4.65521 | 4.66603 |   4.69355 | 4.65018 |   4.64    |   4.78652 |   4.44444 |   4.75    |
|                                        9 |   5       | 4.7551  | 4.71474 | 4.63707 | 4.69862 | 4.6813  | 4.68121 | 4.67732 | 4.67706 | 4.63745 | 4.62669 | 4.65061 | 4.70358 | 4.68009 |   4.62363 | 4.5     |   4.70861 |   4.63158 |   4.73077 |   4.57143 |
|                                       10 |   5       | 4.48837 | 4.75    | 4.69658 | 4.70636 | 4.63355 | 4.61769 | 4.6004  | 4.68593 | 4.65432 | 4.66366 | 4.65882 | 4.62788 | 4.68594 |   4.68227 | 4.68326 |   4.65333 |   4.73077 |   4.5625  |   4.90625 |
|                                       11 |   4.83333 | 4.8     | 4.60938 | 4.69453 | 4.72    | 4.70785 | 4.64064 | 4.69745 | 4.58997 | 4.68724 | 4.69954 | 4.68398 | 4.67853 | 4.64563 |   4.62011 | 4.68367 |   4.69231 |   4.75    |   4.78333 |   4.78378 |
|                                       12 |   4.66667 | 4.61111 | 4.77778 | 4.62667 | 4.69965 | 4.64899 | 4.67249 | 4.64239 | 4.66719 | 4.64296 | 4.7084  | 4.68144 | 4.65617 | 4.65794 |   4.7071  | 4.61066 |   4.68852 |   4.75893 |   4.7625  |   4.36364 |
|                                       13 |   5       | 4.72917 | 4.79231 | 4.6     | 4.79087 | 4.70863 | 4.65912 | 4.6881  | 4.66232 | 4.66867 | 4.66667 | 4.71011 | 4.7134  | 4.67255 |   4.72043 | 4.69853 |   4.54706 |   4.74468 |   4.875   |   4.81818 |
|                                       14 | nan       | 4.96    | 4.5873  | 4.69767 | 4.71505 | 4.65689 | 4.58713 | 4.6897  | 4.68986 | 4.66777 | 4.66025 | 4.68596 | 4.69122 | 4.6219  |   4.61893 | 4.66403 |   4.74419 |   4.77778 |   4.77778 |   4.86154 |
|                                       15 | nan       | 4.66667 | 4.65714 | 4.51562 | 4.65885 | 4.63736 | 4.68341 | 4.58889 | 4.69577 | 4.66244 | 4.67488 | 4.73913 | 4.68116 | 4.6285  |   4.72107 | 4.62661 |   4.76316 |   4.8381  |   4.6699  |   4.46875 |
|                                       16 | nan       | 4.74194 | 4.81579 | 4.84889 | 4.76279 | 4.61751 | 4.64828 | 4.66978 | 4.69194 | 4.72897 | 4.68336 | 4.63371 | 4.66744 | 4.70133 |   4.63456 | 4.77289 |   4.66667 |   4.69136 |   4.72321 |   4.825   |
|                                       17 | nan       | 4.70833 | 5       | 4.63063 | 4.8664  | 4.66667 | 4.65563 | 4.64368 | 4.75941 | 4.75121 | 4.68345 | 4.60929 | 4.66927 | 4.60784 |   4.69231 | 4.71547 |   4.75568 |   4.69512 |   4.4     |   4.73077 |
|                                       18 | nan       | 5       | 4.66667 | 4.60656 | 4.8042  | 4.63448 | 4.71176 | 4.69198 | 4.67398 | 4.61625 | 4.65957 | 4.67529 | 4.71661 | 4.65368 |   4.61502 | 4.59542 |   4.55046 |   4.80952 |   4.53731 |   4.74603 |
|                                       19 | nan       | 5       | 5       | 4.55556 | 4.41379 | 4.38    | 4.61875 | 4.67873 | 4.77509 | 4.67606 | 4.68731 | 4.67687 | 4.75741 | 4.70445 |   4.66434 | 4.64024 |   4.71845 |   4.63636 |   4.72549 |   4.76471 |
|                                       20 | nan       | 4.71429 | 5       | 4.74576 | 4.63889 | 4.78082 | 4.6519  | 4.62326 | 4.63948 | 4.71671 | 4.84091 | 4.66415 | 4.69258 | 4.6713  |   4.71519 | 4.7485  |   4.78431 |   4.82353 |   4.75    |   4.66667 |


## **Assessment of Missingness**




## **Hypothesis Testing**




## **Framing a Prediction Problem**




## **Baseline Model**




## **Final Model**




## **Fairness Analysis**




