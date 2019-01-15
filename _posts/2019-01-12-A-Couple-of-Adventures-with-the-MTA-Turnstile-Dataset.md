---
layout: post
Title: A Couple of Adventures with the MTA Dataset
---

# Introduction

When I started my first week at Metis Data Science Bootcamp, I learned about the first project I would have to complete: exploratory data analysis (EDA) on the [MTA Turnstile
dataset](http://web.mta.info/developers/turnstile.html). The point of the project was to advise a fictitious non-profit on the best stations and times to place their street teams advertising their summer gala, their main fundraiser. I learned a lot about EDA, and I'd like to share a few interesting things I learned along the way. I won't go into the results of that analysis. Rather, this post is about a couple of tangents which caught my attention. 

This post is split into two parts: an examination of the .diff() method in pandas, and a cursory exploration of whether the distribution of ridership among the subway stations of the MTA follow an Pareto distribution, or more generally a power law. To perform the data analysis, we used python and jupyter notebooks, with an emphasis on the pandas and matplotlib libraries.

## Using the .diff() method

My two partners, Max Barry and Stephen Ilhardt and I decided on the modest of goal of figuring out which station had the largest amount of traffic over roughly the period of May 2018. This ended up being a more difficult task than it at first sounded. The trouble was that the columns in the dataset which were labeled 'ENTRIES' and 'EXITS' were cumulative counts over long stretches of time. Some of these values were in the 100's of billions. Clearly, some manipulation was required before this data could be of any use to us.

We ended up finding a quick solution to this problem. Since cumulative values come about through successive addition, taking the difference between consecutive values should undo it. All we had to do was group by the turnstile (represented by the columns 'C/A', 'UNIT', 'SCP', and 'STATION') and apply the .diff() function on a pandas dataframe. The following example also contains three lines that turn negative values into postives (who's every heard of negative foot traffic?) and filters out unreasonably large values.

```python
turnstile = ["C/A", "UNIT", "SCP", "STATION"]

# Makes sure the index is the naturals. Necessary for diff() to work
df.reset_index(inplace=True, drop=True)
df = df.sort_values(turnstile + ['DATE', 'WEEKDAY', 'TIME']) 

df[['TOTAL_ENTRIES', 'TOTAL_EXITS']] = df.groupby(turnstile)[['ENTRIES', 'EXITS']].diff()

# make negative values positive
df[['TOTAL_ENTRIES', 'TOTAL_EXITS']] = df[['TOTAL_ENTRIES', 'TOTAL_EXITS']].apply(abs)

# filtering out values larger than 4000
df = df[df['TOTAL_ENTRIES'] < 4000]
df = df[df['TOTAL_EXITS'] < 4000]
```

This accomplishes what we were trying to achieve very quickly. Contrast with this more long winded strategy:

```python
turnstile = ["C/A", "UNIT", "SCP", "STATION"]

df = df.sort_values(turnstile + ['DATE', 'WEEKDAY', 'TIME']) 
df[['PREV_TIME','PREV_ENTRIES','PREV_EXITS']] = \
                            (df.groupby(turnstile)['TIME','ENTRIES','EXITS']\
                            .transform(lambda x: x.shift(1)))

def get_entry_counts(row, max_counter):
    counter = row["ENTRIES"] - row["PREV_ENTRIES"]
    if counter < 0:
        counter = -counter
    if counter > max_counter: 
        return 0 
    return counter

def get_exit_counts(row, max_counter):
    counter = row["EXITS"] - row["PREV_EXITS"]
    if counter < 0:
        counter = -counter
    if counter > max_counter:
        return 0
    return counter

# cut off any entries and exits that were greater than 4000
# took difference between consecutive counts

df["ENTRY_COUNTS"] = df.apply(get_entry_counts, axis=1, max_counter=4000) 
df["EXIT_COUNTS"] = df.apply(get_exit_counts, axis=1, max_counter=4000)

```

The moral of this story is that there is often more than one way to accomplish the same thing. I personally find the .diff() example more readable, but there is of course other criterion for code. For example, perhaps the latter block of code is more computationally efficient. So, how do they compare?

When I timed the first block, it took 3.59 seconds to complete. The second block took 42.8 seconds to complete. The second example took a good order of magnitude longer to execute. It seems that .diff() is optimized to handle this sort of operation. 

## But is it a Pareto Distribution?

After the project was completed, I decided to go down a rabbit hole. I've heard in the past that many phenomena in nature happen to follow a Pareto distribution (e.g., wealth distribution and the population distribution between human settlements). When I looked at a graph of the most trafficked stations ordered by popularity, it seemed to follow a similar pattern. The below graph shows the total foot traffic for roughly the month of May.

![Stations by Total Foot Traffic](images/Pareto.svg)

### The Pareto Distribution

The Pareto Distribution is a probability distribution which is notable for its description of many natural phenomena, as well as its inability to be adequately described by common summary statistics such as mean and standard deviation. 

![Pareto Distribution](/images/Probability_density_function_of_Pareto_distribution.svg.png)

As can be seen from the above image, it is a power law distribution. For that reason, I will end up attempting to fit a general power distribution to the 

### The 20-80 Principle

"The [Pareto principle](https://en.wikipedia.org/wiki/Pareto_principle) states that, for many events, roughly 80% of the effects come from 20% of the causes." - Wikipedia

I did a calculation of the top 20% of stations by total foot traffic. They accounted for 60% of the traffic. This is one mark against following a classic Pareto distribution, though it should be noted that the 20-80 rule is not a hard and fast determination of whether something is a Pareto distribution. The distribution has parameters that can change; and when they change, the level of inequality grows.

The application of this finding to our imaginary client would of course be that focusing only the most popular stations would be sufficient to reach most of the ridership of the MTA. Of course, my client wouldn't care if this data *really* follows a Pareto distribution, but, for some reason, I did.

### Attempting a Fit

So, here's what I came up with:

![First Attempt](images/fit_first_attempt.svg)

As you can probably see, this is not a very good fit. It quickly dips way below the observed data, before going way over it, and then permanently dipping below it again and even into the negatives. This is basically a disaster. 

After doing a little bit of research, I learned that the tails of power-law distributions in general tend to deviate away from the distribution. I cut off both the highest values as well as the lowest values to see if the middle section fit any better.

![Second Attempt](images/fit_second_attempt.svg)

This definitely still shows that same behavior as before (i.e., under --> over --> under), but this deviation appears to be much less severe than in the first fit. This is actually quite promising.

### Conclusion?

I didn't have time to do rigorous analysis on this data. In my research, I find an interesting [article](https://arxiv.org/pdf/0706.1062.pdf) available on arXiv.org arguing that the occurrence of power-laws in empirical data have been vastly over-stated. One thing I would like to do in the future is apply the [Kolmogorov-Smirnov test](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test) on this data as outlined in the arXiv article to come to a more conclusive result. They even have some [code](http://tuvalu.santafe.edu/~aaronc/powerlaws/) up that looks interesting!

In conclusion, I don't have too much of a conclusion. I'm pretty confident in saying that the full empirical distribution as observed is not obeying a power law. But, the middle section seems like it could be more promising. If I had spent more time trying to find the right initial estimates for a fit, then maybe an even nicer fit would have popped up. Regardless, a take-away for me is to be more skeptical that a pure power-law or Pareto distribution is in front of me just because of some superficial similarities.

