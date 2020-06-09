---
layout: post
title:      "Classification of Changes in Jail Incarceration Rates "
date:       2020-06-09 17:14:57 -0400
permalink:  classification_of_changes_in_jail_incarceration_rates
---


For my capstone project at the Flatiron School's Immersive Data Science Program, I decided to use classification modeling to predict *changes in jail incarceration rates at the county level*, using 10 years of incarceration data and other county-level data on population, employment, income, and more. 

I initially set out with several lofty, overly ambitious ideas based on a well-known hypothesis that economic opportunities (read *inequality*) have some culpability in increased criminality, to say the least.  After exploring the availability of incarceration-related data, specifically trying to assess the level of granularity possible, I took a pause to ask myself a few questions regarding my overall motivations and ethical considerations moving forward.  I realized that approaching these questions at the individual level, be that adult citizen, inmate, parolee, etc. made me deeply uncomfortable, even if I had the intention of setting out to prove that criminal behaviors and incarceration had more to do with external circumstances, even policy, than it had to do with the individual.  There is plenty to contend with when it comes to perpetuating bias in AI and data science focused on social issues, and, while still being new to the field, I needed to acknowledge the limits of my knowledge and experience in dealing with these issues.   

Moreover, my original intention was to understand if structural conditions and socio-economic environment could be elevated in the discussion around reducing crime and reversing the U.S.'s troublesome incarceration trends.  Given this, I decided to find a unit of analysis that allowed me to bring in data on incarceration *and* the socio-economic ecosystem, which could potentially be used as a way to support interventions at the local county level.  

## Why this matters?
Apart from why this project matters to me, I'll step back to provide a high level, and admittedly depersonalized picture of incarceration in the U.S..  Approximately 0.88% of the total U.S. adult population is currently incarcerated in federal or state prisons or local county jails.  For additional scale, 1 out of 5 prisoners in the world is incarcerated in the U.S, meaning that even though the U.S. accounts for less than 5% of the world's population, it holds 20% of the world's incarcerated people.  

While research on incarceration tends to focus on state-level data, this county-level analysis allows for a deeper understanding of within-state nuances and hyperlocal differentiation of incarceration dynamics. This is especially important because state laws on criminal penalties are interpreted and implemented by decision makers at the local city or county level, including local law enforcement, prosecutors, and judges. Moreover, criminal activities do not happen in a vacuum, and the local ecosystem, including local effects of broader economic and social conditions, could provide additional insight to trends, challenges, and opportunities for improvement (read *mitigation*) to incarceration trends and the criminal justice system.

## Methodology
Now that the context and motiviation are set in place, I'll turn my attention to the approach for the analysis.  I used the OSEMiN framework - Obtain, Scrub, Explore, Model, and Interpret.  I'll briefly mention the main actions in each of these steps, before summarizing the key findings and their potential implications, as well as possibilities for additional analyses. 

### Obtain - Data Sources
The data I used in this project is thanks to the massive efforts made by the Vera Institute of Justice, compiling the first-ever county-level database on incarceration trends, and the USDA's Economic Research Service (ERS).  Here's more information on these excellent sources: 

**Incarceration Rates Data:**
* [Vera Institute of Justice database of county and jurisdiction level incarceration rates](https://github.com/vera-institute/incarceration_trends) from 1970 to 2017: 
* The Database is comprised of the following Bureau of Justice Statistics (BJS) data collections: the Census of Jails (COJ), which covers all jails and is conducted every five to eight years since 1970, and the Annual Survey of Jails (ASJ), which covers about one-third of jails-and includes nearly all of the largest jails-that has been conducted in non-census years since 1982, and the BJS National Corrections Reporting Program (NCRP) data collection. Vera merged this data to produce a first-in-kind national dataset that can examine both jail and prison incarceration at the county level.
* "[Incarceration Trends is supported by Google.org, the John D. and Catherine T. MacArthur Foundation Safety and Justice Challenge, and the Robert W. Wilson Charitable Trust](http://trends.vera.org/incarceration-rates?data=pretrial)." 

**County-level data on Population, Jobs, Education:**
* The United States Department of Agriculture's (USDA) [Economic Research Service (ERS)](https://catalog.data.gov/dataset/county-level-data-sets): 
* The USDA's [Atlas of Rural and Small Town America](https://www.ers.usda.gov/data-products/atlas-of-rural-and-small-town-america/download-the-data/):
* "Data are grouped by topic and reported in four tabs within the spreadsheet: People, Jobs, Income, Veterans, and County Classifications. Each tab includes the County FIPS Code as the first column. The Variable Name Lookup tab allows users to connect the short name for the indicator used as the header in the spreadsheet with the more descriptive title used in the atlas." 

### Scrub
As expected, I spent a fair amount of time in this stage of the process - cleaning the data. I started by preparing the incarceration data - taking a subset of the data focused on the years of interest (2008-2017); removing prison data to stay focused on jail data; dealing with null values through county or state-level imputations; transforming the incarceration dataframe so that each row corresponded to a unique county.  I then turned my attention to the other county-level data - cleaning, dealing with null values, and finally merging all the dataframes together.

### Explore
In this stage, I created the target variable by testing out a few approaches, finally deciding on a *2017 incarceration rate change variable* that indicated whether the rate **Increased** or **Decreased/Stayed the Same** compared to the average rate over the last 9 years (per county).  Approximately 61% of the 3,103 counties in the dataframe had increased rates and 39% showed decreased or steady rates.  

Once the target variable was in place with the class labels, I set aside the test set and proceeded with exploring a copy of the training data.  With over 600 features, I selected a few variables from various categories on incarceration, population, and income to visualize the way counties with increased and decreased/steady rates compared.

I also looked at how different regions of the U.S. compare when it comes to the number of counties with increased versus decreased/steady rates.  The only region where the number of counties with increased rates doesn't surpass the number of counties with the same/decreased incarceration rates is the West. All other regions, especially the South and Midwest have far more counties with increased rates than not.

```
# Quick visualization of our target var labels by region
sns.countplot('region', data=data, hue="incarc_rate_change_2017")
plt.title("Incarceration Rate Change in 2017 by Region", fontsize=18)
plt.show();
```
![](https://imgur.com/tbz2dVa.png)


Interestingly, in keeping with the regional breakdowns, if we look at immigration rates from 2000 through 2010 split by counties with increased versus decreased/steady rates, we can see that in all regions, counties with decreased/steady incarceration rates have higher immigration rates compared to counties with increased incarceration rates.  

```
# bar plots of immigration rate (2000-2010) by region and incarceration rate change
sns.barplot( x="region", y="Immigration_Rate_2000_2010", hue="incarc_rate_change_2017", data=feats_target)
plt.title("Immigration Rate from 2000-2010 by Region and by Incarceration Rate Change", fontsize=18);
```
![](https://imgur.com/ETqulJ6.png)

The violin plot below displays the distribution of per capita income data across our two labels, featuring a kernel density estimation of the underlying distribution. We can see that the largest distribution of data for counties with increased incarceration rates correspond to a slightly lower per capita income compared to counties with the same or decreased incarceration rate in 2017 (relative to the average over 2008-2016 per county).

```
# Plot violin plot of target var and per capita income
sns.violinplot(x="incarc_rate_change_2017", y="PerCapitaInc", data=data)
plt.title("Distibution of Per Capita Income by Incarceration Rate Change in 2017", fontsize=18)
plt.show();
```
![](https://imgur.com/AYaysEx.png)

When comparing the number of jail admissions and the number of unemployed individuals in 2017 alone, we can see a clear upward trend - unemployment and jail admissions have a positive correlation.

```
# jointplot of 2017 jail admissions and total number of unemployed persons
g = sns.jointplot(x="NumUnemployed2017", y="total_jail_adm_2017", 
                  data=num_feats_targ, kind='reg');
```
![](https://imgur.com/rj0XNLc.png)

After exploring and visualizing more features, I converted the categorical data to dummy variables, expanding the final number of features to 698.  I also decided to set aside a subset of the final dataframe that only included the other county-level data, removing all incarceration-related features (349 features total).

### Model
My considerations for selecting classification models were based on the following dimensions:
* interpretability and ability to identify feature importance
* capable of dealing with data that has multicollinearity amongst the features
* capable of dealing with a high number of features

Given the above, I proceeded with tree-based, non-parametric models because they are flexible, powerful, capable of dealing with highly correlated features, and they offer direct insight and measurement of feature importance.  I also selected the F1 score and Confusion Matrix as my primary method for evaluating model performance.  I first built a basic Decision Tree Classifier as my baseline model, followed by several ensemble methods (Random Forest, Extra Trees, and AdaBoost) using GridSearchCV for hyperparameter tuning. 

Below is the AdaBoost Classifier used on the data with incarceration-related features retained:

```
# Construct the pipeline
ab_pipe = Pipeline([('ab', AdaBoostClassifier(random_state=42))])

# Set Grid Search Params
ab_param_grid = {
    'ab__n_estimators':[10,100,500,1000], 
    'ab__learning_rate':[1.0, 0.5, 0.1]
}

# Construct grid search
ab_gs = GridSearchCV(estimator=ab_pipe, 
                    param_grid=ab_param_grid, 
                    scoring='f1', 
                    cv=3, return_train_score=True)

# Fit using gridsearch
ab_gs.fit(X_train, y_train)

# Print best f1 score
print('Best f1 score: %.3f'%ab_gs.best_score_)

# Print best parameters
print('\nBest Params:\n', ab_gs.best_params_)
```
```
# Predict on Test Set
ab_gs_pred = ab_gs.predict(X_test)

# Generate metrics for model performance
ab_gs_f1_score = f1_score(y_test, ab_gs_pred)
print("AdaBoost Classifier: F1 score on Test Set: {}".format(ab_gs_f1_score))
print("--------------------------------------------------")
print(classification_report(y_test, ab_gs_pred))
```

### Interpret 
The baseline model F1 score was 78%.  The AdaBoost classifier was able to achieve a higher F1 score of 87%, a 9% improvement compared to baseline. As expected the top features included previous years' incarceration rates along with population change rates.

```
# Build confusion matrix
unique_label = np.unique([y_test, ab_gs_pred])

cmtx = pd.DataFrame(confusion_matrix(y_test, ab_gs_pred, labels=unique_label), 
                   index=['true: {:}'.format(x) for x in unique_label], 
                   columns=['pred:{:}'.format(x) for x in unique_label]);
sns.set(font_scale=1.25)
sns.heatmap(cmtx, annot=True);
#sns.heatmap(cmtx/np.sum(cmtx), annot=True, fmt='.2%');

plt.title("Confusion Matrix for AdaBoost Classifier", fontsize=20);
```
![](https://imgur.com/gDd3Eco.png)

When it came to modelling the data withOUT any incarceration-related features, the AdaBoost was still the best performing in terms of having the highest F1 score (75.9%), but the Random Forest model performed better in terms of the F1 scores per class label.  AdaBoost was not as accurate in classifying counties with decreased/steady rates, erroneously predicting 190 counties to have increased rates when in fact they had decreased/steady rates. 

The Random Forest Classifier also misclassified counties with decreased/steady rates, but not as many as AdaBoost (160), while accurately classifying 82 counties with decreased/steady rates.

![](https://imgur.com/Yq5Wdbx.png)

## Findings
* In general, the models struggled with accurately classifying counties with decreased/steady rates, resulting in consistently lower f1 scores for this true class label.
* If we include incarceration-related features, we can see an improvement in the overall F1 scores of 87%, which is a little over a 10% increase from our best performing model on the data withOUT the incarceration-related features.
* However, including incarceration-related features unsurprisingly means that these same features will have the most predictive power, thereby mitigating the overall understanding of how other population and socio-economic conditions interact with jail incarceration rate changes.
* Looking at the model on data withOUT incarceration-related features, we can see that county-level data on disability, population change rates and natural change rates, household income, the proportion of elderly in the population, and percentage change in employment are among the top 10 important features in our best performing model.

### What next?
Building off this project, there are a few next steps I'd like to explore in order to make this analysis more useable and insightful to relevant stakeholders:

First, I would like to introduce additional county-level data on education, businesses, and political party breakdowns and/or election data.  Second, I would perform some feature engineering, which could hopefully standardize some of the way that the data is presented (for instance percentages versus raw numbers) as well as reduce some of the dimensionality.  Third, upon modeling the data I would then seek to identify the top n features in order to make this model more appropriate for continuous monitoring and predicting.  Based on this, I would build a pipeline for updating and making the model available through a more user-friendly interface so that interested parties can search for their counties.

And with that, it's time to get back to work.


