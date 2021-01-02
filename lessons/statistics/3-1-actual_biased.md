[Think Stats Chapter 3 Exercise 1](http://greenteapress.com/thinkstats2/html/thinkstats2004.html#toc31) (actual vs. biased)

### Question

Use the NSFG respondent variable numkdhh to construct the actual distribution for the number of children under 18 in the respondents' households.

Now compute the biased distribution we would see if we surveyed the children and asked them how many children under 18 (including themselves) are in their household.

Plot the actual and biased distributions, and compute their means.


### Preparing the analysis
Do necessary imports:
```{python}
from __future__ import print_function, division
%matplotlib inline
import numpy as np
import nsfg
import first
import thinkstats2
import thinkplot
```

Read in data
```{python}
resp = nsfg.ReadFemResp()
resp.head()
```
![pandas head output](img/3_1_pandas_head.png)

The reports (presumably by parents) in this NSFG dataset indicate that average number of children in a family is 1.02

```{python}
children_in_fam = resp["numkdhh"]
children_in_fam_mean = children_in_fam.mean()
children_in_fam_mean
```
 Output

```1.024205155043831```

Generate histogram
```{python}
children_in_fam_hist = thinkstats2.Hist(children_in_fam, label='number of children in family')
thinkplot.PrePlot(2)

thinkplot.Hist(children_in_fam_hist, align='right', alpha=.25, color = 'g')
thinkplot.Config(xlabel='number of children', ylabel='Count', xlim=[-.99, 5] )
thinkplot.axvline(x= children_in_fam_mean, color = 'g', lw=2, alpha=1)
```
![histogram](img/3_1_histogram.png)

### A potential hypothetical wrinkle in the collected data
Yet if we were to survey children about their siblings, we may get a biased mean because the more children there are in a family, the more likely it is that two or more siblings of the same family would be surveyed, resulting in multiple counts of the same family, and those multiple counts being higher and more likely the larger the family is.

### Simulating bias
To simulate such a biased counting, we can create a copy of the dataset and augment it by multiplying the sibling count by the number siblings in the family, as if each siblying had been a respondant and count his or her family again.

Practically speaking, this is done with a function that cycles through each child-count datapoint, multiplying it by itself (thus represending the number of siblings), to create the biased Probability Mass Function.

A function that counts every observsation of siblings, including duplicate counts

```{python}
def BiasPmf(pmf, label):
    new_pmf = pmf.Copy(label=label)

    for x, p in pmf.Items():
        new_pmf.Mult(x, x)

    new_pmf.Normalize()
    return new_pmf
```
Compute the _probability mass function_ for the actual and biased perspectives, as well as the biased mean
```{python}
pmf_children_in_fam = thinkstats2.Pmf(children_in_fam, label='actual')
biased_pmf_children_in_fam = BiasPmf(pmf_children_in_fam, label='observed by children')
biased_mean = biased_pmf_children_in_fam.Mean()
```
### Visualizing

Visualizing the difference between actual and observation-biased distributions
```{python}
thinkplot.PrePlot(2)
thinkplot.Pmfs([pmf_children_in_fam], color='g', alpha=.15)
thinkplot.Pmfs([biased_pmf_children_in_fam], color='r', alpha=.15)
thinkplot.Config(xlabel='number of Children', ylabel='PMF')
thinkplot.axvline(x= children_in_fam_mean, color = 'g', lw=1, alpha=1)
thinkplot.axvline(x= biased_mean, color = 'r', lw=1, alpha=1)
```
![graph of pmf](img/3_1_actual_vs_observed_bias.png)

### comparing means

```{python}
biased_mean = biased_pmf_children_in_fam.Mean()
print("The actual mean number of children: ", children_in_fam_mean)
print("The biased mean number of children: ", biased_mean)
```

Output
```
The actual mean number of children:  1.024205155043831
The biased mean number of children:  2.403679100664282
```

### Discussion of actual mean vs. simulated-bias mean
Whereas the actual mean is 1.02, the biased mean would be 2.40. The difference in distribution can be observed
in the different probability mass functions (green vs. red). We can see that whereas no children is the most
prevalant situation overall, the bias in the simulation puts two children as the most prevelant number (the mode).
As expected, the actual and biased counting agree entirely on the prevelance of single children
because each only-child would count him or herself only, producing no distortion whatsoever in that case.
