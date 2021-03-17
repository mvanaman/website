---
title: Tidy T-Test in R With Cohen's D
author: Matthew E. Vanaman
date: '2021-01-31'
slug: []
categories: 
  - efficiency
  - functions
tags:
  - r
  - tidy
  - t-test
  - effect-size
  - cohens-d
lastmod: 2021-03-17
keywords: 
  - r
  - rstudio
  - tidy
  - t-test
  - effect-size
  - cohens-d
  - matthew-e-vanaman
  - matthew-vanaman
  - matt-vanaman
description: ''
comment: yes
output: 
  md_document:
    toc: true
    fig_cap: yes
postMetaInFooter: yes
hiddenFromHomePage: no
contentCopyright: no
reward: no
mathjax: yes
mathjaxEnableSingleDollar: yes
mathjaxEnableAutoNumber: yes
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





The independent-samples \$t\$-test is one of the most commonly used statistical tests and is taught in most introductory statistics courses. In broad strokes, the independent-samples \$t\$-test is used to compare the means of some continuous variable between two qualitatively different groups. Associated with this mean difference is a \$p\$-value. Although there's <a href="https://en.wikipedia.org/wiki/Sampling_distribution" target="_blank">more to the story</a>, for our purposes we can define the \$p\$-value as the probability of observing a difference between the means this large or larger, just due to chance, if there were in reality no difference between the groups. Although arbitrary, researchers will adopt (for most applications) a cutoff \$p\$-value of .05, a value referred to as alpha. If the \$p\$-value falls below alpha, we can conclude that the difference between the means is large enough to warrant further scrutiny.

# Where's My Effect Size?

Effect sizes help contextualize the results of statistical tests, which consensus is more often beginning to recognize as the least interesting part of statistical output [@sullivan2012using]. Indeed, researchers have been pressured to report measures of effect size alongside descriptive and inferential statistics in their published work for about two decades now [@coe2002effect]. As most introductory statistics courses will teach, Cohen's \$d\$ follows some rules of thumb that help give a sense of how impressive the magnitude of the mean difference is [@cohen2013statistical]:

-   \$\\le 0.2\$ is "small"
-   \$0.5\$ is "medium"
-   \$\\ge 0.8\$ is "large"

One would be forgiven for expecting to see this reported alongside the default output of standard statistical software. You almost always need it. I so far have been unable to find a function that provides this, and have become annoyed enough to finally create my own wrapper function that uses `R`'s built-in `t.test()` function and the `cohen.d()` function from the `psych` package. The output, unlike that of the default `t.test()` function, is in a <a href="https://vita.had.co.nz/papers/tidy-data.pdf" target="_blank">tidy format</a>.

Feel free to skip to the [full function](#Function) at the bottom; if you want a preview, keep reading.

# Walk-Through of `my.t()`

## Default T-Test in `R`

The `t.test()` function, which is built in to base-`R` along with other mainstream tests, has messy output. It also doesn't provide the mean difference, the standard error of the difference, or Cohen's \$d\$. As an example of its use, I'll conduct a \$t\$-test to see whether automatic or manual transmissions are associated with greater miles per gallon. This data can be found in the `mtcars` dataset (from the `datasets` package, also available in base-`R`):


```r
# t.test(DV ~ IV, data)
# am = automatic or manual
# mpg = miles per gallon
t.test(mpg ~ am, mtcars)
```

```

	Welch Two Sample t-test

data:  mpg by am
t = -3.7671, df = 18.332, p-value = 0.001374
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -11.280194  -3.209684
sample estimates:
mean in group 0 mean in group 1 
       17.14737        24.39231 
```

In this dataset, vehicles with automatic transmissions are coded as 0, and manuals as 1. The dependent variable is miles per gallon. The mean miles per gallon for vehicles with automatic transmissions (17.15) is quite a bit lower than for vehicles with manual transmissions (24.39). This difference is statistically significant, \$p = .001\$ (that's less than an alpha of .05), meaning the means are different enough that we might interested in investigating a bit further. At this point, you'd usually calculate Cohen's \$d\$.

## T-Test Using `my.t()`


```r
my.t(mtcars, IV = am, DV = mpg)
```

<table class="table" style="">
<caption>(\#tab:unnamed-chunk-4)T-Test With `my.t` (IV = Transmission and DV = Miles Per Gallon) <div id="table1"></div>
</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> 0.mean </th>
   <th style="text-align:right;"> 1.mean </th>
   <th style="text-align:right;"> 0.sd </th>
   <th style="text-align:right;"> 1.sd </th>
   <th style="text-align:right;"> mean.difference </th>
   <th style="text-align:right;"> std.err </th>
   <th style="text-align:right;"> conf.low </th>
   <th style="text-align:right;"> conf.high </th>
   <th style="text-align:right;"> t </th>
   <th style="text-align:right;"> df </th>
   <th style="text-align:right;"> p.value </th>
   <th style="text-align:left;"> cohens.d </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 17.15 </td>
   <td style="text-align:right;"> 24.39 </td>
   <td style="text-align:right;"> 3.83 </td>
   <td style="text-align:right;"> 6.17 </td>
   <td style="text-align:right;"> -7.24 </td>
   <td style="text-align:right;"> 1.92 </td>
   <td style="text-align:right;"> -11.28 </td>
   <td style="text-align:right;"> -3.21 </td>
   <td style="text-align:right;"> -3.77 </td>
   <td style="text-align:right;"> 18.33 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> 1.53 </td>
  </tr>
</tbody>
</table>

The output in [Table 1](#table1) above provides all of the relevant statistics, clearly labeled, with Cohen's \$d\$ included, and in a tidy format that can easily be exported to a .csv file, copied-and-pasted into Word, or printed within an R Markdown as a table as I have done here (using `knitr::kable()`).

## Output Formatting

Currently the output is in wide format, meaning that each statistic is its own column with the values for those statistics contained in the table row. If you prefer a different format, I have also added the `long =` argument that can optionally display output in long format, with all of the values in a single column with the statistic indicated by the row:


```r
my.t(mtcars, IV = am, DV = mpg, long = TRUE)
```

<table class="table" style="">
<caption>(\#tab:unnamed-chunk-6)T-Test With `my.t` in Long Format (IV = Transmission and DV = Miles Per Gallon) <div id="table2"></div>
</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Statistic </th>
   <th style="text-align:right;"> Value </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 0.mean </td>
   <td style="text-align:right;"> 17.15 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1.mean </td>
   <td style="text-align:right;"> 24.39 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 0.sd </td>
   <td style="text-align:right;"> 3.83 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1.sd </td>
   <td style="text-align:right;"> 6.17 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mean.difference </td>
   <td style="text-align:right;"> -7.24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> std.err </td>
   <td style="text-align:right;"> 1.92 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> conf.low </td>
   <td style="text-align:right;"> -11.28 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> conf.high </td>
   <td style="text-align:right;"> -3.21 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> t </td>
   <td style="text-align:right;"> -3.77 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> df </td>
   <td style="text-align:right;"> 18.33 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> p.value </td>
   <td style="text-align:right;"> 0.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cohens.d </td>
   <td style="text-align:right;"> 1.53 </td>
  </tr>
</tbody>
</table>

Personally, I like the long format, because usually I am running more than one \$t\$-test. For example, say that in my car-shopping, miles per gallon was one of just several criteria I were using to decide what kind of car to buy. In addition to miles per gallon, I might compare automatic vs manual transmissions in their level of *displacement*, which roughly describes "how fast a vehicle gets up and goes" (my <a href="https://matthewevanaman.netlify.app/img/dad.jpeg" target="_blank">dad</a> built engines for 40 years, so I hope he will forgive me if this description is less than accurate). Displacement is measured in cubic inches. Below is a table with the output of both tests side-by-side:


```r
# conduct the tests
mpg.test <- my.t(mtcars, IV = am, DV = mpg, long = TRUE)
disp.test <- my.t(mtcars, IV = am, DV = disp, long = TRUE)
# relabel the columns with the name of the DV
mpg.test <- mpg.test %>% rename(MPG = Value)
disp.test <- disp.test %>% rename("Disp." = Value)
# combine into one dataframe
my.results <- left_join(mpg.test, disp.test, by = "Statistic")
# print the table
my.results
```

<table class="table" style="">
<caption>(\#tab:unnamed-chunk-8)Comparing Group Differences in MPG and Displacement <div id="table3"></div>
</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Statistic </th>
   <th style="text-align:right;"> MPG </th>
   <th style="text-align:right;"> Disp. </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 0.mean </td>
   <td style="text-align:right;"> 17.15 </td>
   <td style="text-align:right;"> 290.38 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1.mean </td>
   <td style="text-align:right;"> 24.39 </td>
   <td style="text-align:right;"> 143.53 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 0.sd </td>
   <td style="text-align:right;"> 3.83 </td>
   <td style="text-align:right;"> 110.17 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1.sd </td>
   <td style="text-align:right;"> 6.17 </td>
   <td style="text-align:right;"> 87.20 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mean.difference </td>
   <td style="text-align:right;"> -7.24 </td>
   <td style="text-align:right;"> 146.85 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> std.err </td>
   <td style="text-align:right;"> 1.92 </td>
   <td style="text-align:right;"> 34.98 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> conf.low </td>
   <td style="text-align:right;"> -11.28 </td>
   <td style="text-align:right;"> 75.33 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> conf.high </td>
   <td style="text-align:right;"> -3.21 </td>
   <td style="text-align:right;"> 218.37 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> t </td>
   <td style="text-align:right;"> -3.77 </td>
   <td style="text-align:right;"> 4.20 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> df </td>
   <td style="text-align:right;"> 18.33 </td>
   <td style="text-align:right;"> 29.26 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> p.value </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> cohens.d </td>
   <td style="text-align:right;"> 1.53 </td>
   <td style="text-align:right;"> -1.49 </td>
  </tr>
</tbody>
</table>

As shown in [Table 3](#table3), the mean difference in displacement between automatics and manuals is 146.85, meaning that automatic transmissions are associated with an extra 146.85 cubic inches of displacement on average. So while you might lose \~7 miles to the gallon by going with an automatic transmission, you can also expect to gain \~147 cubic inches of displacement.

OK, but what the heck does that mean? Is that an impressive number? It's hard to tell for those who don't spend time dealing with vehicle displacement. At least with miles per gallon, I can readily discern about how much money I would save if my vehicle got an extra 7 miles to the gallon. Displacement is a black box to me - I can't tell if that mean difference in displacement is meaningful or not, especially not compared to how meaningful the difference in miles per gallon was.

In [Table 3](#table3), the Cohen's \$d\$ statistics for miles per gallon and displacement are conveniently side-by-side. Both are very large effect sizes - quite a bit larger than 0.8, the rule of thumb for a large. Although the sign for displacement is negative (indicating that manual transmissions are associated with lower displacement than automatics), the absolute value is very close to that of miles per gallon. It seems like whatever gas I'd save with a manual transmission, I'd lose an equally-meaningful amount of displacement per cubic inch. That is, with Cohen's \$d\$, I can see that the *magnitude* of the differences between between automatic and manual transmissions (effect sizes of 1.53 and -1.49, respectively) are pretty similar, even though the mean differences themselves (7.24 miles per gallon and -146.85 cubic inches of displacement, respectively) are not comparable numbers.

Of course, miles per gallon and displacement might be too conceptually different to be compared meaningfully without added context. These tests would be most helpful if you also knew what adding 146.85 of displacement per cubic inch would feel like in terms of pushing your back against the seat as you accelerate in your new car. Maybe you would need, say, twice as much displacement (or 293.7 cubic inches) in order for a gain in displacement to be noticeable. But in the absence of better information, it's nice to know for the sake of car shopping that the *magnitude of the difference between the groups* is basically the same for miles per gallon and displacement.

# Full Function 

<div id='Function'/>

Copy-and-paste this function to use it in your own `R` projects. You will be prompted to install the `tidyverse` and `psych` packages if you do not already have them.


```r
my.t <-
    function(data, IV, DV, long = FALSE, ...) {
      require(tidyverse)
      require(psych)
      IV.1 <- enquo(IV)
      DV.1 <- enquo(DV)
      means <- suppressMessages(
        data %>%
        group_by(!!IV.1) %>%
        summarise(mean = mean(!!DV.1, na.rm = TRUE)) %>%
          pivot_wider(names_from = !!IV.1, values_from =  "mean")
        )
      names(means) <- paste(names(means), "mean", sep = ".")
      SDs <- suppressMessages(
        data %>%
          group_by(!!IV.1) %>%
          summarise(sd = sd(!!DV.1, na.rm = TRUE)) %>%
          pivot_wider(names_from = !!IV.1, values_from =  "sd")
      )
      names(SDs) <- paste(names(SDs), "sd", sep = ".")

      IV.2 <- rlang::sym(rlang::as_label(rlang::enquo(IV)))
      DV.2 <- rlang::sym(rlang::as_label(rlang::enquo(DV)))
      form <- expr(!! DV.2 ~ !! IV.2)
      t.tests <- t.test(eval(form), data, var.equal = FALSE)
      stats <- t.tests %>% broom::glance(x)
      std.err <- t.tests$stderr
      Ds <-
        cohen.d(x = data %>% select(!!DV.1, !!IV.1),
                group = deparse(substitute(IV)))$cohen.d %>%
        as.data.frame %>%
        select(effect) %>%
        round(2) %>%
        format(nsmall = 2)
      cols <- c("conf.low", "conf.high", "statistic", "parameter", "p.value")
      means.table <-
        cbind(means, SDs, stats[, "estimate"], std.err, stats[, cols], Ds)
      means.table <- means.table %>%
        rename(mean.difference = estimate, cohens.d = effect, t = statistic, df = parameter)
      rownames(means.table) <- NULL
      if (long == TRUE) {
        means.table.long <-
          means.table %>% 
          t %>% 
          data.frame("Value" = .) %>% 
          rownames_to_column("Statistic") %>% 
          mutate(Value = as.numeric(as.character(Value))) %>% 
          mutate(Value = round(Value, 2))
        return(means.table.long)
      } else{
        return(means.table)
      }
    }
```

# References
