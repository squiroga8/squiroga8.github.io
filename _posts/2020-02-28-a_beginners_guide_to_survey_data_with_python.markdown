---
layout: post
title:      "A Beginner's Guide to Survey Data with Python"
date:       2020-02-28 06:34:50 +0000
permalink:  a_beginners_guide_to_survey_data_with_python
---


Admittedly, it had been several years since I had last used STATA.  For as much time I had spent on econometrics problem sets in grad school, I still couldn't quite find the motivation to jump back into any .do files and dust off my STATA skills.  Part of the reason was that I've devoted the better part of the last 15 months to learning Python, truly enjoying the world of Jupyter Notebooks and all the wonderful libraries and packages I could import.  However, STATA and SPSS, still have ahold of the survey data analysis world, and for my Module 5 project, I decided I wanted to work with survey data.  So there I was wondering how to proceed when I learned about `pyreadstat`.

## `pyreadstat` to the Rescue

`Pyreadstat` is a library for reading and writing SAS, STATA, and SPSS files into Python, with the ability to retain a metadata container of variable labels and value labels - a critical and often messy part of understanding the survey.  (Take a look at the `pyreadstat` documentation [here](https://ofajardo.github.io/pyreadstat_documentation/_build/html/index.html#/).)

`Pyreadstat` is what has allowed me to stay in my comfortable python world.  It also reintroduced me to the many challenges that come with working with survey data.  Luckily I learned it is armed with a few really helpful tools that made the transition not so bad.

First, I'll go over how to import a STATA or SPSS file, including how to retrieve the survey metadata of variable labels and value labels.  Then I'll cover a few tips for exploring and dealing with survey data.

## The STATA/SPSS transformation to Pandas
First, import the pyreadstat library as follows:

```
import pyreadstat
```

Then load in your .sav or .dta file, making sure to retrieve a metadata container.  

```
df, meta = pyreadstat.read_sav('Survey_data.sav')
```

When you read in your dataframe, you might notice that the values are label encoded already.  Where you might have expected some string values denoting categorical answer options, you just see numbers.  While this might be useful eventually, especially come time for modeling, when you're first starting out and getting familiar with the survey it's always nice to have the categories on hand to make sense of what you're seeing.  There are two ways to accomplish this:

1. You can use the meta container to quickly map the value labels as needed:

```
df['S5'].map(meta.variable_value_labels['S5']).value_counts()
>>>>Male      420
>>>>Female    286
>>>>Name: S5, dtype: int64
```

2. You can enable the `apply_value_formats` field when loading in the data using `pyreadstat`, which will automatically incorporate the value labels as available:

```
df, meta = pyreadstat.read_sav('Survey_data.sav', apply_value_formats=True)

df['S5'].value_counts()
>>>>Male      420
>>>>Female    286
>>>>Name: S5, dtype: int64
```
	
I should caution that you shouldn't implicitly trust that the value labels are provided and/or applied perfectly.  If you proceed with including the value formats, be sure to check that they were applied consistently.  In my case, because I had merged several .dta files together, this likely introduced some messiness that resulted in the value labels being applied in a spotty manner.  You may need to replace and/or map the correct value labels to clean this up (more on that later).
	
Finally, as you load the data, I suggest creating a data dictionary containing the variables and their labels.  In the same block of code, you can make a meta data dictionary of the column names and their corresponding labels (ie. the survey questions).  Often, variable names carry no representative meaning in their name, so being able to quickly look at the variable label to see the question as it was asked in the survey is critical.  Here's how to go about that:

```
df, meta = pyreadstat.read_sav('Survey_data.sav')
data_dict = dict(zip(meta.column_names, meta.column_labels))
```
	
Now we're ready to start exploring the data!
	
## Getting Familiary with the Survey Data
First, let's take a quick look at that lovely data dictionary:

```
data_dict

Output:
 {'Directorio': 'DIRECTORIO',
 'Secuencia_p': 'SECUENCIA_P',
 'Orden': 'ORDEN',
 'P6074': '¿…….. siempre ha vivido en este  municipio?',
 'P756': 'Dónde nació…..:',
 'P756S1': 'En otro Municipio: Departamento:____________________',
 'P756S3': 'En otro país:',
 'P755': '¿Dónde vivía …. , hace cinco años?',
 'P755S1': '¿Dónde vivía …. , hace cinco años? Departamento:',
 'P755S3': '¿Dónde vivía …. , hace cinco años?  En otro pais',
 'P754': 'El lugar donde vivía ……. hace cinco años era:',
 'P753': '¿Dónde vivía …. , hace 12 meses?',
 'P753S1': '¿Dónde vivía …. , hace 12 meses? Departamento:',
 'P753S3': '¿Dónde vivía …. , hace 12 meses? En otro pais',
 'P752': 'El lugar donde vivía ……. hace 12 meses era:',
```

Yes, the sample above is in Spanish, which depending on your language capabilities can make things difficult, but you'll also notice that there are a few (...) and ______'s, which might mean that the question label as provided here is deliberately vague. In some cases, this might have to do with the fact that it's up to the interviewer to modify the question depending on who it's asked about.  For instance, it might be a question that's asked about multiple members of the household and not just the respondent.  Also notice that in this data dictionary, we don't see any indication of survey logic and skip patterns. 

All this to say, if and when you can have your hands on the actual survey questionnaire, a full survey data dictionary, and the survey methodology document this will give you the best footing going forward.  But within the realm of your jupyter notebook, having this data dictionary for quick reference is extremely helpful.

### A Note on the Nulls
Another notorious aspect of dealing with survey data is that, often by design, there will be plenty of null values.  With effective fieldwork monitoring and data quality checks, this isn't necessarily a red flag, but it's a product of the survey design.  In the survey programming and questionnaire development, it's not uncommon for the design of the survey to become ambitious and subsequently complicated in terms of topics covered and the resulting underlying logic.  

For instance, in practice, you might find that for evaluations using an experimental design, with treatment and control groups, the same survey is used for both groups to enable comparisons, but that whole subsets of questions are irrelevent to one group, resulting in a staggering amount of null values in some columns and across observations.  While this shouldn't be problematic, especially if the idea is to do analysis on two subsets of data corresponding to the treatment and control groups, it's an important thing to keep in mind before applying any data imputations to quickly deal with null values. 

Essentially, you should plan to spend a disproportionate amount of time on understanding the null values, as theoretically they are there by design, ie. certain questions were deliberately skipped based on the respondent and/or how they answered a previous question. 

One helpful tool for understanding the relationship between null values across the dataset is `missingno` which allows you to visualize the null values and identify correlations.  You can produce heatmaps to detect correlations between columns with null values, bar charts, and dendrograms for more in-depth understanding.  Check out this great tutorial on how to use `missingno` [here](https://github.com/ResidentMario/missingno).

### Categorical Data
If you decided to load in the data using `apply_value_formats=True`, make sure that you diligently examine the value labels for each of the categorical data.  Especially if you have to merge and/or concatenate different modules of the survey together, you might find that the value labels have been applied inconsistently.  You'll want to explore each categorical variable to ensure proper labels before doing any one-hot encoding/label encoding.  

You might find that some categories are totally blank and/or blank strings.  Ideally, we would have wanted to detect these while closely examining the null values, however based on the way the survey was prepared in STATA or SPSS it's possible that these get 'missed' when using `isnull()` with python.  

I suggest replacing all blanks with `np.nan` like so:
```
df.replace(r'^\s*$', np.nan, regex=True, inplace=True)
df.replace(' ', np.nan, regex=True, inplace=True
```

Survey data can be very rich data, but all data collection methods can have their flaws and shortcomings.  This is why it's important to have strong background knowledge and familarity with the survey questionnaire, its design and logic, and the overall evaluation purpose/design.  Expect to jump back and forth between methodology documents and the questionnaire itself when trying to make sense of what you're seeing in your Jupyter notebook.  And enjoy!



