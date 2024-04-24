
<!-- README.md is generated from README.Rmd. Please edit that file -->

# kpitools [<img src='man/figures/logo.png' align="right" width="200">](https://ctu-bern.github.io/kpitools)

[![](https://img.shields.io/badge/dev%20version-0.2.3-blue.svg)](https://github.com/CTU-Bern/kpitools)
[![R-CMD-fullcheck](https://github.com/CTU-Bern/kpitools/actions/workflows/R-CMD-full.yaml/badge.svg)](https://github.com/CTU-Bern/kpitools/actions/workflows/R-CMD-full.yaml)

Tools for creating key performance indicator (KPI) reports.

The package can be installed from the CTU Bern universe via

``` r
install.packages('kpitools', repos = c('https://ctu-bern.r-universe.dev', 'https://cloud.r-project.org'))
```

The package can also be installed from
[github](https://github.com/CTU-Bern/kpitools) via the `remotes` package

``` r
# install.packages("remotes")
remotes::install_github("CTU-Bern/kpitools")
```

Note that `remotes` treats any warnings (e.g. that a certain package was
built under a different version of R) as errors. If you see such an
error, run the following line and try again:

``` r
Sys.setenv(R_REMOTES_NO_ERRORS_FROM_WARNINGS = "true")
```

The package is loaded, as usual, via

``` r
library(kpitools)
```

The main function is the `kpi` function. A dataframe is passed to it
together with the `var`iable that is of interest for the current KPI. A
summary function also needs to be passed which determines how the KPI is
calculated.

``` r
data(mtcars)

mtcars$highmpg <- mtcars$mpg > 20
```

``` r
kpis <- (mtcars %>%
  kpi(var = "highmpg",                          # variable to be summarized (focus of the KPI)  
      kpi_fn = kpi_fn_perc,                     # summary function   
      txt = "Percentage MPG > 20",              # (optional) nicer text to add to tables 
      by = "cyl",                               # (optional) stratifying variable 
      breakpoints = c(0,33.3,66.6,100),         # (optional) cutoff points 
      risklabels = c("Low", "Medium", "High"))) # (optional) labels for the cutoff points
```

There is a plot method for the output from `kpi` which returns a list of
`ggplot2` objects.

``` r
plot <- plot(kpis)
plot$cyl +
  theme_kpitools()
```

![](README_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

For further details, see the vignette:

``` r
vignette("kpitools")
```

### Acknowledgements

The package logo was created with
[`ggplot2`](https://ggplot2.tidyverse.org/) and
[`hexSticker`](https://github.com/GuangchuangYu/hexSticker).
