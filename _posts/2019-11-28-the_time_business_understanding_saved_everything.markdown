---
layout: post
title:      "The time Business Understanding saved everything "
date:       2019-11-28 08:25:27 +0000
permalink:  the_time_business_understanding_saved_everything
---


That's a bit dramatic.  But looking back, it's hard to imagine finding my way through a time series analysis project with over 14,700 unique zip codes and 22 years of data on housing prices, without the guiding force that was the *Business Understanding*.  For the Module 4 Final Project in Flatiron's Online Data Science course, we were tasked with the deceptively complex goal of identifying the "5 best" zip codes where a fictitious real estate investment firm should invest.  Defining "best" was up to us, which can be a difficult task considering many of us are reckoning with the monstrous battle of taking complex concepts like Auto Regressive Integrated Moving Average models and translating said concepts into Python, to then apply to our data, which also requires a significant amount of Pythonic sweet-talking (pre-processing), and finally, should all of this work out, evaluating and communicating the results *effectively*.  Who has time for Business Understanding?!

After glimpsing into the abyss of infinite possibilities for dealing with this dataset and the project goal, I quickly realized that the Business Understanding might actually be my life line.  It was a secret power that could give order and direction to everything, well almost everything, and that power was basically entirely of my own making.  *I* could decide what "best" meant, *I* could decide what values this firm had, *I* could define and determine the story of this firm's interests, and therefore outline the narrative for my data analysis.  This kind of ownership of the story was invigorating, and with my newfound agency I set out to define the business at hand.

## CRoss-Industry Standard Process for Data Mining (CRISP-DM) 
At first, I let my imagination run wild for a bit when it came to the fictitious firm - this would be a firm that wanted to set new precedents in environmentally friendly practices, that wanted to challenge growing economic inequality, that would ethically turn away from housing bubbles, that would... right, but then there was the reality of the data I had on hand and the deadline ahead of me.  If only there was a framework that could help me stay on track with defining the business understanding in line with the data understanding... welcome CRISP-DM!

![](https://imgur.com/lPxd1ug.png)

I chose this data science process framework - CRoss-Industry Standard Process for Data Mining - largely due to the weight given to the Business Understanding component, which not only informed by final recommendations but also outlined my first crucial steps in preparing the data.  When reading more about this CRISP-DM, the line that most caught my attention under the Business Understanding phase was " As a Data Scientist, it's up to you clarify the requirements and make sure that everyone involved understands what the project is and isn't."  This couldn't have been more true than in this project, and I have a feeling this will be crucial to any future data endeavors.

Armed with this framework and the electrifying energy of realizing your own agency, I defined the business values, my  business understanding, and the implications for initial data preparation:

**The (fictitious) firm - Redwood Real Estate**

* Based in the Pacific Northwest, Redwood is seeking to invest in metro areas in Oregon and Washington only.
* Redwood is weary of areas where housing prices are surging, as they are a family-owned business that identifies as more risk adverse with a focus on ethical and shared growth.
* Redwood's business values include sustainable investments, establishing ties with local community, and responsible real estate appraisal practices. They want to avoid contributing to what they see as growing economic inequality.
* Redwood's slogan is: "Let's grow together."

Fun, but how does the above translate into actionable steps for the data understanding and preparation?

**Business Understanding meets Data Understanding**

* Redwood is seeking to invest in metro areas in the Pacific Northwest, specifically Oregon and Washington, where they can establish long-term ties to the local community.
* Given Redwood's geographic interests - *The data can be subset to focus exclusively on zip codes in OR and WA states*
* Given Redwood's interest in metro areas - *Remove zip codes that are not part of Metro areas, based on Metro column.*
* Since Redwood does not want to invest in areas considered to be housing bubbles, where housing prices have surged to quickly, or in areas with high volatility - *Remove areas where housing price to income ratio is too high. Requires appending additional data and calculating new metrics, as well as industry research on acceptable ratios and inequality.*
* Redwood would like to invest in areas that have shown a capacity for recovery since the 2008 financial crisis - *Identify areas with highest growth from 2008-2018, after removing areas that have high inequality as demonstrated through the housing price to income ratio.*
* Redwood is slightly risk adverse and wants to avoid areas with high variability - *Examine distributions and standard deviation as indicator of volatility of housing price changes over select timeframe.*

Great!  Now I had some very clear next steps that I could take, with the added benefit that these steps would drastically narrow my focus on a select number of zip codes.  I went from 14,723 zip codes to 341 zip codes in WA and 224 in OR. From there, I looked at the data from the Metro level; grouping together aggregated statistics on mean and median income levels (appended at the zip code level), I created a new, but important business understanding benchmark - the Housing Price to Income ratio.

To enhance my business understanding and establish a workable threshold for this ratio, I did two things in line with CRISP-DM:
1. Research industry standards good housing price to income ratios under health economic conditions (rule-of-thumb ratio in real estate is 2.6)
2. Look at my data and understand the distribution of the ratios (median ratio was 3.7)
3. Visualize these standards against the available data and make a decision (3.7)

**Housing Price to Income Ratio per Metro with industry standard ratio**

![](https://imgur.com/OenNbdf.png)

**Boxplot of distribution of Housing Price to Income Ratios with Mean and Median calculated**

![](https://i.imgur.com/Ybbf1dw.png)

At this point, I was left with 19 metros.  Zooming back into the zip codes, I calculated growth over the last 10 years, allowing for the demonstration of resilience and growth since the 2008 financial crisis.  With the top 10 zip codes, I looked at their standard deviation to provide a measure of risk, or volatility, in their growth rates, identifying the final 5 zip codes to use for modeling and forecasting.

While time series analysis was arguably the main focus for this project given the curriculum and material leading up to it, in practice it was the Business Understanding combined with the intricacies of the Data Understanding that provided both the foundation and guiding principles for this project.

In the future, I know I will not always have the great responsibility and great power that is defining the business values and interests, but going through the process of clarifying the Business Understanding from both sides will sharpen my vision for what is possible, promising, and ultimately meaningful in the data analyses tasks ahead of me.  


