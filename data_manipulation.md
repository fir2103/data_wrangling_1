Data Manipulation with dplyr
================
Farizah Rob
2022-09-22

Once you’ve imported data, we’re going to need to do some cleaning up.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
options(tibble.print_min = 3)

litters_data = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data = janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii")
pups_data = janitor::clean_names(pups_data)
```

### `select`

You can specify the columns you want to keep by naming all of them.

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive) 
```

    ## # A tibble: 49 × 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <int>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # … with 46 more rows

You can also specify column range

``` r
select(litters_data, group:gd_of_birth) 
```

    ## # A tibble: 49 × 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <int>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # … with 46 more rows

You can also specify columns you want removed

``` r
select(litters_data, -pups_survive) 
```

    ## # A tibble: 49 × 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_…¹
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20               3       4
    ## 2 Con7  #1/2/95/2           27          42            19               8       0
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6       0
    ## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth

``` r
select(litters_data, -c(pups_survive, group)) #removing multiple columns together 
```

    ## # A tibble: 49 × 6
    ##   litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_dead_b…¹
    ##   <chr>              <dbl>       <dbl>       <int>           <int>         <int>
    ## 1 #85                 19.7        34.7          20               3             4
    ## 2 #1/2/95/2           27          42            19               8             0
    ## 3 #5/5/3/83/3-3       26          41.4          19               6             0
    ## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth

Rename variables in this process

``` r
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number) #select columns and change names, first new name=old name
```

    ## # A tibble: 49 × 2
    ##   GROUP LiTtEr_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # … with 46 more rows

If all you want to do is rename something, you can use rename instead of
select. This will rename the variables you care about, and keep
everything else:

``` r
rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 8
    ##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

There are some handy helper functions for select; read about all of them
using ?select_helpers. I use starts_with(), ends_with(), and contains()
often, especially when there variables are named with suffixes or other
standard patterns:

``` r
select(litters_data, starts_with("gd"))
```

    ## # A tibble: 49 × 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <int>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # … with 46 more rows

``` r
select(litters_data, ends_with("weight"))
```

    ## # A tibble: 49 × 2
    ##   gd0_weight gd18_weight
    ##        <dbl>       <dbl>
    ## 1       19.7        34.7
    ## 2       27          42  
    ## 3       26          41.4
    ## # … with 46 more rows

I also frequently use is everything(), which is handy for reorganizing
columns without discarding anything:

``` r
select(litters_data, litter_number, pups_survive, everything())
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

`relocate` does a similar thing (and is sort of like rename in that it’s
handy but not critical):

``` r
relocate(litters_data, litter_number, pups_survive)
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

Lastly, like other functions in dplyr, select will export a dataframe
even if you only select one column. Mostly this is fine, but sometimes
you want the vector stored in the column. To pull a single variable, use
`pull`.

Learning Assessment

``` r
select(pups_data, litter_number, sex, pd_ears)
```

    ## # A tibble: 313 × 3
    ##   litter_number   sex pd_ears
    ##   <chr>         <int>   <int>
    ## 1 #85               1       4
    ## 2 #85               1       4
    ## 3 #1/2/95/2         1       5
    ## # … with 310 more rows

### `filter`

Filter rows according to specific conditions

Some ways you might filter the litters data are:

-   `gd_of_birth == 20`
-   `pups_born_alive >= 2`
-   `pups_survive != 4`
-   `!(pups_survive == 4)`
-   `group %in% c("Con7", "Con8")`
-   `group == "Con7" & gd_of_birth == 20`

Example

``` r
filter(litters_data, gd_of_birth==20)
```

    ## # A tibble: 32 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #4/2/95/3-3         NA          NA            20       6       0       6
    ## 3 Con7  #2/2/95/3-2         NA          NA            20       6       0       4
    ## # … with 29 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

``` r
filter(litters_data, group %in% c("Con7", "Con8"))
```

    ## # A tibble: 15 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>                <dbl>       <dbl>    <int>   <int>   <int>   <int>
    ##  1 Con7  #85                   19.7        34.7       20       3       4       3
    ##  2 Con7  #1/2/95/2             27          42         19       8       0       7
    ##  3 Con7  #5/5/3/83/3-3         26          41.4       19       6       0       5
    ##  4 Con7  #5/4/2/95/2           28.5        44.1       19       5       1       4
    ##  5 Con7  #4/2/95/3-3           NA          NA         20       6       0       6
    ##  6 Con7  #2/2/95/3-2           NA          NA         20       6       0       4
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA         20       9       0       9
    ##  8 Con8  #3/83/3-3             NA          NA         20       9       1       8
    ##  9 Con8  #2/95/3               NA          NA         20       8       0       8
    ## 10 Con8  #3/5/2/2/95           28.5        NA         20       8       0       8
    ## 11 Con8  #5/4/3/83/3           28          NA         19       9       0       8
    ## 12 Con8  #1/6/2/2/95-2         NA          NA         20       7       0       6
    ## 13 Con8  #3/5/3/83/3-3-2       NA          NA         20       8       0       8
    ## 14 Con8  #2/2/95/2             NA          NA         19       5       0       4
    ## 15 Con8  #3/6/2/2/95-3         NA          NA         20       7       0       7
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

Getting rid of missing values - use the tidyr package instead of filter

``` r
drop_na(litters_data)
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

``` r
drop_na(litters_data, pups_survive)
```

    ## # A tibble: 49 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

Don’t create too many subsets of the data -\> can clutter up the
workspace

Learning Assessment

``` r
filter(pups_data, sex==1)
```

    ## # A tibble: 155 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #85               1       4      13        7      11
    ## 2 #85               1       4      13        7      12
    ## 3 #1/2/95/2         1       5      13        7       9
    ## # … with 152 more rows

``` r
filter(pups_data, sex==2 & pd_walk<11)
```

    ## # A tibble: 127 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # … with 124 more rows

### `mutate`

``` r
mutate(litters_data, 
       wt_gain = gd18_weight - gd0_weight, 
       group = str_to_lower(group), 
       wt_gain_kg = wt_gain * 2.2
)
```

    ## # A tibble: 49 × 10
    ##   group litter…¹ gd0_w…² gd18_…³ gd_of…⁴ pups_…⁵ pups_…⁶ pups_…⁷ wt_gain wt_ga…⁸
    ##   <chr> <chr>      <dbl>   <dbl>   <int>   <int>   <int>   <int>   <dbl>   <dbl>
    ## 1 con7  #85         19.7    34.7      20       3       4       3    15      33  
    ## 2 con7  #1/2/95…    27      42        19       8       0       7    15      33  
    ## 3 con7  #5/5/3/…    26      41.4      19       6       0       5    15.4    33.9
    ## # … with 46 more rows, and abbreviated variable names ¹​litter_number,
    ## #   ²​gd0_weight, ³​gd18_weight, ⁴​gd_of_birth, ⁵​pups_born_alive,
    ## #   ⁶​pups_dead_birth, ⁷​pups_survive, ⁸​wt_gain_kg

A few things in this example are worth noting:

-   Your new variables can be functions of old variables
-   New variables appear at the end of the dataset in the order that
    they are created
-   You can overwrite old variables
-   You can create a new variable and immediately refer to (or change)
    it

Learning Assessment

``` r
mutate(pups_data, 
       new_var = pd_pivot-7,
       total_pd = pd_ears + pd_eyes + pd_pivot + pd_walk)
```

    ## # A tibble: 313 × 8
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk new_var total_pd
    ##   <chr>         <int>   <int>   <int>    <int>   <int>   <dbl>    <int>
    ## 1 #85               1       4      13        7      11       0       35
    ## 2 #85               1       4      13        7      12       0       36
    ## 3 #1/2/95/2         1       5      13        7       9       0       34
    ## # … with 310 more rows

### `arrange`

``` r
head(arrange(litters_data, group, pups_born_alive), 10)
```

    ## # A tibble: 10 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>                <dbl>       <dbl>    <int>   <int>   <int>   <int>
    ##  1 Con7  #85                   19.7        34.7       20       3       4       3
    ##  2 Con7  #5/4/2/95/2           28.5        44.1       19       5       1       4
    ##  3 Con7  #5/5/3/83/3-3         26          41.4       19       6       0       5
    ##  4 Con7  #4/2/95/3-3           NA          NA         20       6       0       6
    ##  5 Con7  #2/2/95/3-2           NA          NA         20       6       0       4
    ##  6 Con7  #1/2/95/2             27          42         19       8       0       7
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA         20       9       0       9
    ##  8 Con8  #2/2/95/2             NA          NA         19       5       0       4
    ##  9 Con8  #1/6/2/2/95-2         NA          NA         20       7       0       6
    ## 10 Con8  #3/6/2/2/95-3         NA          NA         20       7       0       7
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

### `%>%`

``` r
litters_data_raw = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data_clean_names = janitor::clean_names(litters_data_raw)
litters_data_selected_cols = select(litters_data_clean_names, -pups_survive)
litters_data_with_vars = 
  mutate(
    litters_data_selected_cols, 
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))
litters_data_with_vars_without_missing = 
  drop_na(litters_data_with_vars, wt_gain)
litters_data_with_vars_without_missing
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7        34.7          20       3       4    15  
    ## 2 con7  #1/2/95/2           27          42            19       8       0    15  
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth

Below, second option

``` r
litters_data_clean = 
  drop_na(
    mutate(
      select(
        janitor::clean_names(
          read_csv("./data/FAS_litters.csv", col_types = "ccddiiii")
          ), 
      -pups_survive
      ),
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)
    ),
  wt_gain
  )

litters_data_clean
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7        34.7          20       3       4    15  
    ## 2 con7  #1/2/95/2           27          42            19       8       0    15  
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth

These are both confusing and bad: the first gets confusing and clutters
our workspace, and the second has to be read inside out.

Piping solves this problem. It allows you to turn the nested approach
into a sequential chain by passing the result of one function call as an
argument to the next function call:

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names() %>%
  select(-pups_survive) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) %>% 
  drop_na(wt_gain)

litters_data 
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7        34.7          20       3       4    15  
    ## 2 con7  #1/2/95/2           27          42            19       8       0    15  
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth

Steps: Reading the data, cleaning names, removing pups_survive column,
creating new variable wt_gain and changing group to lowercase, dropping
missing values of wt_gain.

### Shortcut for %\>%: command + shift + m

Fitting a linear model

``` r
litters_data %>%
  lm(wt_gain ~ pups_born_alive, data = .) %>%
  broom::tidy()
```

    ## # A tibble: 2 × 5
    ##   term            estimate std.error statistic  p.value
    ##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)       13.1       1.27      10.3  3.39e-11
    ## 2 pups_born_alive    0.605     0.173      3.49 1.55e- 3

Learning Assessment

``` r
read_csv("./data/FAS_pups.csv", col_types = "ciiiii") %>% 
janitor::clean_names() %>% 
filter(sex==1) %>% 
select(-pd_ears) %>%
mutate(pd_greater_7 = pd_pivot > 7)
```

    ## # A tibble: 155 × 6
    ##   litter_number   sex pd_eyes pd_pivot pd_walk pd_greater_7
    ##   <chr>         <int>   <int>    <int>   <int> <lgl>       
    ## 1 #85               1      13        7      11 FALSE       
    ## 2 #85               1      13        7      12 FALSE       
    ## 3 #1/2/95/2         1      13        7       9 FALSE       
    ## # … with 152 more rows
