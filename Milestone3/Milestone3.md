Mini Data-Analysis Deliverable 3
================
Yihang Chen

# Welcome to your last milestone in your mini data analysis project!

In Milestone 1, you explored your data and came up with research
questions. In Milestone 2, you obtained some results by making summary
tables and graphs.

In this (3rd) milestone, you’ll be sharpening some of the results you
obtained from your previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

## Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-3.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 40 points (compared to the usual 30
points): 30 for your analysis, and 10 for your entire mini-analysis
GitHub repository. Details follow.

**Research Questions**: In Milestone 2, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) 
library(tidyverse)
library(broom)
library(here)
```

From Milestone 2, you chose two research questions. What were they? Put
them here.

<!-------------------------- Start your work below ---------------------------->

1.  What is the pattern of the review percentage?  
2.  Would the games with higher original price generally receive higher
    reviews?
    <!----------------------------------------------------------------------------->

# Exercise 1: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

**For the plot, I chose the plot made when working on research question
2: Would the games with higher original price generally receive higher
reviews?**

Note: I modified this plot a little bit(from milestone 2) to work better
with the following tasks. The modification that I made are: 1) Change
the dataset to dataset_for_milestone3, as it was made at the end of
milestone2 and fulfills the requirements for milestone3 better. 2) Add
color in ggplot. This is for viewing the groups better. 3) Change the
labels for x-axis. This is for viewing the graph better (more
organized). 4) Deleted the filter that “original_price \<100” because
this has already been set up in the dataset_for_milestone3. 5) Changed
the groups from only “High” to “High” and “Very High”, for the future
work on task 2.

``` r
#First, I input the dataset_for_milestone3 that created in dataset2:
dataset_for_milestone3 <- steam_games %>%
  mutate(reviews_percentage = as.numeric(str_match(all_reviews, ",- \\s*(.*?)\\s*% of the")[,2])) %>%
  mutate(total_reviews =  as.numeric(gsub(",","",str_match(all_reviews, "of the \\s*(.*?)\\s* user")[,2]))) %>%
  mutate(review_category = case_when(reviews_percentage <= 50 ~ "Low",
                                 reviews_percentage <= 70~ "Medium",
                                 reviews_percentage <= 85~ "High",
                                 reviews_percentage <= 100 ~ "Very High")) %>%
  filter(original_price<100) %>%
  separate(release_date, into = c("date", "release_year"), sep=",") %>%
  separate(date, into = c("release_month", "release_day"), sep=" ") %>%
  select(name, types,achievements, release_day, release_month, release_year, original_price, discount_price, reviews_percentage, total_reviews, review_category)
```

    ## Warning: Expected 2 pieces. Missing pieces filled with `NA` in 723 rows [37, 59,
    ## 78, 96, 121, 159, 170, 182, 193, 204, 214, 234, 254, 298, 317, 350, 370, 384,
    ## 387, 490, ...].

    ## Warning: Expected 2 pieces. Additional pieces discarded in 7 rows [29169, 31487,
    ## 32055, 32303, 34095, 34627, 34642].

    ## Warning: Expected 2 pieces. Missing pieces filled with `NA` in 669 rows [37, 59,
    ## 78, 96, 121, 159, 170, 182, 193, 204, 214, 234, 254, 298, 317, 350, 370, 384,
    ## 387, 490, ...].

``` r
#Then, create the plot from the dataset
plot5 <- dataset_for_milestone3 %>%
  ggplot(aes(x=review_category,y=original_price, color=review_category)) +
          geom_boxplot()+
          xlab("")
print(plot5)
```

![](Milestone3_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)
        -   Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
        -   Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.
    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).
        -   For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 1

``` r
plot6 <- dataset_for_milestone3 %>%
  mutate(review_category_reorder = fct_relevel(review_category,"Low","Medium","High","Very High")) %>% #reorder the category
  ggplot(aes(x=review_category_reorder,y=original_price, color=review_category_reorder)) +
          geom_boxplot()+
          xlab("")
print(plot6)
```

![](Milestone3_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->
**Reasoning**: When reordering the graph, I have thought about
reordering by the mean of original_price or a specific order of the
review_category: low, medium, high, very high. Eventually, I chose to
use “low”, “medium”, “high”,“Very high” because it is easier to compare
that the products different levels of reviews. Here, it shows that
products with higher reviews are generally sold for higher (median)
original price. I also decided to leave the NA option here just to show
that games the original prices for those games that does not have a
review, as sort of a control group.

<!----------------------------------------------------------------------------->
<!-------------------------- Start your work below ---------------------------->

**Task Number**: 2

I combined the level “high” and “very high” because their classification
is very close. By putthing them together, it is easier to visualization
the increase of price from “low” to “medium” to “high” levels of
reviews.

``` r
plot7 <- dataset_for_milestone3 %>%
  mutate(review_category_combined = fct_collapse(review_category, High = c("High","Very High"))) %>% #combine two levels together
  mutate(review_category_reorder2 = fct_relevel(review_category_combined,"Low","Medium","High")) %>% #reorder them for better visualization
  ggplot(aes(x=review_category_reorder2,y=original_price, color=review_category)) +
          geom_boxplot()+
          xlab("")

print(plot7)
```

![](Milestone3_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

<!----------------------------------------------------------------------------->

# Exercise 2: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: Would the games with higher original price
generally receive higher reviews?

**Variable of interest**: review_percentage

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.
    -   You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
    -   You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
    -   You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

I choose to fit my data to a linear regression model.

``` r
dataset4 <- dataset_for_milestone3 %>% #I firstly filter the NA values
  filter(!is.na(reviews_percentage)) %>%
  filter(!is.na(original_price))

lmfit <- lm(reviews_percentage ~ original_price, dataset4)
summary(lmfit)
```

    ## 
    ## Call:
    ## lm(formula = reviews_percentage ~ original_price, data = dataset4)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -74.941 -10.561   4.059  14.431  25.472 
    ## 
    ## Coefficients:
    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)    74.52822    0.18826 395.883   <2e-16 ***
    ## original_price  0.02067    0.01155   1.789   0.0736 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 18.56 on 16721 degrees of freedom
    ## Multiple R-squared:  0.0001914,  Adjusted R-squared:  0.0001316 
    ## F-statistic: 3.201 on 1 and 16721 DF,  p-value: 0.07363

From this, we predict the linear model to be **reviews_percentage =
74.52822 + 0.02067 \* original_price**. We can use this to predict the
review using the original price.

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

``` r
review_at_OP100 <- broom::augment(lmfit, newdata = tibble(original_price = 100))

review_at_OP100
```

    ## # A tibble: 1 x 2
    ##   original_price .fitted
    ##            <dbl>   <dbl>
    ## 1            100    76.6

Because I used the filter function to look at the original price \< 100
for the dataset used, for the reason that I think below 100 dollars is a
relatively affordable price for the public, I want to predict what the
review percentage is when original price is at exactly 100. By using the
augment function, I found the predicted review in percentage format is
76.59514%.

<!----------------------------------------------------------------------------->

# Exercise 3: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 2 (Exercise 1.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

-   **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

I chose the research question 4 from milestone 2 exercise 1.2 but made
some changes for this part: 1) I made a summary table (because I think I
made mistake in milestone2 that only get the summarized statistics but
did not make a table). 2) I changed the category into
“low”,“medium”,“high”,“very high” instead of “low”, “medium” and “high”,
to be more consistent with the previous tasks in milestone3.

``` r
summary_table_reviews = (dataset_for_milestone3 %>% 
  filter(!is.na(reviews_percentage)) %>% #filter those NA values
  filter(!is.na(original_price)) %>%
  group_by(review_category) %>% 
  summarise(min_OP = min(original_price),#OP stands for original price
            max_OP = max(original_price),
            mean_OP = mean(original_price), 
            median_OP = median(original_price),
            std_OP = sd(original_price)))
summary_table_reviews 
```

    ## # A tibble: 4 x 6
    ##   review_category min_OP max_OP mean_OP median_OP std_OP
    ##   <chr>            <dbl>  <dbl>   <dbl>     <dbl>  <dbl>
    ## 1 High                 0  100.    11.5       7.99   13.3
    ## 2 Low                  0   96.3    9.33      4.99   12.2
    ## 3 Medium               0   96.3   10.5       5.99   13.0
    ## 4 Very High            0  100.    10.1       6.99   11.2

Then write it as a csv file and store in the “output” folder:

``` r
write_csv(summary_table_reviews, file = here::here("output","summary_table_reviews.csv"))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Exercise 2 to an R binary file (an RDS),
and load it again. Be sure to save the binary file in your `output`
folder. Use the functions `saveRDS()` and `readRDS()`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

**To save RDS**

``` r
saveRDS(lmfit, file = here::here("output","lmfit.rds"))
```

**To load RDS**

``` r
load_lmfit <- readRDS(here::here("output","lmfit.rds"))
load_lmfit
```

    ## 
    ## Call:
    ## lm(formula = reviews_percentage ~ original_price, data = dataset4)
    ## 
    ## Coefficients:
    ##    (Intercept)  original_price  
    ##       74.52822         0.02067

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been `knit`ted to their output, and all data
    files saved from Exercise 3 above appear in the `output` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 3 document knits error-free. (We’ve already graded this
aspect for Milestone 1 and 2)

## Tagged release (1 point)

You’ve tagged a release for Milestone 3. (We’ve already graded this
aspect for Milestone 1 and 2)
