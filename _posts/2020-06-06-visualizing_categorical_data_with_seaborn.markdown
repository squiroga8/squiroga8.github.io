---
layout: post
title:      "Visualizing Categorical Data with Seaborn"
date:       2020-06-06 22:03:19 +0000
permalink:  visualizing_categorical_data_with_seaborn
---


In this blog, I'll provide a quick walk-through of how to approach data visualization of exclusively categorical data using seaborn.  

The data I'm using comes from my Capstone project for the Flatiron School's Immersive Data Science program, which focuses on classifying changes in the 2017 jail incarceration rates at the county level in the U.S..  The data is comprised of county-level data from the [Vera Institute for Justice](https://github.com/vera-institute/incarceration_trends) for incarceration data and the [USDA's Economic Research Service](http://trends.vera.org/incarceration-rates?data=pretrial) for other county-level data on population, employment, income, and veterans.  More information on these amazing sources can be found at the bottom of this blog. 

## Visualising Categorical Data
### Getting Started
I'll skip over the usual set-up with the data - loading the data into pandas, merging data sources, ensuring proper data types, dealing with null values, and completing some minor feature engineering to have one final dataframe with clearly identified categorical and numerical data.  Once I have my final dataframe that I want to work with, I start by setting aside the test set and making a copy of my training set so that I can use this for exploration and potential transformations (which will later by applied to the test set as needed).  I also recommend quickly extracting the categorical data for exclusive exploration and visualization.  Here's how:

```
import pandas as pd

# Identify numeric and categorical vars from features
categ_feats = feats.select_dtypes(exclude=['int', 'float']).copy()
num_feats = feats.select_dtypes(include=['int', 'float']).copy()
```

With `select_dtypes()` I could have avoided using the *exclude* clause by just specifying `object` or `category`, however using the *exclude* clause means I'll capture boolean, string, category, and object datatypes in this subset of categorical data, which is exactly what I want.

Since my project was a classification task to predict an increase or decrease/steady rate change to the 2017 jail incarceration rate, the target variable is also categorical.  We'll want to include this in the subset of categorical data for more in-depth exploration, but since I removed it during the `train_test_split` stage, I'll quickly merge the target variable with my `categ_feats`:

```
# merge categ dataframe with target var using the index
categ_feats_targ = pd.merge(categ_feats, target, left_index=True, right_index=True)
categ_feats_targ.head()
```
![](https://imgur.com/wIyUFwF.png)

Before being able to do any data visualization, we need to import matplotlib and seaborn:

```
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
```

We include `%matplotlib inline`, which is a built-in magic function for IPython notebooks, so that the data visuals conveniently show up within the notebook.

While we're importing our needed packages, we can also set the style aesthetics and parameters to have consistent palette and figure size options throughout our exploration and visualization.  

```
sns.set_style("whitegrid") 
sns.set(rc={'figure.figsize':(14,10)})
sns.set_palette('Set2')
plt.rcParams["axes.labelsize"] = 14
```

In the above, we're selecting a set seaborn style for the overall aesthetic, setting the figure size for all visuals, selecting a color palette, and setting the fontsize of our axes labels. 

### Categorical Data Visualization
Since we are exploring strictly categorical data at this stage, our options for visualizations include count plots, which can be accessed either through seaborn's `countplot` function or through `catplot` using `kind="count"`.  There are a number of visualization types that can include a mix of numerical and categorical data, including seaborn's FacetGrids, boxplots, and violin plots.  For the purposes of this blog, we'll stick with countplots for visualizing strictly categorical data. 

We'll start by showing our target variable splits across divisions of the U.S..  Since our data is at the county-level, with roughly 3,000 observations total, and roughly 2,400 in our training data used here, the count presented below is the count of counties with an increased versus decreased/stead jail incarceration rate per division in the U.S.:

```
# plot target variable by division of the U.S.
sns.countplot(y='division', data=categ_feats_targ, hue="incarc_rate_change_2017")
plt.title("Incarceration Rate Change in 2017 by Division", fontsize=18)
plt.show();
```
![](https://imgur.com/uRF3b1i.png)

While the above shows the frequency distributions across divisions using our target variable to split the data, let's quickly see if the other location variables add to our understanding of the how rate changes are geographically distributed in the U.S.:

```
# plot target variable by region of the U.S.
sns.countplot(y='region', data=categ_feats_targ, hue="incarc_rate_change_2017")
plt.title("Incarceration Rate Change in 2017 by Region", fontsize=18)
plt.show();
```
![](https://i.imgur.com/UtmOM3L.png)

The only region where the number of counties with increased rates doesn't surpass the number of counties with the same/decreased incarceration rates is the West. All other regions, especially the South and Midwest have far more counties with increased rates than not.

### Seaborn's countplot and catplot
In the previous two examples, we used seaborn's `countplot` which directly provides a plot of the *count* of the selected variables. Here's a quick breakdown of the parameters we included and their implications for the charts:  
* Provide `x=` or `y=` to specify if the plot should be horizontal or vertical;
* Specify the data source under `data=`;
* For adding an extra level of data split/comparison, include the `hue=` parameter.

Here's another example of using the `hue` parameter to provide an additional split on the data.  In this example, we're using `catplot` and specifying the `kind="count"` within the parameters.  This is the same as using `countplot` as we previously did, however `catplot` has additional `kind` specifications that can be used for visualizing both numerical and categorical data in one plot.

```
# visualize counts of counties by urbanicity category in each region
sns.catplot(x='region', hue='urbanicity', kind="count", data=categ_feats_targ)
plt.title("Urbanicity Levels of Counties by Region in the U.S.", fontsize=16)
plt.show();
```
![](https://i.imgur.com/zmyI8xU.png)

While the above doesn't include the target variable splits, it gives us a sense of how many counties fall under the four urbanicity categories per region of the U.S., with rural counties consistently outnumbering the other categories in each region, especially in the South and Midwest. Given the prevalence of rural counties in the U.S., it would be good to zero in on how the incarceration rate changes distribute amongst urbanicity levels.

```
# countplot of target var labels by urbanicity category
sns.countplot('urbanicity', data=categ_feats_targ, hue="incarc_rate_change_2017")
plt.title("Incarceration Rate Change in 2017 by Urbanicity", fontsize=20)
plt.show();
```
![](https://i.imgur.com/Vhl4Afc.png)

We can see that only in urban counties does the number of counties with decreased/steady rates outnumber the number of counties with increased rates.  In rural counties, roughly twice as many have increased rates than decreased/steady rates, whereas with small/mid and suburban the number of counties with increased rates is only slightly higher than those with decreased/steady rates.

Categorical data visualization is an important step for getting a first pass at the breakdown of our data, in particular for understanding how the categories are featured against each other or as sub-categories of the other.  

## Data Sources
The database I built for this project is comprised of the following sources:
* Incarceration data comes from the Vera Institute of Justice database of county and jurisdiction level incarceration rates form 1970 to 2017 (https://github.com/vera-institute/incarceration_trends), which comes from careful and diligent integration of data collections and annual surveys from the Bureau of Justice Statistics (BJS), including the Census of Jails, the Annual Survey of Jails, and the National Corrections Reporting Program. This particular Vera Institute effort to compiling and understanding incarceration trends at such a granular level is also supported by Google.org, the John D. and Catherine T. MacArthur Foundation Safety and Justice Challenge, and the Robert W. Wilson Charitable Trust." http://trends.vera.org/incarceration-rates?data=pretrial
* County-level data on Population, Income, Jobs, and Veterans comes from the US Department of Agriculture's Economic Research Service (ESR) and the USDA's Atlas of Rural and Small Town America ( https://catalog.data.gov/dataset/county-level-data-sets) and (https://www.ers.usda.gov/data-products/atlas-of-rural-and-small-town-america/download-the-data/) 
