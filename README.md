**Authors: Joey Kaminsky and Ishaan Chadha**

---
# Problem Identification

From the Recipes and Ratings dataframes, we decided to investigate the following prediction problem: is there a way to predict the rating of a recipe given by a reviewer based on the other columns in the merged dataframe? In order to predict the response variable that is rating--which is an integer that is 1, 2, 3, 4, or 5--we chose to utilize a classificaton model in the form of a RandomForestClassifier with multiclass classification. We chose to try to predict rating in order to gain some insight as to what influences a rating, from the aspects of the recipe to the reviewer. In order to evaluate the model, we will utilize the metric accuracy  overall to see how the model preforms overall. We used this over others as there is no instrinsically worse error to commit (false negative and false positive are both equally bad), which means precision and recall are not suitable; we also want to make it such that the model makes as many accurate predictions as possible, which accuracy measures. Furthermore, since we do not particular care about precision and recall as much as we care about overall success of the model, F1-score is not all that suitable either, which lead us to choose accuracy as our evaluation metric.

At the time of prediction of the rating of a particular recipe and reviewer, we have access to the particular information of the recipe; for the recipe aspect, we can utilize the recipe id, description (both of the overall recipe, steps, and ingredients), time to make, number of steps, and number of ingredients of the recipe. As for the review, we can use the reviewer id and their review description. All these predictor variables will help us to predict the rating given by the reviewer for that particular recipe.

---
# Baseline Model

In our Baseline Model, we try to predict the ratings that different recipes on [food.com](https://www.food.com) are given.

In our model, we use a pipeline that consists of a preprocessing step followed by a decision tree classifier. The preprocessing step involves transforming the features before feeding them into the decision tree classifier. The preprocessor consists of a ColumnTransformer which applies different features to different columns of the dataset. We Standard Scale the `calories (#)` column and One-Hot-Encode the `n_ingredients_cat` column. It is a categorical feature derived from the `n_ingredients` feature. It categorizes the number of ingredients into three categories: 0 for less than 7 ingredients, 1 for 7-10 ingredients, and 2 for more than 10 ingredients. The rest of the columns in the dataset are dropped and thus aren't factored in while predicting the `rating` column of the dataset.

**Features:**

**1. Quantitative features:** `calories (#)` (one feature). It is a quantitative feature representing the number of calories in a recipe. It is standardized (scaled) using the StandardScaler.

**2. Ordinal feature:** `n_ingredients_cat` (one feature). It is a categorical feature derived from the `n_ingredients` feature. It categorizes the number of ingredients into three categories: 0 for less than 7 ingredients, 1 for 7-10 ingredients, and 2 for more than 10 ingredients. It is one-hot encoded using the OneHotEncoder.

The model is split randomly into a training model (0.8) and test model (0.2) to make sure that we don't overfit our model.

The performance of the model is evaluated using accuracy, which is calculated by the `score` method of the pipeline on the test set. The accuracy represents the proportion of correctly predicted ratings out of all the predictions made. We get an accuracy of approximately 74% when we run this model on our test dataset.

We believe that our model isn't up to the mark because it is highly biased towards predicting '5' as the rating of the recipes. Our model over predicts '5's and underpredicts all the other ratings. We believe that adding more features to our model that help distinguish between the rating of a recipe as a '5' and all the other ratings would greatly help our model predict other ratings too, and possibly increase the accuracy of our model.

---
