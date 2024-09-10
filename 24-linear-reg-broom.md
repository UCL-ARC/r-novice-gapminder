---
title: "Linear regression and Broom"
teaching: 80
exercises: 20
source: Rmd
editor_options: 
  markdown: 
    wrap: sentence
---

::: objectives
-   To be able to explore relationships between variables
-   To be able to calculate predicted variables and residuals
-   To be able to construct linear regression models
-   To be able to present model outcomes using Broom
:::

::: questions
-   How can I explore relationships between variables in my data?
-   How can I present model outputs in an easier to read way?
:::

## Content

-   Linear Regression Models
-   Use of Log transform
-   Use of Categorical Variables
-   Use of Broom

## Data


``` r
# We will need these libraries and this data later.
library(ggplot2)
library(tidyverse)
library(lmtest)
library(sandwich)
library(broom)

lon_dims_imd_2019 <- read.csv("data/English_IMD_2019_Domains_rebased_London_by_CDRC.csv")
```

We are going to use the data from the Consumer Data Research Centre, specifically the London IMD 2019 (English IMD 2019 Domains rebased).

Atribution: Data provided by the Consumer Data Research Centre, an ESRC Data Investment: ES/L011840/1, ES/L011891/1

The statistical unit areas across the country are Lower layer Super Output Areas (LSOAs).
We will explore the relationships between the different dimensions of the Indices of Multiple Deprivation.

## Linear regression

Linear Regression enables use to to explore the the linear relationship of the dependent variable Y and independent variable(s) X(s).
We are going to explore the linear relationship between the Health Deprivation and Disability Domain and the Living Environment Deprivation Domain.

The Health Deprivation and Disability Domain measures the risk of premature death and the impairment of quality of life through poor physical or mental health.
The domain measures morbidity, disability and premature mortality but not aspects of behaviour or environment that may be predictive of future health deprivation.

The Living Environment Deprivation Domain measures the quality of the local environment.
The indicators fall into two sub-domains.
The ‘indoors’ living environment measures the quality of housing; while the ‘outdoors’ living environment contains measures of air quality and road traffic accidents.

Reference: McLennan, David et al.
The English Indices of Deprivation 2019 : Technical Report.
Ministry of Housing, Communities and Local Government, 2019.
Print.

### Simple linear regression

In the simple linear regression example we have only one dependent variable (health_london_rank) and one independent variable (livingEnv_london_rank).


``` r
reg_LivEnv_health <- lm(health_london_rank ~ livingEnv_london_rank, data = lon_dims_imd_2019)
# We put the dependent variable to the left of the '~' and the independent variable(s) to the right
# and we tell R which dataset we are referring to.

summary(reg_LivEnv_health)
```

``` output

Call:
lm(formula = health_london_rank ~ livingEnv_london_rank, data = lon_dims_imd_2019)

Residuals:
     Min       1Q   Median       3Q      Max 
-21549.6  -5948.2    609.1   6239.2  15792.2 

Coefficients:
                       Estimate Std. Error t value Pr(>|t|)    
(Intercept)           1.692e+04  2.274e+02   74.40   <2e-16 ***
livingEnv_london_rank 3.430e-01  1.915e-02   17.91   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 7625 on 4833 degrees of freedom
Multiple R-squared:  0.06225,	Adjusted R-squared:  0.06205 
F-statistic: 320.8 on 1 and 4833 DF,  p-value: < 2.2e-16
```

From the result of this analysis, we can see that the Living Environment Deprivation Domain rank has a significant(small p-value, general rule of thumb \<0.05) and positive relationship(positive coefficient) with the Health Deprivation and Disability Domain rank.

One way of interpreting the result is: One unit increase in the Living Environment rank is related to around 0.343 (3.430e-01) points increase of the Health Deprivation and Disability rank.

R-square shows the amount of variance of Y explained by X.
In this case the Living Environment rank explains 6.225% of the variance in the Health Deprivation and Disability rank.
Adj R2(6.205%) shows the same as R2 but adjusted by the \# of cases and \# of variables.
When the \# of variables is small and the \# of cases is very large then Adj R2 is closer to R2.

### Log transform

If your data is skewed, it can be useful to transform a variable to it's log form when doing the regression.
You can either transform the variable beforehand or do so in the equation.


``` r
reg_logbarriers_health <- lm(health_london_rank ~ log(barriers_london_rank), data = lon_dims_imd_2019)

summary(reg_logbarriers_health)
```

``` output

Call:
lm(formula = health_london_rank ~ log(barriers_london_rank), 
    data = lon_dims_imd_2019)

Residuals:
     Min       1Q   Median       3Q      Max 
-20379.3  -5611.7    774.9   5988.6  23828.5 

Coefficients:
                          Estimate Std. Error t value Pr(>|t|)    
(Intercept)                -1349.4      798.3   -1.69    0.091 .  
log(barriers_london_rank)   2917.0      105.7   27.59   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 7319 on 4833 degrees of freedom
Multiple R-squared:  0.1361,	Adjusted R-squared:  0.1359 
F-statistic: 761.1 on 1 and 4833 DF,  p-value: < 2.2e-16
```

The interpretation of the log-transformed variable is a bit different.
In this example only the predictor variable is log tranformed, therefore to interpret the slope coefficient we divide it by 100 (2917.0/100=29.170).

If the dependent/response variable is solely log-transformed, exponentiate the coefficient.
This gives the multiplicative factor for every one-unit increase in the independent variable.
Example: the coefficient is 0.198.
exp(0.198) = 1.218962.
For every one-unit increase in the independent variable, our dependent variable increases by a factor of about 1.22, or 22%.
Recall that multiplying a number by 1.22 is the same as increasing the number by 22%.
Likewise, multiplying a number by, say 0.84, is the same as decreasing the number by 1 – 0.84 = 0.16, or 16%.

If both are transformed, interpret the coefficient as the percent increase in the dependent variable for every 1% increase in the independent variable.
Example: the coefficient is 0.198.
For every 1% increase in the independent variable, our dependent variable increases by about 0.20%.
For x percent increase, calculate 1.x to the power of the coefficient, subtract 1, and multiply by 100.
Example: For every 20% increase in the independent variable, our dependent variable increases by about (1.20 0.198 - 1) \* 100 = 3.7 percent.

### Predicted values and residuals

We can expand our simple linear regression example to incorporate the Barriers to Housing and Services Domain rank.
The Barriers to Housing and Services Domain measures the physical and financial accessibility of housing and local services.
The indicators fall into two sub-domains: ‘geographical barriers’, which relate to the physical proximity of local services, and ‘wider barriers’ which includes issues relating to access to housing, such as affordability.


``` r
reg_LivEnv_barriers_health <- lm(
  health_london_rank ~ livingEnv_london_rank + barriers_london_rank,
  data = lon_dims_imd_2019
)

summary(reg_LivEnv_barriers_health)
```

``` output

Call:
lm(formula = health_london_rank ~ livingEnv_london_rank + barriers_london_rank, 
    data = lon_dims_imd_2019)

Residuals:
     Min       1Q   Median       3Q      Max 
-21685.8  -4834.9    546.5   5142.4  18971.7 

Coefficients:
                       Estimate Std. Error t value Pr(>|t|)    
(Intercept)           1.169e+04  2.502e+02   46.74   <2e-16 ***
livingEnv_london_rank 2.620e-01  1.721e-02   15.22   <2e-16 ***
barriers_london_rank  2.508e+00  7.059e-02   35.54   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 6790 on 4832 degrees of freedom
Multiple R-squared:  0.2566,	Adjusted R-squared:  0.2562 
F-statistic: 833.7 on 2 and 4832 DF,  p-value: < 2.2e-16
```

After running the regression model, we can access the model predicted values and the residuals compared to the real observations.


``` r
# first we fit the predictions
health_rank_pred <- fitted(reg_LivEnv_barriers_health)
health_rank_pred <- as.data.frame(health_rank_pred)

# now we add the residual values too
health_rank_resid <- residuals(reg_LivEnv_barriers_health)
health_rank_pred$resid <- health_rank_resid

# We can thenview the predictions and residuals
head(health_rank_pred)
```

``` output
  health_rank_pred     resid
1         20454.30 11658.702
2         24260.97  5444.031
3         15233.92  2366.080
4         16671.36  1235.645
5         15719.80  5861.196
6         14981.47  1432.528
```


``` r
# You can view the full data in RStudio with the View() function
View(health_rank_pred)
```

### Robust regression

We can run the robust standard error regressions(control for heteroskedasticity, meaning unequal variances):


``` r
reg_LivEnv_barriers_health$robse <- vcovHC(reg_LivEnv_barriers_health, type = "HC1")
coeftest(reg_LivEnv_barriers_health, reg_LivEnv_barriers_health$robse)
```

``` output

t test of coefficients:

                        Estimate Std. Error t value  Pr(>|t|)    
(Intercept)           1.1694e+04 2.3776e+02  49.182 < 2.2e-16 ***
livingEnv_london_rank 2.6197e-01 1.7458e-02  15.006 < 2.2e-16 ***
barriers_london_rank  2.5085e+00 6.4394e-02  38.955 < 2.2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

In addition, we can access the cluster-robust standard errors regression results:


``` r
# cluster-robust standard errors
coeftest(reg_LivEnv_barriers_health, reg_LivEnv_barriers_health$clse)
```

``` output

t test of coefficients:

                        Estimate Std. Error t value  Pr(>|t|)    
(Intercept)           1.1694e+04 2.5018e+02  46.741 < 2.2e-16 ***
livingEnv_london_rank 2.6197e-01 1.7207e-02  15.225 < 2.2e-16 ***
barriers_london_rank  2.5085e+00 7.0588e-02  35.537 < 2.2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

::: challenge
## Challenge 1

Use the `gapminder` data to create a linear model between two continuous variables.

Discuss your question and your findings.
:::

### Regression with categorical independent variables

We will explore the use of categorical independent variables in linear regression in this episode.
When the dependent variable is a categorical variable, you may consider the alternatives of linear regression like logit regression and multinomial regression.


``` r
# As a categorical variable we have added la19nm, these are the names of the London boroughs
reg_cat_var <- lm(health_london_rank ~ livingEnv_london_rank + barriers_london_rank + la19nm, data = lon_dims_imd_2019)

summary(reg_cat_var)
```

``` output

Call:
lm(formula = health_london_rank ~ livingEnv_london_rank + barriers_london_rank + 
    la19nm, data = lon_dims_imd_2019)

Residuals:
     Min       1Q   Median       3Q      Max 
-21694.3  -3423.6    248.1   3691.8  19321.5 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   1.105e+04  5.429e+02  20.346  < 2e-16 ***
livingEnv_london_rank         3.595e-02  1.802e-02   1.996 0.046036 *  
barriers_london_rank          3.149e+00  7.888e-02  39.924  < 2e-16 ***
la19nmBarnet                  8.400e+03  6.508e+02  12.908  < 2e-16 ***
la19nmBexley                  1.416e+03  7.186e+02   1.970 0.048856 *  
la19nmBrent                   7.765e+03  6.545e+02  11.863  < 2e-16 ***
la19nmBromley                 5.208e+03  6.789e+02   7.670 2.06e-14 ***
la19nmCamden                 -1.842e+03  7.426e+02  -2.480 0.013184 *  
la19nmCity of London          4.742e+03  2.251e+03   2.107 0.035185 *  
la19nmCroydon                 7.488e+02  6.388e+02   1.172 0.241192    
la19nmEaling                  3.697e+03  6.459e+02   5.724 1.11e-08 ***
la19nmEnfield                 7.344e+03  6.501e+02  11.297  < 2e-16 ***
la19nmGreenwich              -2.221e+03  6.873e+02  -3.232 0.001238 ** 
la19nmHackney                -2.725e+03  6.811e+02  -4.000 6.43e-05 ***
la19nmHammersmith and Fulham -3.907e+03  7.438e+02  -5.252 1.57e-07 ***
la19nmHaringey                1.019e+03  6.877e+02   1.481 0.138580    
la19nmHarrow                  9.993e+03  7.053e+02  14.168  < 2e-16 ***
la19nmHavering               -3.945e+02  7.317e+02  -0.539 0.589781    
la19nmHillingdon              8.459e+02  6.937e+02   1.219 0.222791    
la19nmHounslow                2.462e+03  6.890e+02   3.573 0.000356 ***
la19nmIslington              -8.217e+03  7.305e+02 -11.248  < 2e-16 ***
la19nmKensington and Chelsea  9.191e+03  7.473e+02  12.299  < 2e-16 ***
la19nmKingston upon Thames    4.899e+03  7.804e+02   6.277 3.75e-10 ***
la19nmLambeth                -6.270e+03  6.830e+02  -9.180  < 2e-16 ***
la19nmLewisham               -1.991e+03  6.675e+02  -2.983 0.002869 ** 
la19nmMerton                  3.387e+02  7.474e+02   0.453 0.650434    
la19nmNewham                  4.209e+03  6.617e+02   6.361 2.19e-10 ***
la19nmRedbridge               4.420e+03  6.914e+02   6.392 1.79e-10 ***
la19nmRichmond upon Thames    4.469e+03  7.740e+02   5.774 8.22e-09 ***
la19nmSouthwark              -3.363e+03  6.731e+02  -4.996 6.05e-07 ***
la19nmSutton                 -1.729e+02  7.537e+02  -0.229 0.818563    
la19nmTower Hamlets          -5.862e+03  6.968e+02  -8.414  < 2e-16 ***
la19nmWaltham Forest          2.026e+03  6.854e+02   2.956 0.003137 ** 
la19nmWandsworth              1.087e+01  6.797e+02   0.016 0.987246    
la19nmWestminster             5.683e+02  7.472e+02   0.761 0.446958    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 5362 on 4800 degrees of freedom
Multiple R-squared:  0.5395,	Adjusted R-squared:  0.5363 
F-statistic: 165.4 on 34 and 4800 DF,  p-value: < 2.2e-16
```

R automatically recognizes la19nm as a factor and treats it accordingly.
The missing one in the coefficient summary (Barking and Dagenham) is treated as a base line, therefore the value is 0.
However, we can also modify our model to show for all:


``` r
reg_cat_var_showall <- lm(
  health_london_rank ~ 0 + livingEnv_london_rank + barriers_london_rank + la19nm,
  data = lon_dims_imd_2019
)

summary(reg_cat_var_showall)
```

``` output

Call:
lm(formula = health_london_rank ~ 0 + livingEnv_london_rank + 
    barriers_london_rank + la19nm, data = lon_dims_imd_2019)

Residuals:
     Min       1Q   Median       3Q      Max 
-21694.3  -3423.6    248.1   3691.8  19321.5 

Coefficients:
                              Estimate Std. Error t value Pr(>|t|)    
livingEnv_london_rank        3.595e-02  1.802e-02   1.996    0.046 *  
barriers_london_rank         3.149e+00  7.888e-02  39.924  < 2e-16 ***
la19nmBarking and Dagenham   1.105e+04  5.429e+02  20.346  < 2e-16 ***
la19nmBarnet                 1.945e+04  4.764e+02  40.820  < 2e-16 ***
la19nmBexley                 1.246e+04  5.876e+02  21.209  < 2e-16 ***
la19nmBrent                  1.881e+04  4.526e+02  41.564  < 2e-16 ***
la19nmBromley                1.625e+04  5.444e+02  29.858  < 2e-16 ***
la19nmCamden                 9.205e+03  5.783e+02  15.918  < 2e-16 ***
la19nmCity of London         1.579e+04  2.198e+03   7.184 7.78e-13 ***
la19nmCroydon                1.180e+04  4.574e+02  25.789  < 2e-16 ***
la19nmEaling                 1.474e+04  4.388e+02  33.601  < 2e-16 ***
la19nmEnfield                1.839e+04  4.549e+02  40.431  < 2e-16 ***
la19nmGreenwich              8.825e+03  5.233e+02  16.865  < 2e-16 ***
la19nmHackney                8.322e+03  4.675e+02  17.802  < 2e-16 ***
la19nmHammersmith and Fulham 7.140e+03  5.675e+02  12.582  < 2e-16 ***
la19nmHaringey               1.207e+04  4.848e+02  24.886  < 2e-16 ***
la19nmHarrow                 2.104e+04  5.667e+02  37.127  < 2e-16 ***
la19nmHavering               1.065e+04  6.178e+02  17.243  < 2e-16 ***
la19nmHillingdon             1.189e+04  5.541e+02  21.464  < 2e-16 ***
la19nmHounslow               1.351e+04  5.050e+02  26.750  < 2e-16 ***
la19nmIslington              2.830e+03  5.500e+02   5.145 2.78e-07 ***
la19nmKensington and Chelsea 2.024e+04  5.491e+02  36.859  < 2e-16 ***
la19nmKingston upon Thames   1.595e+04  6.476e+02  24.624  < 2e-16 ***
la19nmLambeth                4.777e+03  4.657e+02  10.257  < 2e-16 ***
la19nmLewisham               9.055e+03  4.543e+02  19.931  < 2e-16 ***
la19nmMerton                 1.139e+04  5.935e+02  19.182  < 2e-16 ***
la19nmNewham                 1.526e+04  4.467e+02  34.149  < 2e-16 ***
la19nmRedbridge              1.547e+04  5.307e+02  29.141  < 2e-16 ***
la19nmRichmond upon Thames   1.552e+04  6.355e+02  24.414  < 2e-16 ***
la19nmSouthwark              7.684e+03  4.662e+02  16.481  < 2e-16 ***
la19nmSutton                 1.087e+04  6.274e+02  17.332  < 2e-16 ***
la19nmTower Hamlets          5.184e+03  5.107e+02  10.151  < 2e-16 ***
la19nmWaltham Forest         1.307e+04  4.831e+02  27.057  < 2e-16 ***
la19nmWandsworth             1.106e+04  4.905e+02  22.543  < 2e-16 ***
la19nmWestminster            1.161e+04  5.649e+02  20.560  < 2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 5362 on 4800 degrees of freedom
Multiple R-squared:  0.9407,	Adjusted R-squared:  0.9403 
F-statistic:  2177 on 35 and 4800 DF,  p-value: < 2.2e-16
```

### Categorical variables with interaction terms

Sometimes we are interested in how a variable interacts with another variable.
We can explore any interactions between locations (la19nm) and the living environment and barrier ranks.


``` r
reg_cat_var_int <- lm(health_london_rank ~ la19nm * (livingEnv_london_rank + barriers_london_rank), data = lon_dims_imd_2019)

summary(reg_cat_var_int)
```

``` output

Call:
lm(formula = health_london_rank ~ la19nm * (livingEnv_london_rank + 
    barriers_london_rank), data = lon_dims_imd_2019)

Residuals:
     Min       1Q   Median       3Q      Max 
-19227.9  -3254.4    297.6   3372.8  17891.5 

Coefficients:
                                                     Estimate Std. Error
(Intercept)                                         1.300e+04  1.431e+03
la19nmBarnet                                        9.411e+03  1.909e+03
la19nmBexley                                       -5.447e+03  2.464e+03
la19nmBrent                                         3.068e+03  1.866e+03
la19nmBromley                                       1.577e+03  2.107e+03
la19nmCamden                                       -1.547e+04  3.565e+03
la19nmCity of London                               -8.405e+02  5.283e+03
la19nmCroydon                                      -3.824e+03  1.679e+03
la19nmEaling                                        2.124e+03  1.841e+03
la19nmEnfield                                       2.420e+03  1.750e+03
la19nmGreenwich                                    -3.383e+03  2.268e+03
la19nmHackney                                      -1.803e+03  1.937e+03
la19nmHammersmith and Fulham                       -1.123e+04  2.389e+03
la19nmHaringey                                     -4.527e+02  1.975e+03
la19nmHarrow                                        7.800e+03  2.476e+03
la19nmHavering                                     -8.133e+03  2.519e+03
la19nmHillingdon                                    7.792e+02  2.030e+03
la19nmHounslow                                      4.939e+03  2.064e+03
la19nmIslington                                    -4.614e+03  2.592e+03
la19nmKensington and Chelsea                        1.264e+04  2.185e+03
la19nmKingston upon Thames                          5.297e+03  2.972e+03
la19nmLambeth                                      -6.973e+03  2.276e+03
la19nmLewisham                                     -4.745e+03  1.912e+03
la19nmMerton                                       -8.433e+03  2.793e+03
la19nmNewham                                        3.468e+03  1.786e+03
la19nmRedbridge                                     4.339e+03  2.156e+03
la19nmRichmond upon Thames                          3.822e+03  4.288e+03
la19nmSouthwark                                    -6.830e+03  1.923e+03
la19nmSutton                                       -8.242e+03  2.600e+03
la19nmTower Hamlets                                -2.705e+03  2.163e+03
la19nmWaltham Forest                                2.070e+03  1.814e+03
la19nmWandsworth                                    6.438e+02  2.060e+03
la19nmWestminster                                  -1.063e+04  3.281e+03
livingEnv_london_rank                              -1.900e-01  1.321e-01
barriers_london_rank                                3.620e+00  8.023e-01
la19nmBarnet:livingEnv_london_rank                  1.641e-01  1.553e-01
la19nmBexley:livingEnv_london_rank                  5.286e-01  1.644e-01
la19nmBrent:livingEnv_london_rank                   5.219e-01  1.663e-01
la19nmBromley:livingEnv_london_rank                 4.577e-01  1.492e-01
la19nmCamden:livingEnv_london_rank                 -4.599e-02  1.783e-01
la19nmCity of London:livingEnv_london_rank          3.448e-01  9.482e-01
la19nmCroydon:livingEnv_london_rank                 4.524e-01  1.417e-01
la19nmEaling:livingEnv_london_rank                  2.053e-01  1.624e-01
la19nmEnfield:livingEnv_london_rank                 5.217e-01  1.556e-01
la19nmGreenwich:livingEnv_london_rank               2.230e-01  1.620e-01
la19nmHackney:livingEnv_london_rank                -2.882e-01  1.817e-01
la19nmHammersmith and Fulham:livingEnv_london_rank  1.915e-01  2.025e-01
la19nmHaringey:livingEnv_london_rank               -9.428e-02  2.048e-01
la19nmHarrow:livingEnv_london_rank                  3.559e-01  1.774e-01
la19nmHavering:livingEnv_london_rank                5.238e-01  1.584e-01
la19nmHillingdon:livingEnv_london_rank              3.026e-01  1.572e-01
la19nmHounslow:livingEnv_london_rank               -3.776e-02  1.691e-01
la19nmIslington:livingEnv_london_rank              -4.471e-01  1.967e-01
la19nmKensington and Chelsea:livingEnv_london_rank -1.673e+00  2.725e-01
la19nmKingston upon Thames:livingEnv_london_rank    2.551e-01  1.748e-01
la19nmLambeth:livingEnv_london_rank                -1.901e-01  2.037e-01
la19nmLewisham:livingEnv_london_rank                1.834e-01  1.687e-01
la19nmMerton:livingEnv_london_rank                  5.430e-01  1.761e-01
la19nmNewham:livingEnv_london_rank                 -4.372e-03  1.627e-01
la19nmRedbridge:livingEnv_london_rank               2.310e-01  1.655e-01
la19nmRichmond upon Thames:livingEnv_london_rank   -2.672e-03  1.876e-01
la19nmSouthwark:livingEnv_london_rank               2.786e-01  1.656e-01
la19nmSutton:livingEnv_london_rank                  5.311e-01  1.576e-01
la19nmTower Hamlets:livingEnv_london_rank           1.954e-01  1.629e-01
la19nmWaltham Forest:livingEnv_london_rank         -2.538e-02  1.729e-01
la19nmWandsworth:livingEnv_london_rank             -1.552e-01  1.637e-01
la19nmWestminster:livingEnv_london_rank            -9.646e-01  2.107e-01
la19nmBarnet:barriers_london_rank                  -1.334e+00  8.565e-01
la19nmBexley:barriers_london_rank                  -4.421e-01  8.661e-01
la19nmBrent:barriers_london_rank                   -7.477e-01  9.505e-01
la19nmBromley:barriers_london_rank                 -1.240e+00  8.666e-01
la19nmCamden:barriers_london_rank                   2.994e+00  1.172e+00
la19nmCity of London:barriers_london_rank           1.001e+00  3.050e+00
la19nmCroydon:barriers_london_rank                 -6.200e-01  8.595e-01
la19nmEaling:barriers_london_rank                  -5.816e-01  8.604e-01
la19nmEnfield:barriers_london_rank                 -6.740e-01  8.776e-01
la19nmGreenwich:barriers_london_rank               -8.003e-01  8.842e-01
la19nmHackney:barriers_london_rank                  4.611e-01  1.427e+00
la19nmHammersmith and Fulham:barriers_london_rank   1.442e+00  9.425e-01
la19nmHaringey:barriers_london_rank                 4.613e-01  9.154e-01
la19nmHarrow:barriers_london_rank                  -1.413e+00  9.202e-01
la19nmHavering:barriers_london_rank                -3.766e-01  9.029e-01
la19nmHillingdon:barriers_london_rank              -1.592e+00  8.683e-01
la19nmHounslow:barriers_london_rank                -1.445e+00  9.061e-01
la19nmIslington:barriers_london_rank               -7.852e-01  1.031e+00
la19nmKensington and Chelsea:barriers_london_rank   1.076e+00  1.007e+00
la19nmKingston upon Thames:barriers_london_rank    -1.257e+00  9.816e-01
la19nmLambeth:barriers_london_rank                 -3.110e-01  9.491e-01
la19nmLewisham:barriers_london_rank                 1.004e-01  9.045e-01
la19nmMerton:barriers_london_rank                   4.934e-01  9.399e-01
la19nmNewham:barriers_london_rank                   2.625e+00  2.260e+00
la19nmRedbridge:barriers_london_rank               -1.116e+00  8.944e-01
la19nmRichmond upon Thames:barriers_london_rank    -1.884e-01  1.104e+00
la19nmSouthwark:barriers_london_rank                5.627e-02  9.074e-01
la19nmSutton:barriers_london_rank                  -5.571e-02  9.399e-01
la19nmTower Hamlets:barriers_london_rank           -2.394e+00  9.808e-01
la19nmWaltham Forest:barriers_london_rank          -5.478e-01  9.237e-01
la19nmWandsworth:barriers_london_rank              -2.874e-01  8.705e-01
la19nmWestminster:barriers_london_rank              3.494e+00  1.111e+00
                                                   t value Pr(>|t|)    
(Intercept)                                          9.089  < 2e-16 ***
la19nmBarnet                                         4.929 8.57e-07 ***
la19nmBexley                                        -2.210 0.027119 *  
la19nmBrent                                          1.645 0.100112    
la19nmBromley                                        0.749 0.454155    
la19nmCamden                                        -4.338 1.47e-05 ***
la19nmCity of London                                -0.159 0.873612    
la19nmCroydon                                       -2.277 0.022851 *  
la19nmEaling                                         1.154 0.248528    
la19nmEnfield                                        1.382 0.166890    
la19nmGreenwich                                     -1.491 0.135952    
la19nmHackney                                       -0.931 0.352136    
la19nmHammersmith and Fulham                        -4.700 2.68e-06 ***
la19nmHaringey                                      -0.229 0.818706    
la19nmHarrow                                         3.150 0.001640 ** 
la19nmHavering                                      -3.228 0.001254 ** 
la19nmHillingdon                                     0.384 0.701039    
la19nmHounslow                                       2.393 0.016735 *  
la19nmIslington                                     -1.780 0.075114 .  
la19nmKensington and Chelsea                         5.787 7.61e-09 ***
la19nmKingston upon Thames                           1.782 0.074808 .  
la19nmLambeth                                       -3.064 0.002197 ** 
la19nmLewisham                                      -2.482 0.013106 *  
la19nmMerton                                        -3.019 0.002549 ** 
la19nmNewham                                         1.942 0.052165 .  
la19nmRedbridge                                      2.013 0.044191 *  
la19nmRichmond upon Thames                           0.891 0.372776    
la19nmSouthwark                                     -3.552 0.000387 ***
la19nmSutton                                        -3.169 0.001538 ** 
la19nmTower Hamlets                                 -1.251 0.211119    
la19nmWaltham Forest                                 1.141 0.253966    
la19nmWandsworth                                     0.313 0.754638    
la19nmWestminster                                   -3.240 0.001203 ** 
livingEnv_london_rank                               -1.438 0.150471    
barriers_london_rank                                 4.511 6.60e-06 ***
la19nmBarnet:livingEnv_london_rank                   1.057 0.290593    
la19nmBexley:livingEnv_london_rank                   3.215 0.001312 ** 
la19nmBrent:livingEnv_london_rank                    3.139 0.001705 ** 
la19nmBromley:livingEnv_london_rank                  3.068 0.002169 ** 
la19nmCamden:livingEnv_london_rank                  -0.258 0.796425    
la19nmCity of London:livingEnv_london_rank           0.364 0.716133    
la19nmCroydon:livingEnv_london_rank                  3.191 0.001425 ** 
la19nmEaling:livingEnv_london_rank                   1.264 0.206349    
la19nmEnfield:livingEnv_london_rank                  3.354 0.000803 ***
la19nmGreenwich:livingEnv_london_rank                1.376 0.168758    
la19nmHackney:livingEnv_london_rank                 -1.586 0.112711    
la19nmHammersmith and Fulham:livingEnv_london_rank   0.946 0.344403    
la19nmHaringey:livingEnv_london_rank                -0.460 0.645286    
la19nmHarrow:livingEnv_london_rank                   2.005 0.044971 *  
la19nmHavering:livingEnv_london_rank                 3.307 0.000951 ***
la19nmHillingdon:livingEnv_london_rank               1.925 0.054317 .  
la19nmHounslow:livingEnv_london_rank                -0.223 0.823335    
la19nmIslington:livingEnv_london_rank               -2.273 0.023082 *  
la19nmKensington and Chelsea:livingEnv_london_rank  -6.137 9.08e-10 ***
la19nmKingston upon Thames:livingEnv_london_rank     1.459 0.144509    
la19nmLambeth:livingEnv_london_rank                 -0.934 0.350602    
la19nmLewisham:livingEnv_london_rank                 1.087 0.277108    
la19nmMerton:livingEnv_london_rank                   3.082 0.002065 ** 
la19nmNewham:livingEnv_london_rank                  -0.027 0.978565    
la19nmRedbridge:livingEnv_london_rank                1.396 0.162864    
la19nmRichmond upon Thames:livingEnv_london_rank    -0.014 0.988639    
la19nmSouthwark:livingEnv_london_rank                1.683 0.092533 .  
la19nmSutton:livingEnv_london_rank                   3.370 0.000759 ***
la19nmTower Hamlets:livingEnv_london_rank            1.200 0.230337    
la19nmWaltham Forest:livingEnv_london_rank          -0.147 0.883300    
la19nmWandsworth:livingEnv_london_rank              -0.948 0.343019    
la19nmWestminster:livingEnv_london_rank             -4.578 4.80e-06 ***
la19nmBarnet:barriers_london_rank                   -1.558 0.119396    
la19nmBexley:barriers_london_rank                   -0.510 0.609807    
la19nmBrent:barriers_london_rank                    -0.787 0.431493    
la19nmBromley:barriers_london_rank                  -1.431 0.152500    
la19nmCamden:barriers_london_rank                    2.555 0.010647 *  
la19nmCity of London:barriers_london_rank            0.328 0.742734    
la19nmCroydon:barriers_london_rank                  -0.721 0.470718    
la19nmEaling:barriers_london_rank                   -0.676 0.499110    
la19nmEnfield:barriers_london_rank                  -0.768 0.442499    
la19nmGreenwich:barriers_london_rank                -0.905 0.365429    
la19nmHackney:barriers_london_rank                   0.323 0.746543    
la19nmHammersmith and Fulham:barriers_london_rank    1.530 0.126056    
la19nmHaringey:barriers_london_rank                  0.504 0.614335    
la19nmHarrow:barriers_london_rank                   -1.535 0.124794    
la19nmHavering:barriers_london_rank                 -0.417 0.676607    
la19nmHillingdon:barriers_london_rank               -1.834 0.066734 .  
la19nmHounslow:barriers_london_rank                 -1.595 0.110827    
la19nmIslington:barriers_london_rank                -0.762 0.446220    
la19nmKensington and Chelsea:barriers_london_rank    1.068 0.285397    
la19nmKingston upon Thames:barriers_london_rank     -1.280 0.200487    
la19nmLambeth:barriers_london_rank                  -0.328 0.743140    
la19nmLewisham:barriers_london_rank                  0.111 0.911614    
la19nmMerton:barriers_london_rank                    0.525 0.599625    
la19nmNewham:barriers_london_rank                    1.161 0.245561    
la19nmRedbridge:barriers_london_rank                -1.247 0.212307    
la19nmRichmond upon Thames:barriers_london_rank     -0.171 0.864559    
la19nmSouthwark:barriers_london_rank                 0.062 0.950560    
la19nmSutton:barriers_london_rank                   -0.059 0.952740    
la19nmTower Hamlets:barriers_london_rank            -2.440 0.014708 *  
la19nmWaltham Forest:barriers_london_rank           -0.593 0.553200    
la19nmWandsworth:barriers_london_rank               -0.330 0.741265    
la19nmWestminster:barriers_london_rank               3.146 0.001667 ** 
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 5162 on 4736 degrees of freedom
Multiple R-squared:  0.5789,	Adjusted R-squared:  0.5702 
F-statistic: 66.45 on 98 and 4736 DF,  p-value: < 2.2e-16
```

::: challenge
## Challenge 2

Using the `gapminder` data to create a linear model between a categorical and a continuous variable .

Discuss your question and your findings.
:::

## Broom

The 'broom' package offers an alternative way of presenting the output of statistical analysis.
It centers around three S3 methods, each of which take common objects produced by R statistical functions (lm, t.test, nls, etc) and convert them into a tibble.

These are:

-   tidy: constructs a tibble that summarizes the model’s statistical findings. This includes coefficients and p-values for each term in a regression, per-cluster information in clustering applications, or per-test information for multtest functions.
-   augment: add columns to the original data that was modeled. This includes predictions, residuals, and cluster assignments.
-   glance: construct a concise one-row summary of the model. This typically contains values such as R\^2, adjusted R\^2, and residual standard error that are computed once for the entire model.

Let's revisit our initial linear model:


``` r
reg_LivEnv_health <- lm(health_london_rank ~ livingEnv_london_rank, data = lon_dims_imd_2019)

summary(reg_LivEnv_health)
```

``` output

Call:
lm(formula = health_london_rank ~ livingEnv_london_rank, data = lon_dims_imd_2019)

Residuals:
     Min       1Q   Median       3Q      Max 
-21549.6  -5948.2    609.1   6239.2  15792.2 

Coefficients:
                       Estimate Std. Error t value Pr(>|t|)    
(Intercept)           1.692e+04  2.274e+02   74.40   <2e-16 ***
livingEnv_london_rank 3.430e-01  1.915e-02   17.91   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 7625 on 4833 degrees of freedom
Multiple R-squared:  0.06225,	Adjusted R-squared:  0.06205 
F-statistic: 320.8 on 1 and 4833 DF,  p-value: < 2.2e-16
```

There is a lot of useful information, but it not available in a way so that you can combine it with other models or do further analysis.
We can convert this to tabular data using the 'tidy' function.


``` r
tidy(reg_LivEnv_health)
```

``` output
# A tibble: 2 × 5
  term                   estimate std.error statistic  p.value
  <chr>                     <dbl>     <dbl>     <dbl>    <dbl>
1 (Intercept)           16916.     227.          74.4 0       
2 livingEnv_london_rank     0.343    0.0192      17.9 1.63e-69
```

The row names have been moved into a column called term, and the column names are simple and consistent (and can be accessed using \$).

Information about the model can be explored with 'augment'.
The function augments the original data with information from the model, such as the fitted values and residuals for each of the original points in the regression.


``` r
augment(reg_LivEnv_health)
```

``` output
# A tibble: 4,835 × 8
   health_london_rank livingEnv_london_rank .fitted  .resid     .hat .sigma
                <int>                 <int>   <dbl>   <dbl>    <dbl>  <dbl>
 1              32113                  7789  19588.  12525. 0.000250  7624.
 2              29705                 13070  21400.   8305. 0.000252  7625.
 3              17600                  4092  18320.   -720. 0.000458  7626.
 4              17907                  9397  20140.  -2233. 0.000213  7626.
 5              21581                 10629  20562.   1019. 0.000207  7626.
 6              16414                 11162  20745.  -4331. 0.000210  7626.
 7              12334                  8672  19891.  -7557. 0.000226  7625.
 8               9661                  9611  20213. -10552. 0.000211  7625.
 9              16050                  2269  17694.  -1644. 0.000624  7626.
10              18178                  4309  18394.   -216. 0.000441  7626.
# ℹ 4,825 more rows
# ℹ 2 more variables: .cooksd <dbl>, .std.resid <dbl>
```

Some of the data presented by 'augment' will be discussed in the episode Linear Regression Diagnostics.

Summary statistics are computed for the entire regression, such as R\^2 and the F-statistic can be accessed with the 'glance' function:


``` r
glance(reg_LivEnv_health)
```

``` output
# A tibble: 1 × 12
  r.squared adj.r.squared sigma statistic  p.value    df  logLik     AIC     BIC
      <dbl>         <dbl> <dbl>     <dbl>    <dbl> <dbl>   <dbl>   <dbl>   <dbl>
1    0.0622        0.0621 7625.      321. 1.63e-69     1 -50081. 100167. 100187.
# ℹ 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>
```

### Generalised linear models

We can also use the 'broom' functions to present data from Generalised linear and non-linear models.
For example, if we wanted to explore the Income Rank in relation to whether or not an area was within the City of London.


``` r
# add a variable to indicate whether or not an area is within the City of London
lon_dims_imd_2019 <- lon_dims_imd_2019 %>% mutate(city = la19nm == "City of London")

# create a Generalised Linear Model
glmlondims <- glm(city ~ Income_london_rank, lon_dims_imd_2019, family = "binomial")
summary(glmlondims)
```

``` output

Call:
glm(formula = city ~ Income_london_rank, family = "binomial", 
    data = lon_dims_imd_2019)

Coefficients:
                     Estimate Std. Error z value Pr(>|z|)    
(Intercept)        -7.868e+00  1.004e+00  -7.835 4.68e-15 ***
Income_london_rank  6.888e-05  4.635e-05   1.486    0.137    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 92.295  on 4834  degrees of freedom
Residual deviance: 90.062  on 4833  degrees of freedom
AIC: 94.062

Number of Fisher Scoring iterations: 10
```

Use of 'tidy':


``` r
tidy(glmlondims)
```

``` output
# A tibble: 2 × 5
  term                 estimate std.error statistic  p.value
  <chr>                   <dbl>     <dbl>     <dbl>    <dbl>
1 (Intercept)        -7.87      1.00          -7.84 4.68e-15
2 Income_london_rank  0.0000689 0.0000463      1.49 1.37e- 1
```

Use of 'augment':


``` r
augment(glmlondims)
```

``` output
# A tibble: 4,835 × 8
   city  Income_london_rank .fitted  .resid     .hat .sigma   .cooksd .std.resid
   <lgl>              <int>   <dbl>   <dbl>    <dbl>  <dbl>     <dbl>      <dbl>
 1 TRUE               32831   -5.61  3.35   0.00194   0.128   2.65e-1     3.35  
 2 TRUE               29901   -5.81  3.41   0.00115   0.127   1.93e-1     3.41  
 3 TRUE               18510   -6.59  3.63   0.000233  0.126   8.51e-2     3.63  
 4 TRUE                6029   -7.45  3.86   0.000332  0.125   2.87e-1     3.86  
 5 FALSE              14023   -6.90 -0.0448 0.000239  0.137   1.20e-7    -0.0448
 6 FALSE               6261   -7.44 -0.0343 0.000330  0.137   9.72e-8    -0.0343
 7 FALSE               3382   -7.64 -0.0311 0.000360  0.137   8.70e-8    -0.0311
 8 FALSE               7506   -7.35 -0.0358 0.000315  0.137   1.01e-7    -0.0358
 9 FALSE               8902   -7.25 -0.0376 0.000298  0.137   1.05e-7    -0.0376
10 FALSE               9033   -7.25 -0.0378 0.000296  0.137   1.06e-7    -0.0378
# ℹ 4,825 more rows
```

Use of 'glance':


``` r
glance(glmlondims)
```

``` output
# A tibble: 1 × 8
  null.deviance df.null logLik   AIC   BIC deviance df.residual  nobs
          <dbl>   <int>  <dbl> <dbl> <dbl>    <dbl>       <int> <int>
1          92.3    4834  -45.0  94.1  107.     90.1        4833  4835
```

You will notice that the statistics computed by 'glance' are different for glm objects than for lm (e.g. deviance rather than R\^2).

### Hypothesis testing

The tidy function can also be applied a range of hypotheses tests, such as built-in functions like t.test, cor.test, and wilcox.test.

### t-test


``` r
tt <- t.test(Income_london_rank ~ city, lon_dims_imd_2019)
tidy(tt)
```

``` output
# A tibble: 1 × 10
  estimate estimate1 estimate2 statistic p.value parameter conf.low conf.high
     <dbl>     <dbl>     <dbl>     <dbl>   <dbl>     <dbl>    <dbl>     <dbl>
1   -5344.    14456.    19800.     -1.24   0.270      5.01  -16407.     5719.
# ℹ 2 more variables: method <chr>, alternative <chr>
```

Some cases might have fewer columns (for example, no confidence interval).

Wilcox test:


``` r
wt <- wilcox.test(Income_london_rank ~ city, lon_dims_imd_2019)
tidy(wt)
```

``` output
# A tibble: 1 × 4
  statistic p.value method                                           alternative
      <dbl>   <dbl> <chr>                                            <chr>      
1     9836.   0.174 Wilcoxon rank sum test with continuity correcti… two.sided  
```

Since the 'tidy' output is already only one row, glance returns the same output:


``` r
# t-test
glance(tt)
```

``` output
# A tibble: 1 × 10
  estimate estimate1 estimate2 statistic p.value parameter conf.low conf.high
     <dbl>     <dbl>     <dbl>     <dbl>   <dbl>     <dbl>    <dbl>     <dbl>
1   -5344.    14456.    19800.     -1.24   0.270      5.01  -16407.     5719.
# ℹ 2 more variables: method <chr>, alternative <chr>
```

``` r
# Wilcox test
glance(wt)
```

``` output
# A tibble: 1 × 4
  statistic p.value method                                           alternative
      <dbl>   <dbl> <chr>                                            <chr>      
1     9836.   0.174 Wilcoxon rank sum test with continuity correcti… two.sided  
```

The chisq.test enables use to investigate whether changes in one categorical variable are related to changes in another categorical variable.

The 'augment' method is defined only for chi-squared tests, since there is no meaningful sense, for other tests, in which a hypothesis test produces output about each initial data point.


``` r
# convert IDAOP_london_decile to a factor so it is not interprested as continuous data
lon_dims_imd_2019$IDAOP_london_decile <- factor(lon_dims_imd_2019$IDAOP_london_decile)

# xtabs creates a frequency table of IMD deciles within London borooughs
chit <- chisq.test(xtabs(~ la19nm + IDAOP_london_decile, data = lon_dims_imd_2019))
```

``` warning
Warning in chisq.test(xtabs(~la19nm + IDAOP_london_decile, data =
lon_dims_imd_2019)): Chi-squared approximation may be incorrect
```

``` r
tidy(chit)
```

``` output
# A tibble: 1 × 4
  statistic p.value parameter method                    
      <dbl>   <dbl>     <int> <chr>                     
1     2841.       0       288 Pearson's Chi-squared test
```


``` r
augment(chit)
```

``` output
# A tibble: 330 × 9
   la19nm    IDAOP_london_decile .observed   .prop .row.prop .col.prop .expected
   <fct>     <fct>                   <int>   <dbl>     <dbl>     <dbl>     <dbl>
 1 Barking … 1                           6 1.24e-3    0.0545   0.0124     11.0  
 2 Barnet    1                           6 1.24e-3    0.0284   0.0124     21.1  
 3 Bexley    1                           0 0          0        0          14.6  
 4 Brent     1                          12 2.48e-3    0.0694   0.0248     17.3  
 5 Bromley   1                           2 4.14e-4    0.0102   0.00414    19.7  
 6 Camden    1                          12 2.48e-3    0.0902   0.0248     13.3  
 7 City of … 1                           0 0          0        0           0.599
 8 Croydon   1                           8 1.65e-3    0.0364   0.0166     22.0  
 9 Ealing    1                          11 2.28e-3    0.0561   0.0228     19.6  
10 Enfield   1                           9 1.86e-3    0.0492   0.0186     18.3  
# ℹ 320 more rows
# ℹ 2 more variables: .resid <dbl>, .std.resid <dbl>
```

There are a number of underlying assumptions of a Chi-Square test, these are:

-   Independence: The Chi-Square test assumes that the observations in the data are independent of each other.
    This means that the outcome of one observation should not influence the outcome of another.

-   Random sampling: The data should be obtained through random sampling to ensure that it is representative of the population from which it was drawn.

-   Expected frequency: The Chi-Square test assumes that the expected frequency count for each cell in the contingency table should be at least 5.
    If this assumption is not met, the test results may not be reliable.

As we have received a warning about the reliability of our test, it is likely that one of these assumptions has not been met, and that this is not a suitable test for this data.

::: challenge
## Challenge 3

Use broom to amend the display of your model outputs.

Which function(s) did you use and why?
:::

### Conventions

There are some conventions that enable consistency across the broom functions, these are: \* The output of the tidy, augment and glance functions is always a tibble.

-   The output never has rownames. This ensures that you can combine it with other tidy outputs without fear of losing information (since rownames in R cannot contain duplicates).
-   Some column names are kept consistent, so that they can be combined across different models and so that you know what to expect (in contrast to asking “is it pval or PValue?” every time).
