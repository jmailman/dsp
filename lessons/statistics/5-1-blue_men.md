[Think Stats Chapter 5 Exercise 1](http://greenteapress.com/thinkstats2/html/thinkstats2006.html#toc50) (blue men)

### Question
**Exercise:** In the BRFSS (see Section 5.4), the distribution of heights is roughly normal with parameters µ = 178 cm and σ = 7.7 cm for men, and µ = 163 cm and σ = 7.3 cm for women.

In order to join Blue Man Group, you have to be male between 5’10” and 6’1” (see http://bluemancasting.com). What percentage of the U.S. male population is in this range? Hint: use `scipy.stats.norm.cdf`.

`scipy.stats` contains objects that represent analytic distributions

### Blue Man Group physical specifications
At one point the _Blue Man Group_ entertainment franchise stipulated they would consider only males between **5'10"** and **6'1"**. (This might still be true but is not currently stated on their website.) A legitimate question is what percentage of the U.S. male population is in this range.

According to the _Behavioral Risk Factor Surveillance System_ (BRFSS), conducted by the National Center for Chronic Disease Prevention and Health Promotion (http://cdc.gov/BRFSS/) the distribution of male heights in the U.S. is roughly normal with a mean of **163 cm** and a standard deviation of **7.7**

To compare the Blue Man Group's specifications against this the general population, we can use the python library `scipy.stats` to generate a variable that is the normal distribution with $\mu$=163 and $\sigma$=7.7. Then we can can see where **5'10"** and **6'1"** lie within this distribution.

For the necessary computations, we'll need only `scipy.stats`

```{python}
import scipy.stats
```
Assign mean and standard deviation to variables to serve as inputs to create a distribution object (a "frozen" infrastructure).

```{python}
mu = 178
sigma = 7.7
dist = scipy.stats.norm(loc=mu, scale=sigma)
type(dist)
```
Output:
```
scipy.stats._distn_infrastructure.rv_frozen
```

As a sanity check, we can verify that this distribution has the mean and std we started with:

```{python}
dist.mean(), dist.std()

```
Output:
```
(178.0, 7.7)
```
We can also use this object to get the _cumulative density function_ (CDF).  Specifically, how many are more than one standard deviation below the mean?  About 16%

```{python}
dist.cdf(mu-sigma)
```
Output:
```
0.1586552539314574
```
To compare the BMG specs with the population, we need to convert from feet and inches into centemeters.
```{python}
def ft_inch_to_cm(ft, inches):
    return (ft*12 + inches) * 2.54
```
Output (percentile for **lower** end of BMG range):
```
0.48963902786483265
```


```{python}
blueMG_high = dist.cdf( ft_inch_to_cm(6, 1) )
blueMG_high

```
Output (percentile for **higher** end of BMG range):
```
0.8323858654963072
```

To discover what proportion of the population is within Blue Man Group's range, we subtract the lower cumulative density from the higher:


```{python}
blueMG_high - blueMG_low

```

Output (the difference):
```
0.34274683763147457
```

5'10" is taller than 48% of males and 6'1" is taller than 82% of U.S. males. The difference in percentages is the percentage that lies in the range from 5'10" to 6'1", and this is **34%** (of the male population in the U.S.)

