Machine Learning for Student Housing Retention

This exploratory project uses a dataset from my university's residential life undergraduate survey. Due to data privacy reasons, the code outputs, survey questions, and responses are hidden.

Objective

To design a variety of machine learning models to determine what features are most influence a resident's decision to apply to return to residential housing or not. The goal is not to create a model to predict whether or not a student returns, since the data is collected at the same time as the outcome (whether the student returns or not). The objective is to discover what factors have a heavy influence on the response variable, and see if there is anything that Residential Life can do to improve their services and increase percentage of returning residents.

Dataset

The dataset comes from a survey taken by 2000+ undergraduate residents at this university. The models focus specifically on features regarding which building the resident lives in, type of living accommodation, Resident Assistant (RA) feedback, event feedback, and residential experience/satisfaction. The building and living accommodation columns were categorical, while the remaining variables are on a "strongly disagree" to "strongly agree" scale. As with all projects involving survey data, there may be a bias due to subjective survey responses.

Data Cleaning

Rows with NA values were dropped. Due to an error in the data collection, residents in certain buildings did not receive the "did you apply to return to university housing?" question, so those buildings and their residents are excluded from the models. Residents who responded that they were not applying to return because they were leaving the university were excluded, since we only care about residents attending the university for the next academic year. The building and living accommodation columns were converted to one-hot encoded variables. The remaining variables on a "strongly disagree" to "strongly agree" scale were first converted to a 1 to 5 scale, and then scaled down to be normally distributed so that they were on the same level as the one hot encoded variables. This also ensures that each feature's coefficients are interpetable. I conducted exploratory data analysis (EDA) including a correlation heatmap to discover any highly correlated variables. Using the rule of dropping a feature if r > |0.85|, no features were dropped. To prevent multicollinearity, baselines were identified for features created through the one-hot encoding process, by choosing the most typical or common feature (ex: the most selected building unit) The resulting odds ratios will be in comparison to the chosen baseline.

Models

I first fit a logistic regression model using all the features with the apply to return/not return variable as the response variable. I applied 5-fold cross validation to reduce variance and prevent overfitting, and then averaged the coefficients from the 5 folds. I interpreted the coefficient list as those with the highest coefficients on average had a positive effect on retention, while the negative coefficients on average had a negative effect on retention. 

Then I trained a decision tree model to see if any interesting rule-based patterns emerged( e.g., “if condition X & Y hold, Z% of residents applied to return”). I limited the max depth of the tree to 3 so that the resulting rules were not too complex. Unfortunately, many of the rules contained too small a subset to make any conclusions, and those with a large enough subset had rules that were not logically interpretable.

Afterward I built both a random forest model and a gradient boosted tree model (XGBoost). These models act somewhat similarly, but since XGBoost is supposedly more applicable in industry, I decided to do both models. Both models returned a feature importance score for each variable, and surprisingly returned very different results. The random forest model seemed to give features on the "strongly disagree" to "strongly agree" scale more weight, while the XGBoost model viewed the one-hot encoded variables as the most important. 

Building Upon Models

After viewing the results, I decided to only proceed with the logistic regression model, since it is most easily interpretable for non-technical audiences compared to the decision trees and random forests models. In addition to the cross-validated logistic regression model, I also fit two complementary versions of a logistic regression using maximum likelihood estimation through statsmodels: 

An unscaled logistic regression model, where the original 1–5 survey responses were kept intact. This allows for direct interpretation of coefficients and odds ratios on a per-point basis (e.g., “a one-point increase in X increases odds of returning by Y%”).

A scaled logistic regression model, where the survey variables were standardized to mean 0 and unit variance. This makes coefficients comparable in magnitude across question groups (RA feedback, events, residential experience, etc.).

These two models serve different goals: the unscaled model provides direct interpretability, while the scaled model supports comparing the strength of different feature categories.

Analysis

I organized all predictors into six thematic groups, reflecting major aspects of students’ residential experiences:
RA Feedback, Events, Residential Experience, Theme Program, Unit, Living Accommodation

Using the scaled model, I computed the average absolute coefficient within each group to determine which themes had the strongest overall influence. Living accommodation had by far the largest effect magnitude, followed by building. Other features (RA feedback, events, and residential experience) had smaller effects in comparison. The table is shown below:

| Group                   | Mean Absolute Coefficient |
|------------------------|---------------------------|
| Living Accommodation   | 1.012                     |
| Unit                   | 0.489                     |
| Theme Program          | 0.375                     |
| Residential Experience | 0.141                     |
| Events                 | 0.119                     |
| RA Feedback            | 0.109                     |


Using the unscaled model, I generated coefficients, odds ratios, and p-values for all features to quantify two types of features: how a 1-point increase in each survey item affected the likelihood of returning or how a selection of a certain one-hot encoded variable compares to the baseline in terms of likelihood of returning . This analysis revealed which individual factors mattered most within each thematic category, and while not all tables can be shown due to privacy reasons, two of the tables (some tables are shortened and names changed) are shown below:

Residential Experience:
| Feature | Coefficient | Odds Ratio | p-value |
|---------|-------------|------------|---------|
| My residential experience helps me explore different aspects of myself* | 0.413 | 1.511 | 0.001 |
| I am satisfied with my overall experience living in university housing | 0.128 | 1.136 | 0.267 |
| My residential experience helps transition to social community | 0.061 | 1.063 | 0.600 |
| My residential experience helps develop healthy wellness practices | -0.011 | 0.989 | 0.936 |

example interpretations: A one point increase in being satisfied with overall experience (ex: agree ---> strongly agree) leads to an average of a 13.6% increase in retention. A one point increase in agreeing that residential experience helps develop healthy wellness practices leads to an average of a 1.1% decrease in retention. 

Unit (names changed for privacy reasons):
| Unit | Coefficient | Odds Ratio | p-value |
|------|-------------|------------|---------|
| Placeholder Commons* | 1.160 | 3.190 | 0.002 |
| Unit A | 0.028 | 1.028 | 0.922 |
| Unit C** | 0 | 1.0 | NA |
| Unit B | -0.081 | 0.922 | 0.750 |
| Unit D | -0.445 | 0.641 | 0.075 |
| Placeholder of Berkeley | -0.733 | 0.481 | 0.251 |

example interpretations: Living in Unit A leads to, on average, a 2.8% increase in retention compared to residents of Unit C. Living in Placeholder of Berkeley leads to, on average, a 51.9% decrease in retention compared to residents of Unit C.

(* indicates a statistically significant feature)
(** indicates a baseline to prevent multicollinearity)

Conclusion

Because the dataset contains somewhat correlated survey variables and the sample size is small relative to the number of features, p-values are not the primary focus of inference (however still important). Instead, this analysis emphasizes effect direction and magnitude. This approach provides more actionable insights for Residential Life—such as identifying which building types or room configurations correspond with significantly higher retention rates, or what qualities in RAs or types of events are most important to retention.

This exploratory project gave me experience with different machine learning models, and some useful insights were derived from the results. For the future, ensuring residents have access to all of the questions will allow for a larger, more representative dataset and more accurate models. Retaining the same questions in future surveys will allow for comparisons between years and an analysis on if the important features stay consistent.
