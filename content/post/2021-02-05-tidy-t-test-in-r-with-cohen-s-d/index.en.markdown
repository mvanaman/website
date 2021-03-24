---
title: Tidy T-Test in R With Cohen's D
author: Matthew E. Vanaman
date: '2021-01-31'
slug: [hello]
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
toc: yes
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
bibliography: "/Users/home/Documents/Research and Writing/Misc/MyRefs/Refs.bib"
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

The independent-samples \$t\$-test is one of the most commonly used statistical tests and is taught in most introductory statistics courses. In broad strokes, the independent-samples \$t\$-test is used to compare the means of some continuous variable between two qualitatively different groups. Associated with this mean difference is a \$p\$-value. Although there’s <a href="https://en.wikipedia.org/wiki/Sampling_distribution" target="_blank">more to the story</a>, for our purposes we can define the \$p\$-value as the probability of observing a difference between the means this large or larger, just due to chance, if there were in reality no difference between the groups. Although arbitrary, researchers will adopt (for most applications) a cutoff \$p\$-value of .05, a value referred to as alpha. If the \$p\$-value falls below alpha, we can conclude that the difference between the means is large enough to warrant further scrutiny.

# Where’s My Effect Size?

Effect sizes help contextualize the results of statistical tests, which consensus is more often beginning to recognize as the <a href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3444174/#i1949-8357-4-3-279-Kline1" target="_blank">least interesting part of statistical output</a>. As most introductory statistics courses will teach, you should use effect sizes like Cohen’s \$d\$ to get a sense of how impressive the magnitude of the mean difference is. The suggested rules of thumb for interpreting Cohen’s \$d\$ are ([Cohen, 2013](#ref-cohen2013statistical)):

-   \$\\le 0.2\$ is “small”
-   \$0.5\$ is “medium”
-   \$\\ge 0.8\$ is “large”

One would be forgiven for expecting to see this reported alongside the default output of standard statistical software. You almost always need it. I so far have been unable to find a function that provides this, and have become annoyed enough to finally create my own wrapper function that uses `R`’s built-in `t.test()` function and the `cohen.d()` function from the `psych` package. The output, unlike that of the default `t.test()` function, is in a <a href="https://vita.had.co.nz/papers/tidy-data.pdf" target="_blank">tidy format</a>.

Feel free to skip to the [full function](#Function) at the bottom; if you want a preview, keep reading.

# Walk-Through of `my.t()`

## Default T-Test in `R`

The `t.test()` function, which is built in to base-`R` along with other mainstream tests, has messy output. It also doesn’t provide the mean difference, the standard error of the difference, or Cohen’s \$d\$. As an example of its use, I’ll conduct a \$t\$-test to see whether automatic or manual transmissions are associated with greater miles per gallon. This data can be found in the `mtcars` dataset (from the `datasets` package, also available in base-`R`):

``` r
# t.test(dv ~ iv, data)
# am = automatic or manual
# mpg = miles per gallon
t.test(mpg ~ am, mtcars)
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

In this dataset, vehicles with automatic transmissions are coded as 0, and manuals as 1. The dependent variable is miles per gallon. The mean miles per gallon for vehicles with automatic transmissions (17.15) is quite a bit lower than for vehicles with manual transmissions (24.39). This difference is statistically significant, \$p = .001\$ (that’s less than an alpha of .05), meaning the means are different enough that we might interested in investigating a bit further. At this point, you’d usually calculate Cohen’s \$d\$.

## T-Test Using `my.t()`

``` r
my.t(mtcars, iv = "am", dv = "mpg")
```

<table class="table" style>
<caption>
Table 1: T-Test With my.t() (IV = Transmission and DV = Miles Per Gallon)

<div id="table1">

</div>

</caption>
<thead>
<tr>
<th style="text-align:right;">
mean.0
</th>
<th style="text-align:right;">
mean.1
</th>
<th style="text-align:right;">
sd.0
</th>
<th style="text-align:right;">
sd.1
</th>
<th style="text-align:right;">
mean.difference
</th>
<th style="text-align:right;">
std.err
</th>
<th style="text-align:right;">
CI.diff.lwr
</th>
<th style="text-align:right;">
CI.diff.upr
</th>
<th style="text-align:right;">
t
</th>
<th style="text-align:right;">
df
</th>
<th style="text-align:right;">
p.value
</th>
<th style="text-align:right;">
cohens.d
</th>
<th style="text-align:right;">
CI.d.lwr
</th>
<th style="text-align:right;">
CI.d.upr
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
17.15
</td>
<td style="text-align:right;">
24.39
</td>
<td style="text-align:right;">
3.83
</td>
<td style="text-align:right;">
6.17
</td>
<td style="text-align:right;">
-7.25
</td>
<td style="text-align:right;">
1.92
</td>
<td style="text-align:right;">
-11.28
</td>
<td style="text-align:right;">
-3.21
</td>
<td style="text-align:right;">
-3.77
</td>
<td style="text-align:right;">
18.33
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
-1.48
</td>
<td style="text-align:right;">
-2.27
</td>
<td style="text-align:right;">
-0.67
</td>
</tr>
</tbody>
</table>

The output in [Table 1](#table1) above provides all of the relevant statistics, clearly labeled, with Cohen’s \$d\$ included, and in a tidy format that can easily be exported to a .csv file, copied-and-pasted into Word, or printed within an R Markdown as a table as I have done here (using `knitr::kable()`).

## Output Formatting

Currently the output is in wide format, meaning that each statistic is its own column with the values for those statistics contained in the table row. If you prefer a different format, I have also added the `long =` argument that can optionally display output in long format, with all of the values in a single column with the statistic indicated by the row:

``` r
my.t(mtcars, iv = "am", dv = "mpg", long = TRUE)
```

<table class="table" style>
<caption>
Table 2: T-Test With my.t() in Long Format (IV = Transmission and DV = Miles Per Gallon)

<div id="table2">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
Statistic
</th>
<th style="text-align:right;">
Value
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
mean.0
</td>
<td style="text-align:right;">
17.15
</td>
</tr>
<tr>
<td style="text-align:left;">
mean.1
</td>
<td style="text-align:right;">
24.39
</td>
</tr>
<tr>
<td style="text-align:left;">
sd.0
</td>
<td style="text-align:right;">
3.83
</td>
</tr>
<tr>
<td style="text-align:left;">
sd.1
</td>
<td style="text-align:right;">
6.17
</td>
</tr>
<tr>
<td style="text-align:left;">
mean.difference
</td>
<td style="text-align:right;">
-7.25
</td>
</tr>
<tr>
<td style="text-align:left;">
std.err
</td>
<td style="text-align:right;">
1.92
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.diff.lwr
</td>
<td style="text-align:right;">
-11.28
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.diff.upr
</td>
<td style="text-align:right;">
-3.21
</td>
</tr>
<tr>
<td style="text-align:left;">
t
</td>
<td style="text-align:right;">
-3.77
</td>
</tr>
<tr>
<td style="text-align:left;">
df
</td>
<td style="text-align:right;">
18.33
</td>
</tr>
<tr>
<td style="text-align:left;">
p.value
</td>
<td style="text-align:right;">
0.00
</td>
</tr>
<tr>
<td style="text-align:left;">
cohens.d
</td>
<td style="text-align:right;">
-1.48
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.d.lwr
</td>
<td style="text-align:right;">
-2.27
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.d.upr
</td>
<td style="text-align:right;">
-0.67
</td>
</tr>
</tbody>
</table>

Personally, I like the long format, because usually I am running more than one \$t\$-test. For example, say that in my car-shopping, miles per gallon was one of just several criteria I were using to decide what kind of car to buy. In addition to miles per gallon, I might compare automatic vs manual transmissions in their level of *displacement*, which roughly describes “how fast a vehicle gets up and goes” (my <a href="https://matthewevanaman.netlify.app/img/dad.jpeg" target="_blank">dad</a> built engines for 40 years, so I hope he will forgive me if this description is less than accurate). Displacement is measured in cubic inches. Below is a table with the output of both tests side-by-side:

``` r
# conduct the tests
mpg.test <- my.t(mtcars, iv = "am", dv = "mpg", long = TRUE)
disp.test <- my.t(mtcars, iv = "am", dv = "disp", long = TRUE)
# relabel the columns with the name of the dv
mpg.test <- mpg.test %>% rename(MPG = Value)
disp.test <- disp.test %>% rename("Disp." = Value)
# combine into one dataframe
my.results <- left_join(mpg.test, disp.test, by = "Statistic")
# print the table
my.results
```

<table class="table" style>
<caption>
Table 3: Comparing Group Differences in MPG and Displacement

<div id="table3">

</div>

</caption>
<thead>
<tr>
<th style="text-align:left;">
Statistic
</th>
<th style="text-align:right;">
MPG
</th>
<th style="text-align:right;">
Disp.
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
mean.0
</td>
<td style="text-align:right;">
17.15
</td>
<td style="text-align:right;">
290.38
</td>
</tr>
<tr>
<td style="text-align:left;">
mean.1
</td>
<td style="text-align:right;">
24.39
</td>
<td style="text-align:right;">
143.53
</td>
</tr>
<tr>
<td style="text-align:left;">
sd.0
</td>
<td style="text-align:right;">
3.83
</td>
<td style="text-align:right;">
110.17
</td>
</tr>
<tr>
<td style="text-align:left;">
sd.1
</td>
<td style="text-align:right;">
6.17
</td>
<td style="text-align:right;">
87.20
</td>
</tr>
<tr>
<td style="text-align:left;">
mean.difference
</td>
<td style="text-align:right;">
-7.25
</td>
<td style="text-align:right;">
146.85
</td>
</tr>
<tr>
<td style="text-align:left;">
std.err
</td>
<td style="text-align:right;">
1.92
</td>
<td style="text-align:right;">
34.98
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.diff.lwr
</td>
<td style="text-align:right;">
-11.28
</td>
<td style="text-align:right;">
75.33
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.diff.upr
</td>
<td style="text-align:right;">
-3.21
</td>
<td style="text-align:right;">
218.37
</td>
</tr>
<tr>
<td style="text-align:left;">
t
</td>
<td style="text-align:right;">
-3.77
</td>
<td style="text-align:right;">
4.20
</td>
</tr>
<tr>
<td style="text-align:left;">
df
</td>
<td style="text-align:right;">
18.33
</td>
<td style="text-align:right;">
29.26
</td>
</tr>
<tr>
<td style="text-align:left;">
p.value
</td>
<td style="text-align:right;">
0.00
</td>
<td style="text-align:right;">
0.00
</td>
</tr>
<tr>
<td style="text-align:left;">
cohens.d
</td>
<td style="text-align:right;">
-1.48
</td>
<td style="text-align:right;">
1.45
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.d.lwr
</td>
<td style="text-align:right;">
-2.27
</td>
<td style="text-align:right;">
0.64
</td>
</tr>
<tr>
<td style="text-align:left;">
CI.d.upr
</td>
<td style="text-align:right;">
-0.67
</td>
<td style="text-align:right;">
2.23
</td>
</tr>
</tbody>
</table>

As shown in [Table 3](#table3), the mean difference in displacement between automatics and manuals is 146.85, meaning that automatic transmissions are associated with an extra 146.85 cubic inches of displacement on average. So while you might lose \~7 miles to the gallon by going with an automatic transmission, you can also expect to gain \~147 cubic inches of displacement.

OK, but what the heck does that mean? Is that an impressive number? It’s hard to tell for those who don’t spend time dealing with vehicle displacement. At least with miles per gallon, I can readily discern about how much money I would save if my vehicle got an extra 7 miles to the gallon. Displacement is a black box to me - I can’t tell if that mean difference in displacement is meaningful or not, especially not compared to how meaningful the difference in miles per gallon was.

In [Table 3](#table3), the Cohen’s \$d\$ statistics for miles per gallon and displacement are conveniently side-by-side. Both are very large effect sizes - quite a bit larger than 0.8, the rule of thumb for a large. Although the sign for displacement is negative (indicating that manual transmissions are associated with lower displacement than automatics), the absolute value is very close to that of miles per gallon. It seems like whatever gas I’d save with a manual transmission, I’d lose an equally-meaningful amount of displacement per cubic inch. That is, with Cohen’s \$d\$, I can see that the *magnitude* of the differences between between automatic and manual transmissions (effect sizes of 1.53 and -1.49, respectively) are pretty similar, even though the mean differences themselves (7.24 miles per gallon and -146.85 cubic inches of displacement, respectively) are not comparable numbers.

Of course, miles per gallon and displacement might be too conceptually different to be compared meaningfully without added context. These tests would be most helpful if you also knew what adding 146.85 of displacement per cubic inch would feel like in terms of pushing your back against the seat as you accelerate in your new car. Maybe you would need, say, twice as much displacement (or 293.7 cubic inches) in order for a gain in displacement to be noticeable. But in the absence of better information, it’s nice to know for the sake of car shopping that the *magnitude of the difference between the groups* is basically the same for miles per gallon and displacement.

## Paired Samples T-Tests

The function can handle repeated-measures data. For example, say we had data from a study that looked to see whether anxiety decreases following a round of exercise. I have two columns in my dataset: anxiety score, and time.point (before or after exercise). This would call for a paired samples t-test.

All you have to do is tack on the `paired = TRUE` argument to `my.t`:

``` r
my.t(anxiety, iv = "time.point", dv = "anxiety", paired = TRUE)
```

<table class="table" style>
<caption>
Table 4: Comparing Anxiety Before and After Exercise

<div id="table4">

</div>

</caption>
<thead>
<tr>
<th style="text-align:right;">
mean.before
</th>
<th style="text-align:right;">
mean.after
</th>
<th style="text-align:right;">
sd.before
</th>
<th style="text-align:right;">
sd.after
</th>
<th style="text-align:right;">
mean.difference
</th>
<th style="text-align:right;">
std.err
</th>
<th style="text-align:right;">
CI.diff.lwr
</th>
<th style="text-align:right;">
CI.diff.upr
</th>
<th style="text-align:right;">
t
</th>
<th style="text-align:right;">
df
</th>
<th style="text-align:right;">
p.value
</th>
<th style="text-align:right;">
cohens.d
</th>
<th style="text-align:right;">
CI.d.lwr
</th>
<th style="text-align:right;">
CI.d.upr
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
20.66
</td>
<td style="text-align:right;">
16.96
</td>
<td style="text-align:right;">
6.61
</td>
<td style="text-align:right;">
8.27
</td>
<td style="text-align:right;">
3.7
</td>
<td style="text-align:right;">
1.12
</td>
<td style="text-align:right;">
1.41
</td>
<td style="text-align:right;">
5.99
</td>
<td style="text-align:right;">
3.3
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0.6
</td>
<td style="text-align:right;">
0.21
</td>
<td style="text-align:right;">
1
</td>
</tr>
</tbody>
</table>

Because the after group had a lower anxiety score than the before group, the mean difference and Cohen’s \$d\$ are positive. The reason is that the default behavior of the function is to subtract in the order of the level of the dv, which in this case is:

``` r
levels(anxiety$time.point)
```

    [1] "before" "after" 

In this case, the “before” time point is the first level, so the mean difference and Cohen’s \$d\$ is based on mean.before - mean.after. But this is an odd order if you’re trying to interpret the mean difference as the mean change in anxiety over time. If thinking that way, the mean difference is really the mean change, which was a decrease in anxiety, which means the mean difference should be negative. Just change the order of the levels:

``` r
# change order of levels
anxiety$time.point <- factor(anxiety$time.point, levels = c("after", "before"))
# re-run t-test
my.t(anxiety, iv = "time.point", dv = "anxiety", paired = TRUE)
```

<table class="table" style>
<caption>
Table 5: Switched Level Order: Comparing Anxiety Before and After Exercise

<div id="table5">

</div>

</caption>
<thead>
<tr>
<th style="text-align:right;">
mean.after
</th>
<th style="text-align:right;">
mean.before
</th>
<th style="text-align:right;">
sd.after
</th>
<th style="text-align:right;">
sd.before
</th>
<th style="text-align:right;">
mean.difference
</th>
<th style="text-align:right;">
std.err
</th>
<th style="text-align:right;">
CI.diff.lwr
</th>
<th style="text-align:right;">
CI.diff.upr
</th>
<th style="text-align:right;">
t
</th>
<th style="text-align:right;">
df
</th>
<th style="text-align:right;">
p.value
</th>
<th style="text-align:right;">
cohens.d
</th>
<th style="text-align:right;">
CI.d.lwr
</th>
<th style="text-align:right;">
CI.d.upr
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
16.96
</td>
<td style="text-align:right;">
20.66
</td>
<td style="text-align:right;">
8.27
</td>
<td style="text-align:right;">
6.61
</td>
<td style="text-align:right;">
-3.7
</td>
<td style="text-align:right;">
1.12
</td>
<td style="text-align:right;">
-5.99
</td>
<td style="text-align:right;">
-1.41
</td>
<td style="text-align:right;">
-3.3
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
-0.6
</td>
<td style="text-align:right;">
-1
</td>
<td style="text-align:right;">
-0.21
</td>
</tr>
</tbody>
</table>

The mean difference and Cohen’s \$d\$ are now negative to reflect that anxiety decreased after exercise.

# Full Function

<div id='Function'/>

Copy-and-paste this function to use it in your own `R` projects. You will be prompted to install the `tidyverse` and `psych` packages if you do not already have them.

``` r
my.t <-
  function(data, iv, dv, long = FALSE, ...) {
    require(dplyr)
    require(effectsize)
    require(broom)
    require(stringr)
    means.SDs <- suppressMessages(
      data %>%
        group_by_at(iv) %>%
        summarise(
          mean = mean(eval(parse(text = dv)), na.rm = TRUE),
          sd = sd(eval(parse(text = dv)), na.rm = TRUE)) %>%
        pivot_wider(names_from = iv, values_from =  c("mean", "sd"), names_sep = ".")
    )
    iv.2 <- rlang::sym(iv)
    dv.2 <- rlang::sym(dv)
    form <- expr(!! dv.2 ~ !! iv.2)
    t.tests <- t.test(eval(form), data, var.equal = FALSE, ...) 
    Ds <- cohens_d(eval(form), data = data, ...) %>% as.tibble
    stats <- t.tests %>% tidy
    std.err <- t.tests$stderr
    cols <- c("conf.low", "conf.high", "statistic", "parameter", "p.value")
    means.table <-
      cbind(means.SDs, stats[, "estimate"], std.err, stats[, cols], select(Ds, -CI))
    means.table <- means.table %>%
      rename(
        mean.difference = estimate,
        t = statistic,
        df = parameter,
        CI.diff.lwr = conf.low,
        CI.diff.upr = conf.high,
        cohens.d = Cohens_d,
        CI.d.lwr = CI_low,
        CI.d.upr = CI_high
      ) %>%
      mutate_if(is.numeric, round, 3)
    rownames(means.table) <- NULL
    if (long == TRUE) {
      means.table.long <-
        means.table %>% t %>% data.frame("Value" = .) %>% rownames_to_column("Statistic")
      return(means.table.long)
    } else{
      return(means.table)
    }
  }
```

# References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-cohen2013statistical" class="csl-entry">

Cohen, J. (2013). *Statistical power analysis for the behavioral sciences*. Academic press.

</div>

</div>

<div id="disqus_thread">

</div>

<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://matthews-blog-1.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>
Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a>
</noscript>
