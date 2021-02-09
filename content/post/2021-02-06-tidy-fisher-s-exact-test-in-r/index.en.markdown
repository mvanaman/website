---
title: Tidy Fisher's Exact Test In R (And Why To Always Use It)
author: Matthew E. Vanaman
date: '2021-02-06'
slug: []
categories: []
tags: []
lastmod: '2021-02-06T15:13:40-05:00'
keywords: []
description: ''
comment: yes
toc: yes
autoCollapseToc: yes
postMetaInFooter: no
hiddenFromHomePage: no
contentCopyright: no
reward: no
mathjax: yes
mathjaxEnableSingleDollar: yes
mathjaxEnableAutoNumber: no
hideHeaderAndFooter: no
flowchartDiagrams:
  enable: no
  options: ''
sequenceDiagrams:
  enable: no
  options: ''
bibliography: "/Users/home/Documents/Research and Writing/Misc/MyRefs/MyRefs.bib"
link-citations: true
csl: "/Users/home/Documents/Research and Writing/Misc/MyRefs/apa-web.csl"
---

<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>

<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />

If you are interested in some unsolicited advice about choosing tests and/or a walk-through of this function, keep reading. Otherwise, feel free to skip to the [full function](#Function) at the bottom.

# Why You Should Always Use Fisher’s Exact Test Instead

Most students in the sciences learn about the \$\\chi^{2}\$ test of independence in their introductory statistics course. The idea is that if binary variables *x* and *y* are unrelated, then the observed values and expected values will be identical, in which cases \$\\chi^{2}\$ is equal to zero. The expected values are just the counts of each combination of *x* and *y* we would expect if *x* and *y* were unrelated in the population, while the observed counts are the counts we actually have.

The \$\\chi^{2}\$ value, then, is a function of the difference between the expected values and the observed values (the values in from the sample). The greater the difference between expected and observed values, the lower the probability that the difference between them (or a larger difference) is due to chance, assuming that there is actually no difference at the population level. Thus all the \$\\chi^{2}\$ “test” really means is that we check to see whether the \$p\$-value from the \$\\chi^{2}\$ we get is less than alpha, which is usually a cutoff \$p\$-value of .05.

One assumption of the \$\\chi^{2}\$ test violated fairly often is that no expected values are less than 5. This is sort of an arbitrary cutoff - the larger the expected counts, the more accurate the \$\\chi^{2}\$ value will be (in general). Unlike the \$\\chi^{2}\$ test, the Fisher’s exact test is accurate even in the presence of expected values less than 5. In cases where the expected values are all well above 5, the \$p\$-values from the tests will be close, if not identical. There isn’t really a drawback to using Fisher’s exact test, so it makes sense to just always use Fisher’s exact test. This argument is analogous to the argument that we should always use Welch’s instead of Student’s\$t\$-test by default ([Delacre et al., 2017](#ref-delacre2017psychologists)).

As an illustration, check out the results of a \$\\chi^{2}\$ test checking to see if transmission type (`am`, 0 = automatic, 1 = manual) and engine shape (`vs`, 0 = V-shaped, 1 = straight) are related using the `mtcars` dataset:

``` r
# run test
x.sq <- stats::chisq.test(x = mtcars$am, y = mtcars$vs)
# show observed values
x.sq$observed
```

<table class="table" style>
<caption>
Table 1: Observed Values
</caption>
<thead>
<tr>
<th style="text-align:right;">
0
</th>
<th style="text-align:right;">
1
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
7
</td>
</tr>
</tbody>
</table>

``` r
# show expected values
x.sq$expected
```

<table class="table" style>
<caption>
Table 2: Expected Values
</caption>
<thead>
<tr>
<th style="text-align:right;">
0
</th>
<th style="text-align:right;">
1
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
10.6875
</td>
<td style="text-align:right;">
8.3125
</td>
</tr>
<tr>
<td style="text-align:right;">
7.3125
</td>
<td style="text-align:right;">
5.6875
</td>
</tr>
</tbody>
</table>

``` r
x.sq
```


        Pearson's Chi-squared test with Yates' continuity correction

    data:  mtcars$am and mtcars$vs
    X-squared = 0.34754, df = 1, p-value = 0.5555

And here’s the results from Fisher’s exact test:


        Fisher's Exact Test for Count Data

    data:  mtcars$am and mtcars$vs
    p-value = 0.4727
    alternative hypothesis: true odds ratio is not equal to 1
    95 percent confidence interval:
      0.3825342 10.5916087
    sample estimates:
    odds ratio 
      1.956055 

Taking a look at the output above, the \$p\$-values are a bit different, but not too different. In these cases, while the Fisher’s exact test is more accurate, the \$\\chi^{2}\$ test is still fairly reliable. Assuming adequate sample size, the \$\\chi^2\$ test is usually fine.

Now I’m going to induce some expected values smaller than five by reducing the size of the sample:

``` r
# make sure randomness is reproducible
set.seed(352)
# take random sample of would-be row numbers
random.rows <- sample(1:nrow(mtcars), size = 20, replace = TRUE)
# reduce the dataset to only include the randomly drawn rownumbers
mtcars.reduced <- mtcars[random.rows, ]
# run test
x.sq <- stats::chisq.test(x = mtcars.reduced$am, y = mtcars.reduced$vs)
x.sq$expected
```

<table class="table" style>
<caption>
Table 3: Expected Values: Reduced Dataset
</caption>
<thead>
<tr>
<th style="text-align:right;">
0
</th>
<th style="text-align:right;">
1
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
5.4
</td>
<td style="text-align:right;">
6.6
</td>
</tr>
<tr>
<td style="text-align:right;">
3.6
</td>
<td style="text-align:right;">
4.4
</td>
</tr>
</tbody>
</table>

Gah! We have some violations. Let’s repeat the tests and compare them again:

``` r
x.sq
```


        Pearson's Chi-squared test with Yates' continuity correction

    data:  mtcars.reduced$am and mtcars.reduced$vs
    X-squared = 1.0185, df = 1, p-value = 0.3129

And here’s the results from Fisher’s exact test:


        Fisher's Exact Test for Count Data

    data:  mtcars.reduced$am and mtcars.reduced$vs
    p-value = 0.1968
    alternative hypothesis: true odds ratio is not equal to 1
    95 percent confidence interval:
      0.438792 55.616983
    sample estimates:
    odds ratio 
      3.895711 

Now the \$p\$-values are not-so-trivially different. In these circumstances, I’d go with the Fisher’s exact test.

# A Function for Tidy Crosstabulations and Fisher’s Exact Tests

The `stats` package, which comes preloaded in RStudio, provides `chi.sq` and `fisher.test` functions. Personally I don’t like the format of the output. I’ve written a function that will conduct Fisher’s exact test and provide output in tidy wide or long output, along with pre-formatted crosstabs with counts, percentages, and totals. Here’s the first Fisher’s exact test, but this time using `my.fisher`:

``` r
fisher.results <- my.fisher(mtcars, am, vs)
fisher.results$fish.test
```

<table class="table" style>
<caption>
Table 4: Fisher’s Exact Test

<div id="table1">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
odds.ratio
</th>
<th style="text-align:left;">
p.value
</th>
<th style="text-align:left;">
conf.low
</th>
<th style="text-align:left;">
conf.high
</th>
<th style="text-align:left;">
method
</th>
<th style="text-align:left;">
alternative
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
1.96
</td>
<td style="text-align:left;">
0.47
</td>
<td style="text-align:left;">
0.38
</td>
<td style="text-align:left;">
10.59
</td>
<td style="text-align:left;">
Fisher’s Exact Test for Count Data
</td>
<td style="text-align:left;">
two.sided
</td>
</tr>
</tbody>
</table>

Ah, that’s easier on the eyes. We can also get that in long format too:

``` r
fisher.results <- my.fisher(mtcars, am, vs)
fisher.results$fish.test.long
```

<table class="table" style>
<caption>
Table 5: Fisher’s Exact Test, Long Format

<div id="table1">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
Statistic
</th>
<th style="text-align:left;">
Value
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
odds.ratio
</td>
<td style="text-align:left;">
1.96
</td>
</tr>
<tr>
<td style="text-align:left;">
p.value
</td>
<td style="text-align:left;">
0.47
</td>
</tr>
<tr>
<td style="text-align:left;">
conf.low
</td>
<td style="text-align:left;">
0.38
</td>
</tr>
<tr>
<td style="text-align:left;">
conf.high
</td>
<td style="text-align:left;">
10.59
</td>
</tr>
<tr>
<td style="text-align:left;">
method
</td>
<td style="text-align:left;">
Fisher’s Exact Test for Count Data
</td>
</tr>
<tr>
<td style="text-align:left;">
alternative
</td>
<td style="text-align:left;">
two.sided
</td>
</tr>
</tbody>
</table>

You can also pass arguments to `janitor::fisher.test()`. For example, say you wanted to do a tight-sided single-tailed test. Just add `alternative = "greater"` to `my.fisher()` (note that the default is a two-sided test):

``` r
fisher.results <- my.fisher(mtcars, am, vs, alternative = "greater")
fisher.results$fish.test
```

<table class="table" style>
<caption>
Table 6: Fisher’s Exact Test With One-Sided Test

<div id="table1">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
odds.ratio
</th>
<th style="text-align:left;">
p.value
</th>
<th style="text-align:left;">
conf.low
</th>
<th style="text-align:left;">
conf.high
</th>
<th style="text-align:left;">
method
</th>
<th style="text-align:left;">
alternative
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
1.96
</td>
<td style="text-align:left;">
0.28
</td>
<td style="text-align:left;">
0.48
</td>
<td style="text-align:left;">
Inf
</td>
<td style="text-align:left;">
Fisher’s Exact Test for Count Data
</td>
<td style="text-align:left;">
greater
</td>
</tr>
</tbody>
</table>

Notice the \$p\$-value is smaller; that is because one-tailed tests are more powerful than two-tailed tests, assuming there is a real relationship at the population level, specifically in the direction of the test.

An added benefit of Fisher’s exact test is that unlike \$\\chi^{2}\$, it can give you a \$p\$-value in cases where *x*, *y*, or both have greater than 2 categories (resulting in a 2 \$\\times\$ 3 table, for example). This function can also accommodate nominal variables with greater than 2 categories. For example, a 2 \$\\times\$ 3 table between transmission type and number of cylinders (`cyl`, 4, 6, or 8):

``` r
fisher.results <- my.fisher(mtcars, am, cyl)
fisher.results$fish.test
```

<table class="table" style>
<caption>
Table 7: Fisher’s Exact Test, Transmission Type and Number of Cylinders

<div id="table1">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
p.value
</th>
<th style="text-align:left;">
method
</th>
<th style="text-align:left;">
alternative
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
0.01
</td>
<td style="text-align:left;">
Fisher’s Exact Test for Count Data
</td>
<td style="text-align:left;">
two.sided
</td>
</tr>
</tbody>
</table>

The odds ratio left out of the output in these cases, because it is not applicable to exact tests one or more variables has greater than 2 categories. In this case, you’d want to look at a crosstabulation of *x* and *y* to contextualize the \$p\$-value. This function provides them for you in a nice format with the help of the `janitor` package.

## Getting Crosstabs

``` r
fisher.results$crosstab.all
```

<table class="table" style>
<caption>
Table 8: Crosstab With Sample Total As Percentage Denominator

<div id="table7">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
cyl
</th>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
am
</td>
<td style="text-align:left;">
4
</td>
<td style="text-align:left;">
6
</td>
<td style="text-align:left;">
8
</td>
<td style="text-align:left;">
Total
</td>
</tr>
<tr>
<td style="text-align:left;">
0
</td>
<td style="text-align:left;">
9.38% (3)
</td>
<td style="text-align:left;">
12.50% (4)
</td>
<td style="text-align:left;">
37.50% (12)
</td>
<td style="text-align:left;">
59.38% (19)
</td>
</tr>
<tr>
<td style="text-align:left;">
1
</td>
<td style="text-align:left;">
25.00% (8)
</td>
<td style="text-align:left;">
9.38% (3)
</td>
<td style="text-align:left;">
6.25% (2)
</td>
<td style="text-align:left;">
40.62% (13)
</td>
</tr>
<tr>
<td style="text-align:left;">
Total
</td>
<td style="text-align:left;">
34.38% (11)
</td>
<td style="text-align:left;">
21.88% (7)
</td>
<td style="text-align:left;">
43.75% (14)
</td>
<td style="text-align:left;">
100.00% (32)
</td>
</tr>
</tbody>
</table>

[Table 7](#table7) shows the crosstab between transmission type and number of cylinders, with percentages and counts (the latter in parentheses). You also get row and column totals, along with the sample total in the right-most bottom corner. You also get two other crosstabs, one with percentages calculated relative the row total; the other with percentages calculated relative to the column total:

``` r
fisher.results$crosstab.row
```

<table class="table" style>
<caption>
Table 9: Crosstab With Row Total As Percentage Denominator

<div id="table1">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
cyl
</th>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
am
</td>
<td style="text-align:left;">
4
</td>
<td style="text-align:left;">
6
</td>
<td style="text-align:left;">
8
</td>
<td style="text-align:left;">
Total
</td>
</tr>
<tr>
<td style="text-align:left;">
0
</td>
<td style="text-align:left;">
15.79% (3)
</td>
<td style="text-align:left;">
21.05% (4)
</td>
<td style="text-align:left;">
63.16% (12)
</td>
<td style="text-align:left;">
100.00% (19)
</td>
</tr>
<tr>
<td style="text-align:left;">
1
</td>
<td style="text-align:left;">
61.54% (8)
</td>
<td style="text-align:left;">
23.08% (3)
</td>
<td style="text-align:left;">
15.38% (2)
</td>
<td style="text-align:left;">
100.00% (13)
</td>
</tr>
<tr>
<td style="text-align:left;">
Total
</td>
<td style="text-align:left;">
34.38% (11)
</td>
<td style="text-align:left;">
21.88% (7)
</td>
<td style="text-align:left;">
43.75% (14)
</td>
<td style="text-align:left;">
100.00% (32)
</td>
</tr>
</tbody>
</table>

Ah - with the row-wise percentages, you can see that the likely driver of statistical significance here is the inverse relationship between transmission type and cylinder. More than half of automatics have 8 cylinders, while more than half of manuals have only 4 cylinders. Looks like an <a href="https://en.wikipedia.org/wiki/Interaction_(statistics)" target="_blank">interaction effect</a>!

And here’s a crosstab with column-wise percentages:

``` r
fisher.results$crosstab.column
```

<table class="table" style>
<caption>
Table 10: Crosstab With Column Total As Percentage Denominator

<div id="table1">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
cyl
</th>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
am
</td>
<td style="text-align:left;">
4
</td>
<td style="text-align:left;">
6
</td>
<td style="text-align:left;">
8
</td>
<td style="text-align:left;">
Total
</td>
</tr>
<tr>
<td style="text-align:left;">
0
</td>
<td style="text-align:left;">
27.27% (3)
</td>
<td style="text-align:left;">
57.14% (4)
</td>
<td style="text-align:left;">
85.71% (12)
</td>
<td style="text-align:left;">
59.38% (19)
</td>
</tr>
<tr>
<td style="text-align:left;">
1
</td>
<td style="text-align:left;">
72.73% (8)
</td>
<td style="text-align:left;">
42.86% (3)
</td>
<td style="text-align:left;">
14.29% (2)
</td>
<td style="text-align:left;">
40.62% (13)
</td>
</tr>
<tr>
<td style="text-align:left;">
Total
</td>
<td style="text-align:left;">
100.00% (11)
</td>
<td style="text-align:left;">
100.00% (7)
</td>
<td style="text-align:left;">
100.00% (14)
</td>
<td style="text-align:left;">
100.00% (32)
</td>
</tr>
</tbody>
</table>

# Full Function

<div id='Function'/>

Copy-and-paste this function to use it in your own `R` projects. You will be prompted to install the `tidyverse` `broom`, and `janitor` packages if you do not already have them.

``` r
library(tidyverse)
library(janitor)
library(broom)
my.fisher <-
  # ... allows you to pass arguments to functions where ... are present
    function(data, ..., alternative = "two.sided") {
      # load required packages
      require(janitor)
      require(dplyr)
      require(broom)
      # make.pretty is a function-within-a-function that
      # adds totals and formatting using janitor package
      make.pretty <- function(tab, ...){
        tab %>%
        janitor::adorn_totals(where = c("row", "col")) %>%
        janitor::adorn_percentages(...) %>%
        janitor::adorn_pct_formatting(digits = 2) %>%
        janitor::adorn_ns() %>%
        janitor::adorn_title()
      }
      # this gets the n x n crosstab
      my.tab <- janitor::tabyl(
        data,
        ...,
        show_na = FALSE,
        show_missing_levels = FALSE)
      # use make.pretty to add totals and formatting
      my.tab.all <- make.pretty(tab = my.tab, denominator = "all")
      my.tab.row <- make.pretty(tab = my.tab, denominator = "row")
      my.tab.col <- make.pretty(tab = my.tab, denominator = "col")
      # get the fisher's exact test
      # can pass arguments to stats::fisher.test via ...
      # this determines which type of test to run, depending
      # on alternative = argument
      # default is "two.sided"
      if(alternative == "greater"){
      fish <- my.tab %>%
        janitor::fisher.test(., alternative = "greater")
      } else {
        if (alternative == "less"){
           fish <- my.tab %>%
        janitor::fisher.test(., alternative = "less")
        }
        fish <- my.tab %>%
          janitor::fisher.test(., alternative = "two.sided")
      }
      fish <- fish %>%
        # organizes output into a table
        broom::tidy() %>% 
        # rounds values
          dplyr::mutate_if(is.numeric, round, 2) %>%
        # makes them into character format with exactly 2 decimal points
        # so that numeric and character columns can be combined (for long format)
          dplyr::mutate_if(is.numeric, format, nsmall = 2)
      # if estimate column is present (in case of 2x2's), rename as "odds.ratio"
      if ("estimate" %in% names(fish)) {
        fish <- fish %>% dplyr::rename(odds.ratio = estimate) 
        fish.long <- fish %>% 
          # reshape to long format
          tidyr::pivot_longer(
            cols = everything(), 
            # name of column that gets the row values
            values_to = "Value", 
            # name of column that gets the column names
            names_to = "Statistic")
      }
      # this is the same thing as above, except this gets done when
      # estimate column is not present
      fish.long <- fish %>% 
          tidyr::pivot_longer(
            cols = everything(), 
            values_to = "Value", 
            names_to = "Statistic")
      # what does the function give as output?
      # left side of = is what you'll see with $
      # right side is object saved somewhere above
      return(list(
        crosstab.all = my.tab.all,
        crosstab.row = my.tab.row,
        crosstab.column = my.tab.col,
        fish.test = fish, 
        fish.test.long = fish.long))
    }
```

<div id="refs" class="references csl-bib-body hanging-indent" line-spacing="2">

<div id="ref-delacre2017psychologists" class="csl-entry">

Delacre, M., Lakens, D., & Leys, C. (2017). Why psychologists should by default use welch’s t-test instead of student’s t-test. *International Review of Social Psychology*, *30*(1). [Full Text](https://www.rips-irsp.com/articles/10.5334/irsp.82/)

</div>

</div>
