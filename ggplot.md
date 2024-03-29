ggplot 1
================
9/26/2019

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.2.1     ✔ purrr   0.3.2
    ## ✔ tibble  2.1.3     ✔ dplyr   0.8.3
    ## ✔ tidyr   1.0.0     ✔ stringr 1.4.0
    ## ✔ readr   1.3.1     ✔ forcats 0.4.0

    ## ── Conflicts ──────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(ggridges)
```

    ## 
    ## Attaching package: 'ggridges'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     scale_discrete_manual

## create the weatger data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/Kelly/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-09-26 10:26:04

    ## file min/max dates: 1869-01-01 / 2019-09-30

    ## file path:          /Users/Kelly/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-09-26 10:26:23

    ## file min/max dates: 1965-01-01 / 2019-09-30

    ## file path:          /Users/Kelly/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-09-26 10:26:28

    ## file min/max dates: 1999-09-01 / 2019-09-30

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 1,085 more rows

## create a ggplot

``` r
ggplot(weather_df, aes(x=tmin, y=tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
##aes is for aesthetics
##geom_point() adds a corresponding point to each thing
```

alternate way of making this plot

``` r
weather_df %>%
  ggplot(aes(x=tmin, y=tmax))+geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-3-1.png)<!-- --> saving
initial points. Most of the time i do not do this

``` r
scatterplot=
  weather_df %>%
  ggplot(aes(x=tmin, y=tmax)) +
  geom_point()

#doesn't print out results. just saves things

scatterplot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
#this prints it
```

time to add some color …

``` r
weather_df %>%
  ggplot(aes(x=tmin, y=tmax)) +
  geom_point(aes(color=name), alpha=0.4)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
#alpha level determines transparency. closer to 0, mostly transparent
```

difference between aes in the geom\_point vs in the ggplot. "why do
‘aes’ positions matter?

color matters in geom\_point but not in geom smooth

``` r
weather_df %>%
  ggplot(aes(x=tmin, y=tmax)) +
  geom_point(aes(color=name), alpha=0.4) +
  geom_smooth(se=FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
#se=false gets rid of the error bars
```

if put it in ggplot, we see that it goes through all of the points
(individual)

``` r
weather_df %>%
  ggplot(aes(x=tmin, y=tmax, color=name)) +
  geom_point(aes(color=name), alpha=0.4) +
  geom_smooth(se=FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
#se=false gets rid of the error bars
```

``` r
weather_df %>%
  ggplot(aes(x=tmin, y=tmax, color=name)) +
  geom_point(aes(color=name), alpha=0.4) +
  geom_smooth(se=FALSE) +
  facet_grid(~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
#se=false gets rid of the error bars
#facet panels each group
```

more fun plots\!

``` r
#seasonal trend in each of these locations
weather_df %>%
  ggplot(aes(x=date, y=prcp, color=name)) +
  geom_point() +
  geom_smooth(se=FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
#geom_point is the signifier for scatterplots
#can change the y
```

``` r
#seasonal trend in each of these locations
weather_df %>%
  ggplot(aes(x=date, y=tmax, color=name)) +
  geom_point(aes(size=prcp), alpha=0.5) +
  geom_smooth(size=2, se=FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](ggplot_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
#geom_point is the signifier for scatterplots
#prcp is changed too 
```
