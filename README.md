# Rain-Prediction
The project uses a modified version of the Kaggle competition data: Predict rain tomorrow in Australia (https://www.kaggle.com/jsphyg/weather-dataset-rattle-package). The data contains a number of meteorological observations as attributes, and the class attribute “RainTomorrow”. The goal of the project is to obtain a model that may be used to predict whether it is going to rain tomorrow for 10 locations in Australia.

# Summary Findings
- **Not all rows with NA values are removed** as missing values are common for weather data. The data is assumed to be **Missing Completely at Random**, thus only those variables with **more than 20% of data that are NA values** are removed.
- For the remaining variables, **Multiple Imputation by Chained Equation (MICE) method** is used to impute the missing data. Since MICE algorithm cannot deal with strings and characters, non-numeric variables are converted to factors prior implementing the algorithm.
- The imputation methods are different for the variables:
    - Numerical variables - predictive mean matching (pmm)
    - Unordered categorical variables, polytomous logistic regression (polyreg)
    - Binary variables, logistic regression with bootstrap (logreg.boot) is used.
- After exploratory data analysis, the dataset is found to be **imbalanced**. However, no modifications are made as it reflects the reality better.
- **Outliers** exists in the data, but it is not removed as extreme weathers, though are rare, are logical values.
- The variables "Temp9am", "Temp3pm", "MinTemp" and "MaxTemp" are highly correlated. Based on [Mean Decrease Impurity discussion] [http://blog.datadive.net/selecting-good-features-part-iii-random-forests/], only "Temp9am" is retained and the remaining three variables are removed.
- Similarly, "Pressure9am" and "Pressure3pm" are highly correlated, and only the former is retained.
- There is **no single best classifiers** for the problem as the lowest value in categories that desire higher value doesn't differ much from the highest value. This is also the case for categories that desire lower value.
- Out of the 5 proposed classifiers, **random forest** is chosen to be fine-tuned as it combines the strengths of the other models while overcoming their weaknesses. A $k$-fold cross validation is used to tune $mtry$. Grid search is chosen as it is more likely to find optimal hyperparameters and it's cost is negligible for a small dataset.
- The cross-validated random forest's performance **doesn't differ much** from the non-cross-validated random forest, but the cross-validated model is chosen as the best model as it provides a more reliable estimate of the model's performance.
- ANN **performs worse** than cross-validated random forest, possibly due to the amount of data is not sufficient to train the model.
- **Cost-sensitive learning approach** is attempted to account for class imbalance issues. However, the performance of cross-validated random forest considering class weights actually **performs worse** than cross-validated random forest without class weight consideration, suggesting that the original cross-validated random forest already account for the imbalance very well.
- The cross-validated random forest without class weight is chosen as the best model. The model is then fitted on the whole dataset excluding the 2000 data points used in training and testing the model. The model produces an out-of-sample accuracy of 86.55% and a F1-Score of 91.93%.
