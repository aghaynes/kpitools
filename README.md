<!-- README.md is generated from README.Rmd. Please edit that file -->

kpitools
========

[![](https://img.shields.io/badge/dev%20version-0.0.1.9000-blue.svg)](https://github.com/CTU-Bern/kpitools)
[![R-CMD-fullcheck](https://github.com/CTU-Bern/kpitools/actions/workflows/R-CMD-full.yaml/badge.svg)](https://github.com/CTU-Bern/kpitools/actions/workflows/R-CMD-full.yaml)

Tools for creating key performance indicator (KPI) reports.

Example usage
=============

The package can be installed from
[github](https://github.com/CTU-Bern/kpitools) via the `remotes` package

    # install.packages("remotes")
    remotes::install_github("CTU-Bern/kpitools")

Note that `remotes` treats any warnings (e.g. that a certain package was
built under a different version of R) as errors. If you see such an
error, run the following line and try again:

    Sys.setenv(R_REMOTES_NO_ERRORS_FROM_WARNINGS = "true")

And loaded via

    library(kpitools)

Calculating KPIs
----------------

The main function is the `kpi` function. A dataframe is passed to it
together with the `var`iable that is of interest for the current KPI. A
summary function also needs to be passed which determines how the KPI is
calculated.

    data(mtcars)

    mtcars$highmpg <- mtcars$mpg > 20

    mtcars %>%
      kpi(var = "highmpg",             # variable to be summarized (focus of the KPI)
          kpi_fn = kpi_fn_perc,        # summary function
          txt = "Percentage MPG > 25") # (optional) nicer text to add to tables

    ## $overall
    ## $overall$calc
    ##                   txt  n  N  stat
    ## 1 Percentage MPG > 25 14 32 43.75
    ## 
    ## $overall$outlier
    ## [1] txt  n    N    stat
    ## <0 rows> (or 0-length row.names)
    ## 
    ## 
    ## attr(,"class")
    ## [1] "kpi"  "list"

It is also possible to calculate the KPIs over specific groups via the
`by` option:

    mtcars %>%
      kpi(var = "highmpg",             # variable to be summarized (focus of the KPI)
          kpi_fn = kpi_fn_perc,        # summary function
          txt = "Percentage MPG > 20", # (optional) nicer text to add to tables
          by = "am")                   # (optional) stratifying variable

    ## $overall
    ## $overall$calc
    ##                   txt  n  N  stat
    ## 1 Percentage MPG > 20 14 32 43.75
    ## 
    ## $overall$outlier
    ## [1] txt  n    N    stat
    ## <0 rows> (or 0-length row.names)
    ## 
    ## 
    ## $am
    ## $am$calc
    ## # A tibble: 2 x 5
    ##   txt                    am     n     N  stat
    ##   <chr>               <dbl> <int> <int> <dbl>
    ## 1 Percentage MPG > 20     0     4    19  21.1
    ## 2 Percentage MPG > 20     1    10    13  76.9
    ## 
    ## $am$outlier
    ## # A tibble: 0 x 5
    ## # … with 5 variables: txt <chr>, am <dbl>, n <int>, N <int>, stat <dbl>
    ## 
    ## 
    ## attr(,"class")
    ## [1] "kpi"  "list"

The `by` argument also works over multiple variables:

    mtcars %>%
      kpi(var = "highmpg",             # variable to be summarized (focus of the KPI)
          kpi_fn = kpi_fn_perc,        # summary function
          txt = "Percentage MPG > 20", # (optional) nicer text to add to tables
          by = c("am", "gear"))        # (optional) stratifying variables

    ## $overall
    ## $overall$calc
    ##                   txt  n  N  stat
    ## 1 Percentage MPG > 20 14 32 43.75
    ## 
    ## $overall$outlier
    ## [1] txt  n    N    stat
    ## <0 rows> (or 0-length row.names)
    ## 
    ## 
    ## $am
    ## $am$calc
    ## # A tibble: 2 x 5
    ##   txt                    am     n     N  stat
    ##   <chr>               <dbl> <int> <int> <dbl>
    ## 1 Percentage MPG > 20     0     4    19  21.1
    ## 2 Percentage MPG > 20     1    10    13  76.9
    ## 
    ## $am$outlier
    ## # A tibble: 0 x 5
    ## # … with 5 variables: txt <chr>, am <dbl>, n <int>, N <int>, stat <dbl>
    ## 
    ## 
    ## $gear
    ## $gear$calc
    ## # A tibble: 3 x 5
    ##   txt                  gear     n     N  stat
    ##   <chr>               <dbl> <int> <int> <dbl>
    ## 1 Percentage MPG > 20     3     2    15  13.3
    ## 2 Percentage MPG > 20     4    10    12  83.3
    ## 3 Percentage MPG > 20     5     2     5  40  
    ## 
    ## $gear$outlier
    ## # A tibble: 0 x 5
    ## # … with 5 variables: txt <chr>, gear <dbl>, n <int>, N <int>, stat <dbl>
    ## 
    ## 
    ## attr(,"class")
    ## [1] "kpi"  "list"

KPIs are often reported with cutoffs (for use with a traffic light type
approach). Cutoffs can be passed to `kpi`, with or without labels.

    # without labels
    mtcars %>%
      kpi(var = "highmpg",                # variable to be summarized (focus of the KPI) 
          kpi_fn = kpi_fn_perc,           # summary function 
          txt = "Percentage MPG > 20",    # (optional) nicer text to add to tables
          by = "am",                      # (optional) stratifying variable
          cutpoints = c(0,33.3,66.6,100)) # (optional) cutoff points

Add in cutoffs for quality with labels

    mtcars %>%
      kpi(var = "highmpg",                        # variable to be summarized (focus of the KPI)  
          kpi_fn = kpi_fn_perc,                   # summary function   
          txt = "Percentage MPG > 20",            # (optional) nicer text to add to tables 
          by = "am",                              # (optional) stratifying variable 
          cutpoints = c(0,33.3,66.6,100),         # (optional) cutoff points 
          cutlabels = c("Low", "Medium", "High")) # (optional) labels for the cutoff points

    ## $overall
    ## $overall$calc
    ##                   txt  n  N  stat         cut cutgrp
    ## 1 Percentage MPG > 20 14 32 43.75 (33.3,66.6] Medium
    ## 
    ## $overall$outlier
    ## [1] txt  n    N    stat
    ## <0 rows> (or 0-length row.names)
    ## 
    ## 
    ## $am
    ## $am$calc
    ## # A tibble: 2 x 7
    ##   txt                    am     n     N  stat cut        cutgrp
    ##   <chr>               <dbl> <int> <int> <dbl> <fct>      <fct> 
    ## 1 Percentage MPG > 20     0     4    19  21.1 (0,33.3]   Low   
    ## 2 Percentage MPG > 20     1    10    13  76.9 (66.6,100] High  
    ## 
    ## $am$outlier
    ## # A tibble: 0 x 5
    ## # … with 5 variables: txt <chr>, am <dbl>, n <int>, N <int>, stat <dbl>
    ## 
    ## 
    ## attr(,"class")
    ## [1] "kpi"  "list"

Visualising variation among levels of the study hierarchy
---------------------------------------------------------

There is a plot method for the output from `kpi` which returns a list of
`ggplot2` objects.

    kpi <- mtcars %>%
      kpi(var = "highmpg", 
          kpi_fn = kpi_fn_perc, 
          txt = "Percentage MPG > 20",
          by = "cyl")

    (plot <- plot(kpi))

    ## $cyl

![](man/figures/README-unnamed-chunk-7-1.png)

`ggplot2` objects allow for easy modification after their creation for
further customisation.

    plot$cyl +
      theme_bw() +
      theme(legend.position = "bottom",
            axis.text.y = element_blank(),
            axis.title.y = element_blank(),
            axis.ticks.y = element_blank(),
            legend.margin = margin(0,0,0,0)
            )

![](man/figures/README-unnamed-chunk-8-1.png)

A `ggplot2` theme is also provided which implements modifications
similar to those above - `theme_kpitools`.

    plot$cyl +
      theme_kpitools()

![](man/figures/README-unnamed-chunk-9-1.png)
