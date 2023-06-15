**Authors: Joey Kaminsky and Ishaan Chadha**

---
# Problem Identification

From the Recipes and Ratings dataframes, we decided to investigate the following prediction problem: is there a way to predict the rating of a recipe given by a reviewer based on the other columns in the merged dataframe? In order to predict the response variable that is rating--which is an integer that is 1, 2, 3, 4, or 5--we chose to utilize a classificaton model in the form of a RandomForestClassifier with multiclass classification. We chose to try to predict rating in order to gain some insight as to what influences a rating, from the aspects of the recipe to the reviewer. In order to evaluate the model, we will utilize the metric accuracy, both overall and by the five different response values; this is done to see how the model preforms overall and if there is any bias in correct and incorrect prediction accuracies by each response variable value. We use these over others as there is no instrinsically worse error to commit (false negative and false positive are both equally bad), which means precision and recall are not suitable; we also want to make it such that the model makes as many accurate predictions as possible, which accuracy measures. Furthermore, since we do not particular care about precision and recall as much as we care about overall success of the model, F1-score is not all that suitable either, which lead us to choose accuracy as our evaluation metric. And we use the accuracy for each predictor variable value to gain some insight into whether or not our model has bias, and, if so, what bias is occurring.

At the time of prediction of the rating of a particular recipe and reviewer, we have access to the particular information of the recipe; for the recipe aspect, we can utilize the recipe id, description (both of the overall recipe, steps, and ingredients), time to make, number of steps, and number of ingredients of the recipe. As for the review, we can use the reviewer id and their review description. All these predictor variables will help us to predict the rating given by the reviewer for that particular recipe.

---
# Baseline Model
