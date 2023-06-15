**Authors: Joey Kaminsky and Ishaan Chadha**

___
# Exploratory Data Analysis

Our Exploratory Data Analysis on the recipes and ratings datasets can be found **[here](https://jkaminsky2.github.io/Analysis-of-food.com-Recipes/)**.

---
# Framing the Problem

From the Recipes and Ratings dataframes, we decided to investigate the following prediction problem: is there a way to predict the rating of a recipe given by a reviewer based on the other columns in the merged dataframe? In order to predict the response variable that is rating--which is an integer that is 1, 2, 3, 4, or 5--we chose to utilize a classificaton model in the form of a DecisionTreeClassifier with multiclass classification. We chose to try to predict rating in order to gain some insight as to what influences a rating, from the aspects of the recipe to the reviewer. In order to evaluate the model, we will utilize the metric accuracy  overall to see how the model preforms overall and accuracy for each of the response variable's values to see any bias in predictions made. We used this over others as there is no instrinsically worse error to commit (false negative and false positive are both equally bad), which means precision and recall are not suitable; we also want to make it such that the model makes as many accurate predictions as possible, which accuracy measures. Furthermore, since we do not particular care about precision and recall as much as we care about overall success of the model, F1-score is not all that suitable either, which lead us to choose accuracy as our evaluation metric. And we will also look into the model's accuracy for each of the response variabale's values to better see if our model is biased towards guessing any ratings over others.

At the time of prediction of the rating of a particular recipe and reviewer, we have access to the particular information of the recipe; for the recipe aspect, we can utilize the recipe id, description (both of the overall recipe, steps, and ingredients), time to make, number of steps, and number of ingredients of the recipe. As for the review, we can use the reviewer id and their review description. All these predictor variables will help us to predict the rating given by the reviewer for that particular recipe.

---
# Baseline Model

In our Baseline Model, we try to predict the ratings that different recipes on [food.com](https://www.food.com) are given.

In our model, we use a pipeline that consists of a preprocessing step followed by a decision tree classifier. The preprocessing step involves transforming the features before feeding them into the decision tree classifier. The preprocessor consists of a ColumnTransformer which applies different features to different columns of the dataset. We Standard Scale the `calories (#)` column and Function Transform the `n_ingredients` column. Since the `n_ingredinets` column is an ordinal column, we can't One-Hot-Encode it. Thus, we use a FunctionTransformer to ordinally transform it. Our FunctionTransformer categorizes the number of ingredients into three categories: 0 for less than 7 ingredients, 1 for 7-10 ingredients, and 2 for more than 10 ingredients. The rest of the columns in the dataset are dropped and thus aren't factored in while predicting the `rating` column of the dataset.

**Features:**

**1. Quantitative features:** `calories (#)` (one feature). It is a quantitative feature representing the number of calories in a recipe. It is standardized (scaled) using the StandardScaler.

**2. Ordinal feature:** `n_ingredients` (one feature). It categorizes the number of ingredients into three categories: 0 for less than 7 ingredients, 1 for 7-10 ingredients, and 2 for more than 10 ingredients. It is ordinally transformed using the FunctionTransformer.

The model is split randomly into a training model (0.8) and test model (0.2) to make sure that we don't overfit our model.

The performance of the model is evaluated using accuracy, which is calculated by the `score` method of the pipeline on the test set. The accuracy represents the proportion of correctly predicted ratings out of all the predictions made. We get an accuracy of approximately 74% when we run this model on our test dataset.

We believe that our model isn't up to the mark because it is highly biased towards predicting '5' as the rating of the recipes. Our model over predicts '5's and underpredicts all the other ratings (due to the fact that a 5 rating is so common in the dataset). We believe that adding more features to our model that help distinguish between the rating of a recipe as a '5' and all the other ratings would greatly help our model predict other ratings and increase the accuracy of our model.

Additionally, we need to improve upon the accuracy of the overall model. A good baseline to compare our model's accuracy to is the rate of 5's in the dataset due to it being so common; our baseline model has an accuracy of 74%, whereas the number of 5's in the testing dataset is roughly 77%. Therefore, our model is not good right now, but we can greatly improve on the accuracy (and thus the overall effectivenss) of our model by adding more valuable predictor variables.

---
# Final Model

The features we added to the model are:

**1. 'Nutrition Data':** We used a StandardScalar to standardize the nutritional data such as 'calories', 'total fat', 'sodium', and 'sugar' to then use to predict the rating of the recipe. We believe these are important because reviewers who care about nutrition might give recipes with high calories or high fat a lower rating.

**2. 'n_steps':** We used a Binarizer to convert this feature into a binary variable based on a threshold of 8. This feature represents the number of steps required to prepare the recipe. By converting it into a binary variable, the model can capture whether a recipe has a relatively small number of steps or a larger number of steps, which may affect the cooking time or complexity of the recipe. We feel that the number of steps matter because a recipe with too many steps might cause the reviewers to get exhausted and give a bad review.

**3. 'minutes':** We used a Binarizer to convert this feature into a binary variable based on a threshold of 100. This feature represents the total cooking time in minutes. By converting it into a binary variable, the model can differentiate between recipes with shorter cooking times and those with longer cooking times. This can be useful in capturing potential correlations between cooking time and the target variable. We feel that the cooking time matters because a recipe that takes too long to make might cause the reviewers to get exhausted and give a bad review.

**4. 'n_ingredients':** We applied a custom transformation using a FunctionTransformer. This feature represents the number of ingredients required for the recipe. By transforming this feature, I can extract additional information, such as whether a recipe has a small number of ingredients or a larger number of ingredients. This can be relevant as recipes with a higher number of ingredients might require more effort or be more complex.

**5. Categorical Features:** We used OneHotEncoder to encode the categorical variables that were in the tags column of the dataset. We found out that there are certain tags which have a high correlation with the ratings column and added these tags as columns to our dataframe with '0' representing that this tag wasn't present and '1' representing that the tag is present. By one-hot encoding these variables, the model can capture the relationships between different tags and their impact on the rating of the recipe.

The modeling algorithm we chose for this task is the Decision Tree Classifier. Decision trees are suitable for both classification tasks and handling a mix of continuous and categorical features. They can capture complex interactions and non-linear relationships in the data. 

To select the best hyperparameters, we used the GridSearchCV function, which performs an exhaustive search over the specified parameter values using cross-validation. The model's performance was evaluated based on accuracy, which measures the proportion of correct predictions. The hyperparameters that were tuned using grid search are:

1. `max_depth`: This parameter determines the maximum depth of the decision tree. It controls the complexity of the model and helps prevent overfitting. The values tested are 5, 10, 15, 30. We saw which values of `max_depth` were actually being implemented in the best fit of the model and dependent on that chose the values of 5, 10, 15, 20.

2. `min_samples_split`: This parameter specifies the minimum number of samples required to split an internal node. It affects the decision tree's ability to capture fine-grained patterns in the data. The values tested are 100, 200, 250, 275, 300. We saw which values of `min_samples_split` were actually being implemented in the best fit of the model and dependent on that chose the values of 100, 200, 250, 275, 300.

3. `criterion`: This parameter defines the quality measure used to evaluate the splits in the decision tree. The two options tested are 'gini' and 'entropy'. 'Gini' measures the impurity of a node, while 'entropy' calculates the information gain.

The hyperparameters that gave the best results were:
* `criterion`: 'gini'
* `max_depth`: 10
* `min_samples_split`: 275

Compared to the baseline model, the final model includes additional features and uses a more advanced modeling algorithm. By incorporating information about the number of steps, cooking time, and number of ingredients, as well as one-hot encoding the categorical features, the model can capture more nuanced patterns in the data. Additionally, the decision tree classifier can handle non-linear relationships and interactions between features. The grid search helped in finding the best hyperparameters, resulting in a model that is fine-tuned for the data. Overall, the final model's performance shows a significant improvement over the baseline model due to the inclusion of these additional features and the use of a more sophisticated algorithm. Our final model also predicts more of the other ratings ('1', '2', '3', '4') rather than mostly predicting the rating of '5'. This is important because if we mostly guess '5' then our prediction modeis left redundant.

---

# Fairness Analysis


We decided to look into whether our model preform differently based on the number of minutes of a recipe. To further investigate this, we set up the following permuation test:


Group 1: Recipes that take 35 minutes or more to make

Group 2: Recipes that take less than 35 minutes to make

We chose 35 minutes as the divider between the two groups as this is the value that most evenly splits up the dataset into the groups (roughly 50% of the dataframe to_work is in each group).

- Null Hypothesis: Our model is fair. Its accuracy for recipes that take less than 35 minutes and 35 minutes or more are roughly the same, and any differences are due to random chance.

- Alternative Hypothesis: Our model is unfair. Its accuracy for recipes that take less than 35 minutes is higher than its accuracy for recipes that take 35 minutes or more.

Evaluation Metric: Accuracy

Test Statistic: (Accuracy of recipes that take less than 35 minutes) - (Accuracy of recipes that take 35 minutes or more)

Significance Level: alpha = 0.05

<iframe src="assets/perm_test_recipes3.html" width=800 height=600 frameBorder=0></iframe>
p-value = 0.00

Because our p-value = 0.00 is less than the significance level of alpha = 0.05, we can reject the null hypothesis. There is statistically significant evidence that our model could be unfair, specifically that its accuracy for recipes that take less than 35 minutes could be higher than its accuracy for recipes that take 35 minutes or more. It should be noted that due to not having randomized controlled trials, we cannot make definitive conclusions about the truth or falsehood of either hypothesis.
