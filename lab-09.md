Lab 09 - Grading the professor, Pt. 1
================
Fiona Wang
Mar-20-2025

## Load Packages and Data

``` r
library(tidyverse) 
library(tidymodels)
library(openintro)
```

## Exercise 1 Getting to know the data

``` r
data <- evals
```

``` r
data %>% 
  ggplot(aes(x = score)) + geom_histogram(binwidth = 0.1, color = "black")
```

![](lab-09_files/figure-gfm/scorehist-1.png)<!-- -->

As we can see from the graph, the majority of the data is on the right,
and the tail extends to the left. Thus it is left-skewed. It doesn’t
show me a lot of details about students’ rate. I want to include a
boxplot and the summary statistics here too.

``` r
data %>% 
  ggplot(aes(y = score)) + geom_boxplot()
```

![](lab-09_files/figure-gfm/scorebox-1.png)<!-- -->

``` r
summary(data$score)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   2.300   3.800   4.300   4.175   4.600   5.000

The mean rating is 4.18. Students were, on average, pretty satisfied
with their professors. With a max of 5, and a min of 2.3. There are also
3 outliers on the lower end according to the boxplot. This is what I
expected. Students rated on 463 courses taught by 94 professors, so I
expected this variance. I think an average of 4.18 is fair and pretty
good.

## Exercise 2 Visual

``` r
data %>% 
  ggplot(aes(x = bty_avg, y = score)) + geom_point(alpha = 0.2, color = "blue") + 
  labs(title = "Relationship Between Beauty Rating and Overall Ratings for Professors")
```

![](lab-09_files/figure-gfm/scorebty-1.png)<!-- -->

I made the dots transparent. For the darker dots, it means that more
people rated appearance and score that way. According to this graph, I
think there is a positive relationship between score and beauty rating.
However, it’s not very clear. There could be no relationship too because
the dots are pretty scattered all over the place.

### Exercise 3 Visual improve

``` r
data %>% 
  ggplot(aes(x = bty_avg, y = score)) + geom_jitter() + 
  labs(title = "Relationship Between Beauty Rating and Overall Ratings for Professors")
```

![](lab-09_files/figure-gfm/jitter-1.png)<!-- -->

I don’t know what jitter means, so I looked it up in the Help panel. It
says that jitter “adds a small amount of random variation to the
location fo each point”. It can help with reducing overlap, especially
for our dataset which has a lot of overlapping points. This means that
the first graph might be misleading because we can’t see all the data as
they were stacked on top of each other. However, I made the dots
transparent, so I still retained this information.

### Exercise 4 Beauty model

``` r
m_bty <- linear_reg() %>% 
  set_engine("lm") %>% 
  fit(score ~ bty_avg, data = data)
tidy(m_bty)
```

    ## # A tibble: 2 × 5
    ##   term        estimate std.error statistic   p.value
    ##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
    ## 1 (Intercept)   3.88      0.0761     51.0  1.56e-191
    ## 2 bty_avg       0.0666    0.0163      4.09 5.08e-  5

From the output table, the linear model will be: score = 3.88 +
0.067(bty_avg)

### Exercise 5 Visual with regression line

``` r
data %>% 
  ggplot(aes(x = bty_avg, y = score)) + geom_jitter() + geom_smooth(method = "lm", se = FALSE) + 
  labs(title = "Relationship Between Beauty Rating and Overall Ratings for Professors",
       x = "Average Beauty Rating",
       y = "Average Professor Evaluation")
```

![](lab-09_files/figure-gfm/replot-1.png)<!-- -->

### Exercise 6 Interpreting the slope

Looking at the table from exercise 4, the slope of the linear model is
0.0666. This means that with one unit increase in average beauty rating,
there will be a 0.0666 unit increase in the average professor
evaluation.

### Exercise 7 Interpreting the intercept

Again, looking at the table from exercise 4, the intercept is 3.88. This
means that when there is a 0 for the average beauty rating for a
professor, the average professor evaluation (score) is 3.88.
Theoretically, this makes sense. However, in the context of this data,
the intercept doesn’t make sense. I checked the description of this
data. The beauty ratings are from 1-10. Thus, there wouldn’t be a 0 for
the average beauty rating.

### Exercise 8 Interpreting R^2

``` r
glance(m_bty)
```

    ## # A tibble: 1 × 12
    ##   r.squared adj.r.squared sigma statistic   p.value    df logLik   AIC   BIC
    ##       <dbl>         <dbl> <dbl>     <dbl>     <dbl> <dbl>  <dbl> <dbl> <dbl>
    ## 1    0.0350        0.0329 0.535      16.7 0.0000508     1  -366.  738.  751.
    ## # ℹ 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>

The R^2 is 0.035. Sine we only have one predictor, I just looked at the
R^2, and not the adjusted R^2. This means that the average beauty rating
explained 3.5% of the variance in the average professor evaluation.

### Exercise 9 Gender model and interpretation

``` r
m_gen <- linear_reg() %>% 
  set_engine("lm") %>% 
  fit(score ~ gender, data = data)
tidy(m_gen)
```

    ## # A tibble: 2 × 5
    ##   term        estimate std.error statistic p.value
    ##   <chr>          <dbl>     <dbl>     <dbl>   <dbl>
    ## 1 (Intercept)    4.09     0.0387    106.   0      
    ## 2 gendermale     0.142    0.0508      2.78 0.00558

The linear model:  
y = 4.093 + 0.142x  
Score = 4.093 + 0.142(gender)  
According to the table, female is the baseline group.  
We have an intercept of 4.093. This means that females had an average of
4.093 in score. The slope is 0.142. This means that with one unit
increase in gender, the score increases by 0.142 unit. Males, on
average, had a 0.142 higher score in rating than females. The 0 here
makes sense in the context of the data.

### Exercise 10 More on gender

Based on the previous exercise, we can write down the equation for each
gender.  
Female: score = 4.093 + 0.142(0) = 4.093.  
Male: score = 4.093 + 0.142(1) = 4.235

### Exercise 11 Rank model

``` r
m_rank <- linear_reg() %>% 
  set_engine("lm") %>% 
  fit(score ~ rank, data = data)
tidy(m_rank)
```

    ## # A tibble: 3 × 5
    ##   term             estimate std.error statistic   p.value
    ##   <chr>               <dbl>     <dbl>     <dbl>     <dbl>
    ## 1 (Intercept)         4.28     0.0537     79.9  1.02e-271
    ## 2 ranktenure track   -0.130    0.0748     -1.73 8.37e-  2
    ## 3 ranktenured        -0.145    0.0636     -2.28 2.28e-  2

There are two slopes, and the reference group is teaching, according to
the table.  
The overall equation is: score = 4.284 -0.1297(tenure track) -
0.1452(tenured)

The first slope (-0.13) is for the tenure track group, and the reference
group is the teaching group, score = 4.284 - 0.13(1) - 0.15(0) = 4.155.
This means that when the rank is 0 (teaching group), the mean score is
4.284, which is the intercept. With one unit increase in rank, there
will be a 0.13 unit decrease in score. One unit increase in rank refers
to the tenure track group. So, the tenure track group has an average
scoring of 4.155.  
The second slope (-0.145) is for the tenured group, and the reference
group is the teaching group, score = 4.284 - 0.13(0) - 0.145(1) = 4.139.
This means that when the rank is 0 (teaching group), the mean score is
4.284, which is the intercept. With one unit increase in rank, there
will be a 0.145 unit decrease in score. One unit increase in rank refers
to the tenured group in this equation. So, the tenured group has an
average scoring of 4.139.

### Exercise 12 Creating rank_relevel

``` r
data$rank_relevel <- relevel(data$rank, ref = "tenure track")
levels(data$rank)
```

    ## [1] "teaching"     "tenure track" "tenured"

``` r
levels(data$rank_relevel)
```

    ## [1] "tenure track" "teaching"     "tenured"

### Exercise 13 Rank_relevel model

Now that the reference group is tenure track, let’s fit another
regression model.

``` r
m_rank_relevel <- linear_reg() %>% 
  set_engine("lm") %>% 
  fit(score ~ rank_relevel, data = data)
tidy(m_rank_relevel)
```

    ## # A tibble: 3 × 5
    ##   term                 estimate std.error statistic   p.value
    ##   <chr>                   <dbl>     <dbl>     <dbl>     <dbl>
    ## 1 (Intercept)            4.15      0.0521    79.7   2.58e-271
    ## 2 rank_relevelteaching   0.130     0.0748     1.73  8.37e-  2
    ## 3 rank_releveltenured   -0.0155    0.0623    -0.249 8.04e-  1

``` r
glance(m_rank_relevel)
```

    ## # A tibble: 1 × 12
    ##   r.squared adj.r.squared sigma statistic p.value    df logLik   AIC   BIC
    ##       <dbl>         <dbl> <dbl>     <dbl>   <dbl> <dbl>  <dbl> <dbl> <dbl>
    ## 1    0.0116       0.00733 0.542      2.71  0.0679     2  -372.  752.  768.
    ## # ℹ 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>

There are two slopes, and the reference group is tenure track.  
The overall model: score = 4.155 + 0.1297(teaching) - 0.0155(tenured).  
The first slope is 0.1297. One unit increase in rank is associated with
0.1297 increase in the average score. One unit increase here refers to
the teaching group, and they have a 4.155 + 0.1297(1) - 0.0155(0) =
4.2843 average rating.  
The second slope is -0.0155. One unit increase in rank is associated
with 0.0155 decrease in the average score. One unit increase here is the
tenured group, and they have a 4.155 + 0.1297(0) - 0.0155(1) = 4.139
average rating.  
The R^2 is 0.0116. This means that rank explains 1.16% of the variance
in average scores. Also, it is not significant. Rank is not a
significant predictor of score.

### Exercise 14 Creat tenure_eligible

``` r
data <- data %>% 
  mutate(tenure_eligible = if_else(rank == "teaching", "no", "yes"))
```

### Exercise 15 Tenure eligible model

``` r
m_tenure_eligible <- linear_reg() %>% 
  set_engine("lm") %>% 
  fit(score ~ tenure_eligible, data = data)
tidy(m_tenure_eligible)
```

    ## # A tibble: 2 × 5
    ##   term               estimate std.error statistic   p.value
    ##   <chr>                 <dbl>     <dbl>     <dbl>     <dbl>
    ## 1 (Intercept)           4.28     0.0536     79.9  2.72e-272
    ## 2 tenure_eligibleyes   -0.141    0.0607     -2.32 2.10e-  2

``` r
glance(m_tenure_eligible)
```

    ## # A tibble: 1 × 12
    ##   r.squared adj.r.squared sigma statistic p.value    df logLik   AIC   BIC
    ##       <dbl>         <dbl> <dbl>     <dbl>   <dbl> <dbl>  <dbl> <dbl> <dbl>
    ## 1    0.0115       0.00935 0.541      5.36  0.0210     1  -372.  750.  762.
    ## # ℹ 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>

According to the output, the reference group is the no group.  
Score = 4.284 - 0.141(tenure_eligible).  
The intercept is 4.284, meaning that when tenure_eligible is 0 (i.e.,
no), the average score is 4.284. The slope is -0.141, meaning with one
unit increase in tenure_eligible, which will be the yes group, there is
a 0.141 unit decrease in the average score. So, the average score for
the yes group is 4.143.  
R^2 = 0.0115, p = 0.02. This means that whether one is tenure eligible
explains 1.15% of the variance in the average score. It is a significant
predictor.

### Additional quest Rank value

I was wondering what will happen if we present rank as numerical values.
First create a new variable rank_value to

``` r
data<- data %>% 
  mutate(rank_value = if_else(rank == "teaching", 0, if_else(rank == "tenure track", 1, 2)))
m_rank_value <- linear_reg() %>% 
  set_engine("lm") %>% 
  fit(score ~ rank_value, data = data)
tidy(m_rank_value)
```

    ## # A tibble: 2 × 5
    ##   term        estimate std.error statistic   p.value
    ##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
    ## 1 (Intercept)   4.26      0.0482     88.4  2.61e-291
    ## 2 rank_value   -0.0660    0.0310     -2.13 3.37e-  2

``` r
glance(m_rank_value)
```

    ## # A tibble: 1 × 12
    ##   r.squared adj.r.squared sigma statistic p.value    df logLik   AIC   BIC
    ##       <dbl>         <dbl> <dbl>     <dbl>   <dbl> <dbl>  <dbl> <dbl> <dbl>
    ## 1   0.00975       0.00760 0.542      4.54  0.0337     1  -372.  750.  763.
    ## # ℹ 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>

This time only one slope. The baseline group is still teaching, but the
numeric representation is that 0:teaching, 1:tenure track, 2: tenured.
The slope is -0.066, with one unit increase in rank_value, score will
decrease by -0.066. We got a different R^2 value again: 0.0098. Our
model explains 0.98% of the variance in score and it is a significant
predictor. So, the association is telling us that the senior the
professor, the lower the average rating. However, this conclusion is
only correlational and not causal.
