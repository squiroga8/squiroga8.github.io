---
layout: post
title:      "An Intro to Hypothesis Testing"
date:       2019-11-08 11:17:13 -0500
permalink:  an_intro_to_hypothesis_testing
---


For the Module 3 Project, we were tasked with coming up with three hypotheses to test using the Northwind Database, an sqlite database created by Microsoft for a fictitious company.  This is all to say that I poked around the database the same way a child approaches anything - letting some natural inclination towards curiosity and experimentation reign supreme, until you get distracted by something else, of course.  

The purpose behind this project was to put into practice some newly learned techniques and approaches for hypothesis testing.  While I might have a few more coding skills than a child (that's probably not a fair assumption to make anymore), working my way through this project gently revealed some areas that could use a bit more attention from me.  This is my way of saying, I'm now going to give myself a mini-tutorial.  Feel free to join along.

## Getting your hypotheses in order
A lot can be said about the initially, counter-intuitive phrasing used when formulating and rejecting, or failing to reject, a hypothesis.  But as a quick refresher, when starting out with a hypothesis in mind, you'll want to define the *null* and *alternative* hypotheses.  

Usually, the spark of curiosity aligns with the Alternative Hypothesis; this is the one that points to something different, maybe even something special.  Whereas the null, bless the null, counteracts this by stating "Nope, nothing to see here."  

For example, one of the hypotheses I formulated stems from the question "Is there a difference in performance between US and UK employees?"  I then proceeded to describe the many metrics that could be used for a performance evaluation, however for the purposes of this project, I chose the total number of orders as the metric to evaluate.

![](https://)<a href="https://imgur.com/SqsIwzJ"><img src="https://imgur.com/SqsIwzJ.png" title="source: imgur.com" /></a>

Accordingly, these are the null and alternative hypotheses I defined:

* **Null Hypothesis**: Avg number of orders processed by employees in the US and the UK are the same.
* **Alternative Hypothesis**: Avg number of orders processed by employees in the US and the UK are different.

Great, I have the data, I have my hypotheses formulated, I'll go ahead and set the alpha level (0.025), and then can be on my way to hypothesis testing!

## Time for T-Testing
T-Tests are a kind of hypothesis test that assess the difference between two averages, in our case - the average number of orders managed by US and UK employees.  The T-Test, or student's t-test, is a handy one, and should be used when you do not know the population standard deviation, and/or when you have a small sample size.  With 9 employees in our database, this makes the T-Test the prime candidate.

For convenience, I split the sample into two groups - US employees and UK employees.  And now, the test:

```
#two sample t-test
results = stats.ttest_ind(usa_emp_orders.TotalOrders, uk_emp_orders.TotalOrders, equal_var=True)
#p-value set at 0.025
alpha = .025
if (results[0] > 0) & (results[1]/2 < alpha):
    print("Reject the null hypothesis, the avg number of orders between US and UK employees is not the same.")
else:
    print("Fail to Reject the Null Hypothesis")

print("Two-Sample T-Test p-value: {}".format(results[1]))
```

And the results:

*Reject the null hypothesis, the avg number of orders between US and UK employees is not the same.*

*Two-Sample T-Test p-value: 0.002063854331616113*

Our p-value of 0.002064 is well below the alpha level we initially set, so we can go ahead and Reject the Null Hypothesis.  Our child-like curiosity wins!  

Now let's use the momentum of this minor victory to move into a light introduction to another important method for hypothesis testing - the ANOVA test.

### And ANOVA?
ANOVA, or analysis of variance, is essentially an extension of the t-test, where ANOVA is used when you want to compare the means of a condition between 2 or more groups.  There are three assumptions in order for ANOVA results to be considered accurate and reliable:

1. Normality of the group distributions, *unless* the group sizes are roughly equal (making the F-stat robust to violations of normality)
2. Homogeneity of variance, *unless* the group sizes are equal (same as above)
3. Independent Observations

ANOVA is considered an omnibus test, as it tests the data as a whole, trying to detect if there is a difference in means somewhere, anywhere, in the model.  It won't indicate where the difference is located, meaning one must conduct post-hoc tests (or pairwise comparisons) to find exactly where the difference is between the groups.  

While there are multiple ways to run an ANOVA test, I used the statsmodels method.  Intuitively, this makes sense when you consider that ANOVA works like a regression model and is actually a generalized linear model, or GLM.

The packages needed for running ANOVA using statsmodels are:
```
import statsmodels.api as sm
from statsmodels.formula.api import ols
```

In my case, I set up the formula in a similar way as one would do with running an OLS regression, using TotalOrders and the Country of employees, where `Country` is *wrapped* with a `C( )` to indicate that it is a categorical variable.

```
# an ANOVA test, treating Country as a category
formula = 'TotalOrders ~ C(Country)'
lm = ols(formula, emp_orders).fit()
table = sm.stats.anova_lm(lm, typ=2)
print(table)
```

Results:
![](https://imgur.com/HQJJ9Dk.png)


As you can very clearly see, the F-stat produced from the ANOVA test is the same as the T-stat produced earlier.  This is because an ANOVA F-test between two categories is the same as performing a 2-tailed T-Test.  Phew!
