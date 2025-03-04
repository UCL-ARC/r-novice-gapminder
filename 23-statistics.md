---
title: "Basic Statistics: describing, modelling and reporting"
teaching: 60
exercises: 20
source: Rmd
---




::::::::::::::::::::::::::::::::::::::: objectives

- To be able to describe the different types of data
- To be able to do basic data exploration of a real dataset
- To be able to calculate descriptive statistics
- To be able to perform statistical inference on a dataset

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I detect the type of data I have?
- How can I make meaningful summaries of my data?

::::::::::::::::::::::::::::::::::::::::::::::::::

## Content

-   Types of Data
-   Exploring your dataset
-   Descriptive Statistics
-   Inferential Statistics

## Data


``` r
# We will need these libraries and this data later.
library(tidyverse)
library(ggplot2)

# loading data
lon_dims_imd_2019 <- read.csv("data/English_IMD_2019_Domains_rebased_London_by_CDRC.csv")

# create a binary membership variable for City of London (for later examples)
lon_dims_imd_2019 <- lon_dims_imd_2019 %>% mutate(city = la19nm == "City of London")
```

We are going to use the data from the Consumer Data Research Centre, specifically the London IMD 2019 (English IMD 2019 Domains rebased) data.
Atribution: Data provided by the Consumer Data Research Centre, an ESRC Data Investment: ES/L011840/1, ES/L011891/1

The statistical unit areas used to provide indices of relative deprivation across the country are Lower layer Super Output Areas (LSOAs), dimensions of depravation include income, employment, education, health, crime, barriers to housing and services, and the living environment.
We have added a variable *city* indicating if an LSOA is within the City of London, or not.

## The big picture

-   Research often seeks to answer a question about a larger population by collecting data on a small sample
-   Data collection:
    -   Many variables
    -   For each person/unit.
-   This procedure, *sampling*, must be controlled so as to ensure **representative** data.

## Descriptive and inferential statistics

::: callout
Just as data in general are of different types - for example numeric vs text data - statistical data are assigned to different *levels of measure*. The level of measure determines how we can describe and model the data.
:::

# Describing data

-   Continuous variables
-   Discrete variables

::: callout
How do we convey information on what your data looks like, using numbers or figures?
:::

### Describing continuous data.

First establish the distribution of the data. You can visualise this with a histogram.


``` r
ggplot(lon_dims_imd_2019, aes(x = barriers_london_rank)) +
  geom_histogram()
```

``` output
`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="fig/23-statistics-rendered-unnamed-chunk-2-1.png" style="display: block; margin: auto;" />

What is the distribution of this data?

### What is the distribution of population?

The raw values are difficult to visualise, so we can take the log of the values and log those.  Try this command


``` r
ggplot(lon_dims_imd_2019, aes(x = log(barriers_london_rank))) +
  geom_histogram()
```

``` output
`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="fig/23-statistics-rendered-unnamed-chunk-3-1.png" style="display: block; margin: auto;" />

What is the distribution of this data?

## Parametric vs non-parametric analysis

-   Parametric analysis assumes that
    -   The data follows a known distribution
    -   It can be described using *parameters*
    -   Examples of distributions include, normal, Poisson, exponential.
-   Non parametric data
    -   The data can't be said to follow a known distribution

::::::::::::::::::::::::::::::::::::: instructor
Emphasise that parametric is not equal to normal.
::::::::::::::::::::::::::::::::::::::::::::::::

### Describing parametric and non-parametric data

How do you use numbers to convey what your data looks like.

-   Parametric data
    -   Use the parameters that describe the distribution.
    -   For a Gaussian (normal) distribution - use mean and standard deviation
    -   For a Poisson distribution - use average event rate
    -   etc.
-   Non Parametric data
    -   Use the median (the middle number when they are ranked from lowest to highest) and the interquartile range (the number 75% of the way up the list when ranked minus the number 25% of the way)
-   You can use the command `summary(data_frame_name)` to get these numbers for each variable.

## Mean versus standard deviation

-   What does standard deviation mean?
-   Both graphs have the same mean (center), but the second one has data which is more spread out.


``` r
# small standard deviation
dummy_1 <- rnorm(1000, mean = 10, sd = 0.5)
dummy_1 <- as.data.frame(dummy_1)
ggplot(dummy_1, aes(x = dummy_1)) +
  geom_histogram()
```

``` output
`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="fig/23-statistics-rendered-unnamed-chunk-4-1.png" style="display: block; margin: auto;" />

``` r
# larger standard deviation
dummy_2 <- rnorm(1000, mean = 10, sd = 200)
dummy_2 <- as.data.frame(dummy_2)
ggplot(dummy_2, aes(x = dummy_2)) +
  geom_histogram()
```

``` output
`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="fig/23-statistics-rendered-unnamed-chunk-4-2.png" style="display: block; margin: auto;" />

::::::::::::::::::::::::::::::::::::: instructor
Get them to plot the graphs. Explain that we are generating random data from different distributions and plotting them.
::::::::::::::::::::::::::::::::::::::::::::::::

### Calculating mean and standard deviation


``` r
mean(lon_dims_imd_2019$barriers_london_rank, na.rm = TRUE)
```

``` output
[1] 2418
```

Calculate the standard deviation and confirm that it is the square root of the variance:


``` r
sdbarriers <- sd(lon_dims_imd_2019$barriers_london_rank, na.rm = TRUE)
print(sdbarriers)
```

``` output
[1] 1395.889
```

``` r
varbarriers <- var(lon_dims_imd_2019$barriers_london_rank, na.rm = TRUE)
print(varbarriers)
```

``` output
[1] 1948505
```

``` r
sqrt(varbarriers) == sdbarriers
```

``` output
[1] TRUE
```

The `na.rm` argument tells R to ignore missing values in the variable.

### Calculating median and interquartile range


``` r
median(lon_dims_imd_2019$barriers_london_rank, na.rm = TRUE)
```

``` output
[1] 2418
```


``` r
IQR(lon_dims_imd_2019$barriers_london_rank, na.rm = TRUE)
```

``` output
[1] 2417
```

Again, we ignore the missing values.

## Describing discrete data

-   Frequencies


``` r
table(lon_dims_imd_2019$la19nm)
```

``` output

  Barking and Dagenham                 Barnet                 Bexley 
                   110                    211                    146 
                 Brent                Bromley                 Camden 
                   173                    197                    133 
        City of London                Croydon                 Ealing 
                     6                    220                    196 
               Enfield              Greenwich                Hackney 
                   183                    151                    144 
Hammersmith and Fulham               Haringey                 Harrow 
                   113                    145                    137 
              Havering             Hillingdon               Hounslow 
                   150                    161                    142 
             Islington Kensington and Chelsea   Kingston upon Thames 
                   123                    103                     98 
               Lambeth               Lewisham                 Merton 
                   178                    169                    124 
                Newham              Redbridge   Richmond upon Thames 
                   164                    161                    115 
             Southwark                 Sutton          Tower Hamlets 
                   166                    121                    144 
        Waltham Forest             Wandsworth            Westminster 
                   144                    179                    128 
```

-   Proportions


``` r
areastable <- table(lon_dims_imd_2019$la19nm)
prop.table(areastable)
```

``` output

  Barking and Dagenham                 Barnet                 Bexley 
           0.022750776            0.043640124            0.030196484 
                 Brent                Bromley                 Camden 
           0.035780765            0.040744571            0.027507756 
        City of London                Croydon                 Ealing 
           0.001240951            0.045501551            0.040537746 
               Enfield              Greenwich                Hackney 
           0.037849018            0.031230610            0.029782834 
Hammersmith and Fulham               Haringey                 Harrow 
           0.023371251            0.029989659            0.028335057 
              Havering             Hillingdon               Hounslow 
           0.031023785            0.033298862            0.029369183 
             Islington Kensington and Chelsea   Kingston upon Thames 
           0.025439504            0.021302999            0.020268873 
               Lambeth               Lewisham                 Merton 
           0.036814891            0.034953464            0.025646329 
                Newham              Redbridge   Richmond upon Thames 
           0.033919338            0.033298862            0.023784902 
             Southwark                 Sutton          Tower Hamlets 
           0.034332989            0.025025853            0.029782834 
        Waltham Forest             Wandsworth            Westminster 
           0.029782834            0.037021717            0.026473630 
```

Contingency tables of frequencies can also be tabulated with **table()**. For example:


``` r
table(
  lon_dims_imd_2019$la19nm,
  lon_dims_imd_2019$IDAOP_london_decile
)
```

``` output
                        
                          1  2  3  4  5  6  7  8  9 10
  Barking and Dagenham    6 11 23 25 22 12  7  4  0  0
  Barnet                  6  7 13 15 18 32 29 37 29 25
  Bexley                  0  3  2  5 11 11 15 24 30 45
  Brent                  12 19 24 28 43 18 17 11  1  0
  Bromley                 2  3  6  9 10 12 20 19 41 75
  Camden                 12 19 14 18 14 10  9 16 11 10
  City of London          0  0  1  0  0  0  0  1  0  4
  Croydon                 8  7 16 25 23 20 29 24 29 39
  Ealing                 11 18 22 23 24 23 31 21 18  5
  Enfield                 9 19 27 22 26 17 16 22 18  7
 [ reached getOption("max.print") -- omitted 23 rows ]
```

Which leads quite naturally to the consideration of any association between the observed frequencies.

# Inferential statistics

## Meaningful analysis

-   What is your hypothesis - what is your null hypothesis?

::: callout
Always: the level of the independent variable has no effect on the level of the dependent variable.
:::

-   What type of variables (data type) do you have?

-   What are the assumptions of the test you are using?

-   Interpreting the result

## Testing significance

-   p-value

-   \<0.05

-   0.03-0.049

    -   Would benefit from further testing.

**0.05** is not a magic number.

## Comparing means

It all starts with a hypothesis

-   Null hypothesis
    -   "There is no difference in mean height between men and women" $$mean\_height\_men - mean\_height\_women = 0$$
-   Alternate hypothesis
    -   "There is a difference in mean height between men and women"

## More on hypothesis testing

-   The null hypothesis (H0) assumes that the true mean difference (μd) is equal to zero.

-   The two-tailed alternative hypothesis (H1) assumes that μd is not equal to zero.

-   The upper-tailed alternative hypothesis (H1) assumes that μd is greater than zero.

-   The lower-tailed alternative hypothesis (H1) assumes that μd is less than zero.

-   Remember: hypotheses are never about data, they are about the processes which produce the data. The value of μd is unknown. The goal of hypothesis testing is to determine the hypothesis (null or alternative) with which the data are more consistent.

## Comparing means

Is there an absolute difference between the income ranks of the Lower-layer Super Output Areas?


``` r
lon_dims_imd_2019 %>%
  group_by(la19nm) %>%
  summarise(avg = mean(Income_london_rank)) %>%
  arrange(la19nm, .locale = "en")
```

``` output
# A tibble: 33 × 2
   la19nm                  avg
   <chr>                 <dbl>
 1 Barking and Dagenham  7786.
 2 Barnet               17049.
 3 Bexley               18592.
 4 Brent                11500.
 5 Bromley              20826.
 6 Camden               14359.
 7 City of London       19800.
 8 Croydon              14686.
 9 Ealing               13718.
10 Enfield              11403.
# ℹ 23 more rows
```


Is the difference between the income ranks statistically significant?

## t-test

### Assumptions of a t-test

-   One independent categorical variable with 2 groups and one dependent continuous variable

-   The dependent variable is approximately normally distributed in each group

-   The observations are independent of each other

-   For students' original t-statistic, that the variances in both groups are more or less equal. This constraint should probably be abandoned in favour of always using a conservative test.

## Doing a t-test


``` r
t.test(health_london_rank ~ city, data = lon_dims_imd_2019)$statistic
```

``` output
         t 
-0.5183242 
```

``` r
t.test(health_london_rank ~ city, data = lon_dims_imd_2019)$parameter
```

``` output
      df 
5.015827 
```

Notice that the summary()** of the test contains more data than is output by default.


Write a paragraph in markdown format reporting this test result including the t-statistic, the degrees of freedom, the confidence interval and the p-value to 4 places.  To do this include your r code **inline** with your text, rather than in an R code chunk.

### t-test result

Testing supported the rejection of the null hypothesis that there is no difference between mean health rank of City of London and non-City of London areas (**t**=-0.5183, **df**= 5.0158,
**p**= 0.6263).

(Can you get p to display to four places?  Cf *format()*.)

## More than two levels of IV

While the t-test is sufficient where there are two levels of the IV, for situations where there are more than two, we use the **ANOVA** family of procedures. To show this, we will create a variable that subsets our data by *per capita GDP* levels. If the ANOVA result is statistically significant, we will use a post-hoc test method to do pairwise comparisons (here Tukey's Honest Significant Differences.)


``` r
anovamodel <- aov(lon_dims_imd_2019$health_london_rank ~ lon_dims_imd_2019$la19nm)
summary(anovamodel)
```

``` output
                           Df    Sum Sq   Mean Sq F value Pr(>F)    
lon_dims_imd_2019$la19nm   32 1.156e+11 3.614e+09    94.3 <2e-16 ***
Residuals                4802 1.840e+11 3.832e+07                   
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

``` r
TukeyHSD(anovamodel)
```

``` output
  Tukey multiple comparisons of means
    95% family-wise confidence level

Fit: aov(formula = lon_dims_imd_2019$health_london_rank ~ lon_dims_imd_2019$la19nm)

$`lon_dims_imd_2019$la19nm`
                                                      diff           lwr
Barnet-Barking and Dagenham                    14629.43408  11864.704458
Bexley-Barking and Dagenham                    10324.16189   7356.018133
Brent-Barking and Dagenham                      8976.53678   6109.643394
Bromley-Barking and Dagenham                   13160.91347  10362.721854
Camden-Barking and Dagenham                     8842.99282   5813.159809
City of London-Barking and Dagenham             8728.87879  -1126.986222
Croydon-Barking and Dagenham                    5472.01364   2726.731357
Ealing-Barking and Dagenham                     7444.55056   4643.802426
Enfield-Barking and Dagenham                    9856.81595   7020.532420
Greenwich-Barking and Dagenham                  3308.37658    361.423935
Hackney-Barking and Dagenham                   -2303.09343  -5280.080786
Hammersmith and Fulham-Barking and Dagenham     3374.21360    225.344643
Haringey-Barking and Dagenham                   4977.28683   2004.748485
Harrow-Barking and Dagenham                    14760.22064  11750.476548
Havering-Barking and Dagenham                   9781.09212   6830.002345
Hillingdon-Barking and Dagenham                 7988.28148   5080.156381
Hounslow-Barking and Dagenham                   6633.44686   3647.394114
Islington-Barking and Dagenham                  -824.36918  -3909.451677
Kensington and Chelsea-Barking and Dagenham    12368.11342   9144.725551
Kingston upon Thames-Barking and Dagenham      14113.29035  10847.712764
Lambeth-Barking and Dagenham                     672.68029  -2178.519304
Lewisham-Barking and Dagenham                   2032.14605   -847.904610
Merton-Barking and Dagenham                    10555.97287   7476.768867
Newham-Barking and Dagenham                     3111.49058    214.182117
Redbridge-Barking and Dagenham                 12151.31254   9243.187437
                                                       upr     p adj
Barnet-Barking and Dagenham                    17394.16370 0.0000000
Bexley-Barking and Dagenham                    13292.30565 0.0000000
Brent-Barking and Dagenham                     11843.43017 0.0000000
Bromley-Barking and Dagenham                   15959.10510 0.0000000
Camden-Barking and Dagenham                    11872.82584 0.0000000
City of London-Barking and Dagenham            18584.74380 0.1877166
Croydon-Barking and Dagenham                    8217.29592 0.0000000
Ealing-Barking and Dagenham                    10245.29869 0.0000000
Enfield-Barking and Dagenham                   12693.09947 0.0000000
Greenwich-Barking and Dagenham                  6255.32923 0.0085802
Hackney-Barking and Dagenham                     673.89392 0.4778538
Hammersmith and Fulham-Barking and Dagenham     6523.08255 0.0185918
Haringey-Barking and Dagenham                   7949.82518 0.0000001
Harrow-Barking and Dagenham                    17769.96473 0.0000000
Havering-Barking and Dagenham                  12732.18190 0.0000000
Hillingdon-Barking and Dagenham                10896.40658 0.0000000
Hounslow-Barking and Dagenham                   9619.49961 0.0000000
Islington-Barking and Dagenham                  2260.71332 1.0000000
Kensington and Chelsea-Barking and Dagenham    15591.50128 0.0000000
Kingston upon Thames-Barking and Dagenham      17378.86794 0.0000000
Lambeth-Barking and Dagenham                    3523.87988 1.0000000
Lewisham-Barking and Dagenham                   4912.19670 0.6922986
Merton-Barking and Dagenham                    13635.17688 0.0000000
Newham-Barking and Dagenham                     6008.79904 0.0179605
Redbridge-Barking and Dagenham                 15059.43763 0.0000000
 [ reached getOption("max.print") -- omitted 503 rows ]
```

# Regression Modelling

The most common use of regression modelling is to explore the relationship between two continuous variables, for example between `Income_london_rank` and `health_london_rank` in our data. We can first determine whether there is any significant correlation between the values, and if there is, plot the relationship.


``` r
cor.test(lon_dims_imd_2019$Income_london_rank, lon_dims_imd_2019$health_london_rank)
```

``` output

	Pearson's product-moment correlation

data:  lon_dims_imd_2019$Income_london_rank and lon_dims_imd_2019$health_london_rank
t = 92.907, df = 4833, p-value < 2.2e-16
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 0.7903110 0.8105571
sample estimates:
      cor 
0.8006626 
```

``` r
ggplot(lon_dims_imd_2019, aes(Income_london_rank, health_london_rank)) +
  geom_point() +
  geom_smooth()
```

``` output
`geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'
```

<img src="fig/23-statistics-rendered-unnamed-chunk-15-1.png" style="display: block; margin: auto;" />

Having decided that a further investigation of this relationship is worthwhile, we can create a linear model with the function `lm()`.


``` r
modelone <- lm(lon_dims_imd_2019$Income_london_rank ~ lon_dims_imd_2019$health_london_rank)
summary(modelone)
```

``` output

Call:
lm(formula = lon_dims_imd_2019$Income_london_rank ~ lon_dims_imd_2019$health_london_rank)

Residuals:
   Min     1Q Median     3Q    Max 
-15354  -3547   -102   3458  24528 

Coefficients:
                                       Estimate Std. Error t value Pr(>|t|)    
(Intercept)                          -3.223e+03  2.039e+02  -15.80   <2e-16 ***
lon_dims_imd_2019$health_london_rank  8.634e-01  9.293e-03   92.91   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 5087 on 4833 degrees of freedom
Multiple R-squared:  0.6411,	Adjusted R-squared:  0.641 
F-statistic:  8632 on 1 and 4833 DF,  p-value: < 2.2e-16
```

## Regression with a categorical IV (the t-test)

Run the following code chunk and compare the results to the t-test conducted earlier.


``` r
lon_dims_imd_2019 %>%
  mutate(city = factor(city))
```

``` output
     ls11cd         la19nm IDAOP_london_rank IDAOP_london_decile
1 E01000001 City of London            32,820                  10
2 E01000002 City of London            31,938                  10
3 E01000003 City of London            16,377                   8
4 E01000005 City of London             3,885                   3
  IDACI_london_rank IDACI_london_decile Income_london_rank Income_london_decile
1             32806                  10              32831                   10
2             29682                  10              29901                   10
3             27063                   9              18510                    7
4              9458                   4               6029                    2
  employment_london_rank employment_london_decile crime_london_rank
1                  32742                       10             32662
2                  31190                       10             32789
3                  15103                        5             29363
4                   7833                        2             31059
  crime_london_decile barriers_london_rank barriers_london_decile
1                  10                 2679                      6
2                  10                 3645                      8
3                  10                  984                      3
4                  10                 1003                      3
  livingEnv_london_rank livingEnv_london_decile health_london_rank
1                  7789                       4              32113
2                 13070                       7              29705
3                  4092                       2              17600
4                  9397                       5              17907
  health_london_decile edu_london_rank edu_london_decile city
1                   10           32842                10 TRUE
2                    9           32832                10 TRUE
3                    4           26386                 8 TRUE
4                    4           12370                 2 TRUE
 [ reached 'max' / getOption("max.print") -- omitted 4831 rows ]
```

``` r
modelttest <- lm(lon_dims_imd_2019$health_london_rank ~ lon_dims_imd_2019$city)

summary(modelttest)
```

``` output

Call:
lm(formula = lon_dims_imd_2019$health_london_rank ~ lon_dims_imd_2019$city)

Residuals:
     Min       1Q   Median       3Q      Max 
-19251.6  -6392.6    367.4   6853.4  12362.4 

Coefficients:
                           Estimate Std. Error t value Pr(>|t|)    
(Intercept)                 20481.6      113.3  180.75   <2e-16 ***
lon_dims_imd_2019$cityTRUE   1478.2     3216.6    0.46    0.646    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 7874 on 4833 degrees of freedom
Multiple R-squared:  4.37e-05,	Adjusted R-squared:  -0.0001632 
F-statistic: 0.2112 on 1 and 4833 DF,  p-value: 0.6458
```

## Regression with a categorical IV (ANOVA)

Use the `lm()` function to model the relationship between `lon_dims_imd_2019$la19nm`
and `lon_dims_imd_2019$health_london_rank`. Compare the results with the ANOVA carried out earlier.

## Break

-   We will explore regression models in more detail during the rest of the day
