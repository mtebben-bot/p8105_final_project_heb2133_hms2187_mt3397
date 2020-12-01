Nordic Exceptionalism
================

The Nordic Region includes five countries: Denmark, Norway, Sweden,
Finland, and Iceland. Tables comparing the Nordic country, as well as
the averages from the Nordic region and the rest of the world. Note that
the human freedom score is a composite score of the personal and
economic freedom scores.

``` r
nordic_only = 
  read_csv("./data/merged_df.csv") %>% 
  filter(countries %in% c("Denmark", "Norway", "Sweden", 
           "Finland", "Iceland")) %>% 
  select(-X1) %>% 
  rename("overall_happiness_score" = "ladder_score")
```

    ## Warning: Missing column names filled in: 'X1' [1]

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_double(),
    ##   countries = col_character(),
    ##   ladder_score = col_double(),
    ##   human_freedom_score = col_double(),
    ##   personal_freedom_score = col_double(),
    ##   economic_freedom_score = col_double(),
    ##   religion = col_double(),
    ##   association = col_double(),
    ##   assembly = col_double(),
    ##   movement = col_double(),
    ##   inheritance_rights = col_double(),
    ##   security_safety = col_double(),
    ##   disapperances_conflicts_and_terrorism = col_double(),
    ##   rule_of_law = col_double(),
    ##   homicide = col_double(),
    ##   size_of_government = col_double()
    ## )

``` r
global_other = read_csv("./data/merged_df.csv") %>% 
  select(-X1) %>% 
  mutate(
    countries = str_remove(
      countries, "Denmark|Norway|Sweden|Finland|Icedland")
  ) %>% 
  rename("overall_happiness_score" = "ladder_score")
```

    ## Warning: Missing column names filled in: 'X1' [1]

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_double(),
    ##   countries = col_character(),
    ##   ladder_score = col_double(),
    ##   human_freedom_score = col_double(),
    ##   personal_freedom_score = col_double(),
    ##   economic_freedom_score = col_double(),
    ##   religion = col_double(),
    ##   association = col_double(),
    ##   assembly = col_double(),
    ##   movement = col_double(),
    ##   inheritance_rights = col_double(),
    ##   security_safety = col_double(),
    ##   disapperances_conflicts_and_terrorism = col_double(),
    ##   rule_of_law = col_double(),
    ##   homicide = col_double(),
    ##   size_of_government = col_double()
    ## )

``` r
#Getting averages across dataframes
nordic_averages = summarise_all(nordic_only, mean)
```

    ## Warning in mean.default(countries): argument is not numeric or logical:
    ## returning NA

``` r
global_averages = summarise_all(global_other, mean, na.rm = TRUE)
```

    ## Warning in mean.default(countries, na.rm = TRUE): argument is not numeric or
    ## logical: returning NA

``` r
#Removing "NAs from last step
nordic_averages[is.na(nordic_averages)] = 0
global_averages[is.na(global_averages)] = 1

#Binding dataframes                          
nordic_data = 
  rbind(nordic_only, nordic_averages, global_averages) %>% 
  mutate(
    countries = as.character(countries),
    countries = 
      str_replace(countries, "0", "nordic_average"),
    countries = 
      str_replace(countries, "1", "other_global_average")
    )
```

``` r
nordic_data %>% 
  select(countries, overall_happiness_score, human_freedom_score, personal_freedom_score, economic_freedom_score) %>% 
  knitr::kable()
```

| countries              | overall\_happiness\_score | human\_freedom\_score | personal\_freedom\_score | economic\_freedom\_score |
| :--------------------- | ------------------------: | --------------------: | -----------------------: | -----------------------: |
| Denmark                |                   7.64560 |              8.560000 |                 9.238444 |                 7.890000 |
| Finland                |                   7.80870 |              8.530000 |                 9.268558 |                 7.800000 |
| Iceland                |                   7.50450 |              8.410000 |                 9.084634 |                 7.740000 |
| Norway                 |                   7.48800 |              8.440000 |                 9.259625 |                 7.620000 |
| Sweden                 |                   7.35350 |              8.500000 |                 9.445978 |                 7.560000 |
| nordic\_average        |                   7.56006 |              8.488000 |                 9.259448 |                 7.722000 |
| other\_global\_average |                   5.47324 |              6.887963 |                 6.976827 |                 6.799074 |

``` r
nordic_data %>% 
  select(countries, overall_happiness_score, human_freedom_score, religion, association, assembly, security_safety, rule_of_law, homicide, size_of_government) %>%
  knitr::kable()
```

| countries              | overall\_happiness\_score | human\_freedom\_score | religion | association |  assembly | security\_safety | rule\_of\_law | homicide | size\_of\_government |
| :--------------------- | ------------------------: | --------------------: | -------: | ----------: | --------: | ---------------: | ------------: | -------: | -------------------: |
| Denmark                |                   7.64560 |              8.560000 | 7.702222 |   10.000000 | 10.000000 |         9.840000 |      8.687101 | 9.520000 |             4.707251 |
| Finland                |                   7.80870 |              8.530000 | 7.901482 |   10.000000 | 10.000000 |         9.807725 |      8.639248 | 9.520000 |             4.711377 |
| Iceland                |                   7.50450 |              8.410000 | 8.122222 |   10.000000 | 10.000000 |         9.880000 |      7.757267 | 9.640000 |             5.811087 |
| Norway                 |                   7.48800 |              8.440000 | 7.407407 |   10.000000 | 10.000000 |         9.933333 |      8.556488 | 9.800000 |             4.927812 |
| Sweden                 |                   7.35350 |              8.500000 | 8.884815 |   10.000000 | 10.000000 |         9.799864 |      8.539644 | 9.560000 |             4.542765 |
| nordic\_average        |                   7.56006 |              8.488000 | 8.003630 |   10.000000 | 10.000000 |         9.852184 |      8.435950 | 9.608000 |             4.940059 |
| other\_global\_average |                   5.47324 |              6.887963 | 7.476429 |    7.536765 |  7.113971 |         8.159625 |      5.108327 | 7.638562 |             6.450821 |

Two sample t-test comparing Nordic happiness and human freedom score
versus the rest of the world.

``` r
#Reformatting data for tests 

merged_setup = read_csv("./data/merged_df.csv") %>% 
  select(-X1) %>% 
  rename("overall_happiness_score" = "ladder_score") %>%
  select(countries, overall_happiness_score,
         human_freedom_score) %>% 
  mutate(
    countries = case_when(
      countries == "Denmark"     ~ "nordic",
      countries == "Norway"      ~ "nordic",
      countries == "Sweden"      ~ "nordic",
      countries == "Finland"     ~ "nordic",
      countries == "Iceland"     ~ "nordic"
    )) %>% 
  mutate(countries = as.character(countries))
```

    ## Warning: Missing column names filled in: 'X1' [1]

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_double(),
    ##   countries = col_character(),
    ##   ladder_score = col_double(),
    ##   human_freedom_score = col_double(),
    ##   personal_freedom_score = col_double(),
    ##   economic_freedom_score = col_double(),
    ##   religion = col_double(),
    ##   association = col_double(),
    ##   assembly = col_double(),
    ##   movement = col_double(),
    ##   inheritance_rights = col_double(),
    ##   security_safety = col_double(),
    ##   disapperances_conflicts_and_terrorism = col_double(),
    ##   rule_of_law = col_double(),
    ##   homicide = col_double(),
    ##   size_of_government = col_double()
    ## )

``` r
merged_setup$countries[is.na(merged_setup$countries)] = "other"

#Group means, counts, and SD for happiness score
group_by(merged_setup, countries) %>%
  summarise(
    count = n(),
    mean = mean(overall_happiness_score, na.rm = TRUE),
    sd = sd(overall_happiness_score, na.rm = TRUE)
  )
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 2 x 4
    ##   countries count  mean    sd
    ##   <chr>     <int> <dbl> <dbl>
    ## 1 nordic        5  7.56 0.173
    ## 2 other       167  5.40 1.06

``` r
#Group means, counts, and SD for happiness score
group_by(merged_setup, countries) %>%
  summarise(
    count = n(),
    mean = mean(human_freedom_score, na.rm = TRUE),
    sd = sd(human_freedom_score, na.rm = TRUE)
  )
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 2 x 4
    ##   countries count  mean     sd
    ##   <chr>     <int> <dbl>  <dbl>
    ## 1 nordic        5  8.49 0.0622
    ## 2 other       167  6.84 1.08

t-test for overall happiness nordic vs other. Assume unequal variance
due to difference in sample size.

``` r
t.test(overall_happiness_score ~ countries, 
              data = merged_setup, var.equal = FALSE) %>% 
  broom::tidy() %>% 
  select(estimate:estimate2, statistic, p.value) %>% 
  knitr::kable()
```

| estimate | estimate1 | estimate2 | statistic | p.value |
| -------: | --------: | --------: | --------: | ------: |
| 2.157321 |   7.56006 |  5.402739 |  18.49376 |       0 |

t-test for human freedom score nordic vs other. Assume unequal variance
due to difference in sample size.

``` r
t.test(human_freedom_score ~ countries, 
              data = merged_setup, var.equal = FALSE) %>% 
  broom::tidy() %>% 
  select(estimate:estimate2, statistic, p.value) %>% 
  knitr::kable()
```

| estimate | estimate1 | estimate2 | statistic | p.value |
| -------: | --------: | --------: | --------: | ------: |
| 1.650994 |     8.488 |  6.837006 |  18.17931 |       0 |

To-do: potentially scatter plots with sub freedoms nordic countries
