Machine Learning for Student Housing Retention

This exploratory project uses a dataset from my university's residential life undergraduate survey. Due to data privacy reasons, the outputs of the code are hidden, as well as the questions and survey answers. 

Objective
Design a variety of machine learning models to determine what features are most important to influencing a resident's decision to apply to return to residential housing or not. We are not worried about creating a model to predict whether or not a student returns, since the data is collected at the same time as the outcome (whether the student returns or not). The objective is to discover what factors have a heavy influence on the response variable, and see if there is anything that Residential Life can do to improve their services and increase percentage of returning residents.

Dataset
The dataset comes from a survey taken by 2000+ undergraduate residents at this university. The models focus specifically on features regarding which building the resident lives in, type of living accomodation, Resident Assistant (RA) feedback, event feedback, and residential experience/satisfaction. The building and living accomodation columns were categorical. The remaining variables are on a "strongly disagree" to "strongly agree" scale.

Data Cleaning
Rows with NA values were dropped. Due to an error in the data collection, residents in certain buildings did not receive the "did you apply to return to university housing?" question, and as a result those buildings and their residents are excluded from the models. Residents who responded that they were not applying to return because they were leaving the university were excluded, since we only care about residents attending the university for the next academic year. The building and living accomodation columns were converted to one-hot encoded variables. The remaining variables on a "strongly disagree" to "strongly agree" scale were first converted to a 1 to 5 scale, and then scaled down to be normally distributed so that they were on the same level as the one hot encoded variables. This also ensures that each features' coefficients are interpetable.

Models
I first fit a logistic regression model using all the features with the apply to return/not return variable as the response variable. I used k-fold cross validation with 5 folds to prevent overfitting and averaged the coefficients from the 5 folds. I interpreted the coefficient list as those with the highest coefficients on average had a positive effect on retention, while the negative coefficients on average had a negative effect on retention. 
I next built a decision tree model to see if any interesting rule-based patterns emerged( e.g., “if condition X & Y hold, Z% of residents applied to return”). I limited the max depth of the tree to 3 so that the resulting rules were not too complex. Unfortunately, many of the rules contained too small a subset to make any conclusions, and those with a large enough subset had rules that did not make much sense logically.
I next built both a random forest model and a gradient boosted tree model (XGBoost). These models act somewhat similarly, but since XGBoost is supposedly more applicable in industry, I decided to do both models. Both models returned a feature importance score for each variable, and surprisingly returned very different results. The random forest model seemed to give features on the "strongly disagree" to "strongly agree" scale more weight, while the XGBoost model viewed the one-hot encoded variables as the most important.
Much of what I've described is very vague, but due to data privacy reasons, I cannot share any of the results of the model.

Conclusion
This exploratory project gave me experience in working with different machine learning models, and some useful insights were derived from the results. For the future, ensuring residents have access to all of the questions will allow for a larger dataset and a more accurate model. Retaining the same questions in future surveys will allow for comparisons between years and an analysis on if the important features stay consistent.
