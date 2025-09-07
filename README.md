<!-- README.md is generated from README.Rmd. Please edit that file -->
# datacume
<!-- badges: start -->
<!-- badges: end -->

The goal of the datacume package is to compute cumulative summaries of grouped data.

□ Code explained: https://agronomy4future.com/archives/24603

## Installation

You can install datacume() like so:

Before installing, please download Rtools (https://cran.r-project.org/bin/windows/Rtools)

``` r
if(!require(remotes)) install.packages("remotes")
if (!requireNamespace("datacume", quietly = TRUE)) {
  remotes::install_github("agronomy4future/datacume", force= TRUE)
}
library(remotes)
library(datacume)
```

## Example

This is a basic code for datacume()

``` r
datacume(data= data name,
              group_vars= c("Group1", "Group2", "Group3"),
              time_var= Day,
              response_vars= c("numeric value"))
```

## Let’s practice with actual dataset

``` r
# data upload 
if(!require(readr)) install.packages("readr")
library(readr)

github="https://raw.githubusercontent.com/agronomy4future/raw_data_practice/refs/heads/main/biomass_cumulative.csv"
df=data.frame(read_csv(url(github),show_col_types = FALSE))
set.seed(100)

print(df[sample(nrow(df),5),])
    Treatment Block Plant Branch Days Biomass
503   Control   III     5      1   66  360.73
358   Control     I     5      4   59  345.46
470   Control     I     8      3   66  403.13
516   Control     I     1      4   73  565.40
98    Drought   III     3      2   59  162.15
.
.
.

# datacume() 
if(!require(dplyr)) install.packages("dplyr")
library(dplyr)

dataA= datacume(
              data= df,
              group_vars= c("Treatment", "Block", "Plant"),
              time_var= Days,
              response_vars= c("Biomass"))

print(dataA[sample(nrow(dataA),5),])           
  Treatment Block Plant  Days Biomass Cumulative_Biomass
1 Drought   II        2    73    184.               648.
2 Control   I         7    66    330.               885.
3 Control   I         3    73    359.              1474.
4 Control   IV        3    73    390.               390.
5 Drought   II        6    66    126.               269.

# Average dataset
summary=data.frame(dataA %>%
                      group_by(Treatment, Days) %>%
                      dplyr::summarize(across(c(Cumulative_Biomass),
                                                    .fns= list(Mean=~mean(., na.rm= TRUE),
                                                               SD= ~sd(., na.rm= TRUE),
                                                               n=~length(.),
                                                               se=~sd(.,na.rm= TRUE) / sqrt(length(.))))))%>%
  ungroup()

print(summary)
   Treatment Days Cumulative_Biomass_Mean Cumulative_Biomass_SD
1    Control   45                206.1908             146.19012
2    Control   52                563.6869             156.95986
3    Control   59                723.4944             231.92993
4    Control   66               1106.6503             263.58099
5    Control   73               1445.3170             331.11848
6    Drought   45                124.5334              83.77338
7    Drought   52                239.8938              87.31755
8    Drought   59                341.1837             149.34782
9    Drought   66                481.5725             165.35873
10   Drought   73                641.8063             168.45339
   Cumulative_Biomass_n Cumulative_Biomass_se
1                    28              27.62734
2                     9              52.31995
3                    29              43.06831
4                    26              51.69249
5                    29              61.48716
6                    24              17.10017
7                    13              24.21753
8                    26              29.28952
9                    22              35.25460
10                   27              32.41887
