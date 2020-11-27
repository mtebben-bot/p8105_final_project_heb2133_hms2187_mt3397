Nordic Exceptionalism
================

Change output later

Filtering out Nordic countries:

The Nordic Region includes five countries: Denmark, Norway, Sweden,
Finland, and Iceland.

``` r
nordic_df = 
  read_csv("./data/merged_df.csv") %>% 
  filter(countries %in% c("Denmark", "Norway", "Sweden", 
           "Finland", "Iceland")) %>% 
  select(-X1)
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

Getting Nordic and global averages for table:

``` r
merged_df = read_csv("./data/merged_df.csv") %>% 
  select(-X1) %>% 
  drop_na()
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
nordic_averages = summarise_all(nordic_df, mean)
```

    ## Warning in mean.default(countries): argument is not numeric or logical:
    ## returning NA

``` r
global_averages = summarise_all(merged_df, mean)
```

    ## Warning in mean.default(countries): argument is not numeric or logical:
    ## returning NA

``` r
nordic_averages[is.na(nordic_averages)] = 0
global_averages[is.na(global_averages)] = 1
                          
nordic_data = 
  rbind(nordic_df, nordic_averages, global_averages) %>% 
  mutate(
    countries = as.character(countries),
    countries = 
      str_replace(countries, "0", "nordic_average"),
    countries = 
      str_replace(countries, "1", "global_average")
    )

nordic_data %>% 
  select(countries, ladder_score, human_freedom_score, personal_freedom_score, economic_freedom_score) %>% 
  knitr::kable()
```

| countries       | ladder\_score | human\_freedom\_score | personal\_freedom\_score | economic\_freedom\_score |
| :-------------- | ------------: | --------------------: | -----------------------: | -----------------------: |
| Denmark         |      7.645600 |              8.560000 |                 9.238444 |                 7.890000 |
| Finland         |      7.808700 |              8.530000 |                 9.268558 |                 7.800000 |
| Iceland         |      7.504500 |              8.410000 |                 9.084634 |                 7.740000 |
| Norway          |      7.488000 |              8.440000 |                 9.259625 |                 7.620000 |
| Sweden          |      7.353500 |              8.500000 |                 9.445978 |                 7.560000 |
| nordic\_average |      7.560060 |              8.488000 |                 9.259448 |                 7.722000 |
| global\_average |      5.645235 |              7.025385 |                 7.148401 |                 6.902539 |

``` r
nordic_data %>% 
  select(countries, ladder_score, human_freedom_score, religion, association, assembly, security_safety, rule_of_law, homicide, size_of_government) %>% 
  knitr::kable()
```

| countries       | ladder\_score | human\_freedom\_score | religion | association |  assembly | security\_safety | rule\_of\_law | homicide | size\_of\_government |
| :-------------- | ------------: | --------------------: | -------: | ----------: | --------: | ---------------: | ------------: | -------: | -------------------: |
| Denmark         |      7.645600 |              8.560000 | 7.702222 |   10.000000 | 10.000000 |         9.840000 |      8.687101 | 9.520000 |             4.707251 |
| Finland         |      7.808700 |              8.530000 | 7.901482 |   10.000000 | 10.000000 |         9.807725 |      8.639248 | 9.520000 |             4.711377 |
| Iceland         |      7.504500 |              8.410000 | 8.122222 |   10.000000 | 10.000000 |         9.880000 |      7.757267 | 9.640000 |             5.811087 |
| Norway          |      7.488000 |              8.440000 | 7.407407 |   10.000000 | 10.000000 |         9.933333 |      8.556488 | 9.800000 |             4.927812 |
| Sweden          |      7.353500 |              8.500000 | 8.884815 |   10.000000 | 10.000000 |         9.799864 |      8.539644 | 9.560000 |             4.542765 |
| nordic\_average |      7.560060 |              8.488000 | 8.003630 |   10.000000 | 10.000000 |         9.852184 |      8.435950 | 9.608000 |             4.940059 |
| global\_average |      5.645235 |              7.025385 | 7.407544 |    7.826923 |  7.365385 |         8.308444 |      5.257269 | 7.875694 |             6.505731 |
