Import
======

We will be mainly using the core packages from the `tidyverse` for our analysis.

``` r
library(tidyverse)
library(stringr)
library(forcats)
library(ggmap)
library(maps)
library(mapdata)
```

Loading data
------------

The [dataset](https://www.kaggle.com/starbucks/store-locations) is publicly available on kaggle and was scraped from the Starbucks store locator webpage by Github user [chrismeller](https://github.com/chrismeller/).

``` r
sbux_stores <- read_csv("directory.csv")
```

Data structure and content
--------------------------

Let's take a look at the structure and content of our dataset:

``` r
glimpse(sbux_stores)
```

    ## Observations: 25,600
    ## Variables: 13
    ## $ Brand          <chr> "Starbucks", "Starbucks", "Starbucks", "Starbuc...
    ## $ Store Number   <chr> "47370-257954", "22331-212325", "47089-256771",...
    ## $ Store Name     <chr> "Meritxell, 96", "Ajman Drive Thru", "Dana Mall...
    ## $ Ownership Type <chr> "Licensed", "Licensed", "Licensed", "Licensed",...
    ## $ Street Address <chr> "Av. Meritxell, 96", "1 Street 69, Al Jarf", "S...
    ## $ City           <chr> "Andorra la Vella", "Ajman", "Ajman", "Abu Dhab...
    ## $ State/Province <chr> "7", "AJ", "AJ", "AZ", "AZ", "AZ", "AZ", "AZ", ...
    ## $ Country        <chr> "AD", "AE", "AE", "AE", "AE", "AE", "AE", "AE",...
    ## $ Postcode       <chr> "AD500", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA...
    ## $ Phone Number   <chr> "376818720", NA, NA, NA, NA, NA, NA, NA, "26670...
    ## $ Timezone       <chr> "GMT+1:00 Europe/Andorra", "GMT+04:00 Asia/Duba...
    ## $ Longitude      <dbl> 1.53, 55.47, 55.47, 54.38, 54.54, 54.49, 54.49,...
    ## $ Latitude       <dbl> 42.51, 25.42, 25.39, 24.48, 24.51, 24.40, 24.40...

The first four variables contain descriptive information about the store while the other 9 variables describe the physical location of the stores. Notice that our dataset has a few problems:

-   improperly classified column types (`Store Number`, `Phone Number`)
-   missing data in `Postcode` and `Phone Number` columns

``` r
sum(is.na(sbux_stores))
```

    ## [1] 8402

There are 8402 missing observations in our dataset, with the majority of them coming from the `Postcode` and `Phone Number` columns. Some possible explanations for the missing data may be:

1.  not all locations have a postal code, such as city-states in the United Arab Emirates, and rely on alternative alphanumeric systems for sorting mail such as Post Office Box numbers

2.  some locations might not have a committed telephone system in place because the location is unable to support it or it may be impractical (for example, if it was a small Starbucks located inside an airport it may not have a dedicated phone system as some of the larger stores do)

It is also debatable whether `Timezone` should be a `date` object or not. However, we do not have the other date components for a `date` object (`year` `month` and `day`) so we will instead convert it to a `factor` since it provides categorical data now.
