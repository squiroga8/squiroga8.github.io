---
layout: post
title:      "np.random.feels()"
date:       2019-11-08 10:56:57 +0000
permalink:  np_random_feels
---


I hate this.  I think, how can I get out of this?  Being forced to write a blog... worse, being forced to write a blog about a field where I feel so small, so devastatingly lacking in confidence, so... not the expert.  (Assumption being only experts should talk? eek.)

Yes, I've read blogs.  I recognize the tone one should take.  Even now, it's like I'm trying to trick myself into adopting that tone, that "whoa, life, right? Big ideas, huh... let's make them bite-sized and all feel good.  Ah, inspiring (pats back, I'm so clever and good)."  

Maybe I shouldn't be so hard on blogs, or bloggers.  This is, in a basic way, the formula for all writing, probably all "good" conversations too.  Maybe I'm just frustrated by it because I think I'll fail at it, I won't be any "good" at it.  Some part of me is disappointed with how much hypocrisy it reveals in me - that through my bad attitude and lack of desire to *blog* I'm somehow above it, better than it, but really I just don't want it to be terrible, maybe I even entertain fantasies of people loving my raw, honest rants on how blogging is insincere/lame/bad.  So I guess I'm stuck, and my being stuck is documented and on display in this blog post that I hope A) no one reads, and B) some people read and love it and it goes minorly viral and I'm validated and good.  It's A and B, even though A is mutually exclusive with B, because apparently my ego defies logic.

Alright, I am going to try and get over myself by writing about something I don't understand well.  I'm already feeling how cringe-worthy my bad attitude is and am hoping the rest of this experience will be humbling, maybe even transformative.

Back to all the things I don't understand as well as I'd like... what to pick... ANOVA, lambda functions, classes and OOP, re-sampling techniques, SQL subqueries with multi-table data retrievals, np.random.normal, really anything that starts with `np.random.___`.  

I'll start there, with that last one, which I'll use to help me pick out one of the first ones listed (currently reading a [blog post](http://www.sharpsightlabs.com/blog/numpy-random-choice/) explaining how np.random.choice works - highly recommended ).  

### **Using `np.random.choice` to randomly select one of the topics I don't understand very well, so that I can try to learn about it better through blogging about it.**

Turns out, `np.random.choice` is like rolling a dice, where each of the faces of the dice are the items you input, and the output will be the result of rolling that dice.  Like most things, this is better demonstrated through an example.  Here's the set-up:

* `np.random.choice(a=`the array you want to work on`, size=`the size/shape of the output`, replace=`if you want to replace your sample or not (true or false)`, p=`the probabilities associated with the input array`)`
* `a` (required parameter) can be a numpy array of numbers, an integer for numpy to create an array up to using the `np.arange(n)` function, python lists, typles, and other python sequences.
* `size` indicates the size of the output, which will be a numpy array (the total number of elements in the numpy array)
* `replace` indicates whether or not you want to sample with replacement - do you want to replace your initial selection, effectively throwing it back into the mix of possible choices? This is important for situations where you're choosing multiple times. In my case, I will not replace because if I were to run the np.random.choice function again, I'd like for it to choose a new topic for me to review.
* `p` the probability of selecting a given item, which is by default set at equal probabilities for all items in the input array (rolling a fair dice).  If you want to provide this parameter, then it needs to be in the form of an "array-like" object (an actual numpy array, list or tuple).

Another quick note, `np.random.choice` is considered a pseudorandom number generator because the numbers in the output are not actually "random" but they approximate random numbers.  In using psuedorandom numbers, the output is deterministic, and what determines the output is the "seed" we set. So if you use the same seed, the pseudorandom number generator will produce the same output.  This is set with `np.random.seed(`number`)`.  Again, this means that `np.random.choice` is kind of random, in that there is no relationship between the seed and the output, but using the same seed to produce the same output can be helpful for reproducibility and code testing.  Just don't forget what seed you set!

Here it is:
```
import numpy as np

things_I_dont_understand_well = ['ANOVA', 'Lambda Functions', 'np.random.normal', 'Classes and OOP', 
                           're-sampling techniques', 'SQL subqueries with multitable data retrieval']

# make an np.array of probabilities for each item selection, clearly favouring some over others
probs_list = [5, 4, 5, 1, 3, 4]
probs_array = np.array(probs_list)
probs = probs_array/sum(probs_array)

np.random.seed(0)
blog_topic_selection = np.random.choice(a=things_I_dont_understand_well, size=1, p=probs, replace=False)

print("And the next topic I'll try to explore is: {}".format(blog_topic_selection[0]))
```
The output: 

*And the next topic I'll try to explore is: np.random.normal*

Seems fitting!

### **A Quick Intro to `np.random.normal`**

This function allows you to create a NumPy array that contains normally distributed data (the "bell curve" distribution).  The output is an NumPy array of random samples, dictated by the parameters used in the original `np.random.normal` function.  These include the following: 

* `np.random.normal(loc= `to set the mean of the distribution`, scale= `the standard deviation of the distribution `, size= `the "shape" of the outputted NumPy array `)`
* `loc` is the parameter that controls the mean of the function, which will default to 0 if a different mean is not specified.
* `scale` is the parameter that controls the standard deviation of the (normal) distribution, which by default is set to 1.
* `size` is the parameter that controls the size and shape of the output (the NumPy array).  NumPy arrays can be 1-dimensional to multi-dimensional.  If you specify a single integer for this parameter, the output will be a 1-dimensional NumPy array of random normal values.  If you specify a `size = (2,4)` then the np.random.normal function will generate a NumPy array with 2 rows and 4 columns, filled with number drawn from a - you guessed it - a random normal distribution.  Note that `np.random.randn` is a special case of `np.random.normal`, where the former is effectively the same function but with the `loc` and `scale` parameters defaulted to 0 and 1 respectively.

Each time you run this function, the resulting NumPy array of random samples will be slightly different (thanks to the randomness part), but the distribution will still follow the same, normal shape.  It is advised to choose a large `size` value, usually arount 10000, to ensure that results are more accurate.

This function is useful for normality tests, hypothesis testing, and probably other more advanced topics that I haven't encountered yet.  But here is a normal distribution of my fluctuating levels of confidence in myself as I learn and put into practice the things I'm learning in this course.

```
#On a scale of -5 to 12.5 for how confident I feel in practicing what I'm learning
#size = 456 days or 15 months, the duration of my course
#loc or mean is set at 2 with a std of 3
norm_feels = np.random.normal(loc=2, scale=3, size=456)
plt.hist(norm_feels)
plt.title('Distribution of my self-confidence while taking this course')
plt.xlabel('Confidence levels in learning ds (not the stat confidence-levels-kind)')
```
![](https://)<a href="https://imgur.com/eciL4Fs"><img src="https://imgur.com/eciL4Fs.png" title="source: imgur.com" /></a>

Sure, I set the mean to a highly reassurring level of 2, but with a sample of 456 days, things look more optimistic than I expected, certainly more than they did when I started writing this blog.  Maybe eventually I'll deviate from the beloved Gaussian distribution and develop a nice left-tail skew, fortified by my growing confidence.





