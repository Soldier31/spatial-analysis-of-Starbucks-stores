Tidy
====

First, let's remove the `-` in the `Store Number` column to simplify the format:

``` r
sbux_stores[["Store Number"]] <- str_replace(sbux_stores[["Store Number"]], "[-]", "")
```

There are several ways we can correct the column types to their appropriate classes. The columns can be:

1.  coerced into the correct type using the `as.double()` function or
2.  we can read our file in again, except this time we specify the `col_type()` argument.

Normally, I would choose the latter option and let the parsing heuristics handle the chore of correctly parsing our columns. But we have differently formatted phone numbers from all regions of the world and the parsing function guesses based on the first 1000 rows of our dataset. Instead of relying on the parsing function to correctly guess the column type, I will manually convert the column using the `as.double()` function. Since `Timezone` is a categorical variable, we will use the `as.factor()` function to convert it to a `factor`:

``` r
sbux_stores[["Store Number"]] <- as.double(sbux_stores[["Store Number"]])
sbux_stores[["Phone Number"]] <- as.double(sbux_stores[["Phone Number"]])
sbux_stores[["Timezone"]] <- as.factor(sbux_stores[["Timezone"]])
```

Next, we need to deal with the missing data. Generally, we can either remove these observations, impute based on a median value or predict its value using more advanced modelling techniques (mice, rpart, kNN Imputation). But the nature of our data does not lend itself to these predictive techniques because it is impossible to predict a postal code in a location that it does not even exist in. Instead of completely removing these observations we will exclude these missing values by setting the `na.action` argument.

We will rename the variable names for greater clarity:

``` r
sbux_stores <- plyr::rename(sbux_stores, c("Brand" = "brand", "Store Number" = "id", "Store Name" = "name", "Ownership Type" = "store_type", "Street Address" = "address", "City" = "city", "State/Province" = "state", "Country" = "region", "Postcode" = "postalcode", "Phone Number" = "phone", "Timezone" = "tz", "Longitude" = "lon", "Latitude" = "lat"))
```

Finally, let's take one last look at our dataset to confirm that it has been tidied:

``` r
glimpse(sbux_stores)
```

    ## Observations: 25,600
    ## Variables: 13
    ## $ brand      <chr> "Starbucks", "Starbucks", "Starbucks", "Starbucks",...
    ## $ id         <dbl> 47370257954, 22331212325, 47089256771, 22126218024,...
    ## $ name       <chr> "Meritxell, 96", "Ajman Drive Thru", "Dana Mall", "...
    ## $ store_type <chr> "Licensed", "Licensed", "Licensed", "Licensed", "Li...
    ## $ address    <chr> "Av. Meritxell, 96", "1 Street 69, Al Jarf", "Sheik...
    ## $ city       <chr> "Andorra la Vella", "Ajman", "Ajman", "Abu Dhabi", ...
    ## $ state      <chr> "7", "AJ", "AJ", "AZ", "AZ", "AZ", "AZ", "AZ", "AZ"...
    ## $ region     <chr> "AD", "AE", "AE", "AE", "AE", "AE", "AE", "AE", "AE...
    ## $ postalcode <chr> "AD500", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, "3...
    ## $ phone      <dbl> 376818720, NA, NA, NA, NA, NA, NA, NA, 26670052, NA...
    ## $ tz         <fctr> GMT+1:00 Europe/Andorra, GMT+04:00 Asia/Dubai, GMT...
    ## $ lon        <dbl> 1.53, 55.47, 55.47, 54.38, 54.54, 54.49, 54.49, 54....
    ## $ lat        <dbl> 42.51, 25.42, 25.39, 24.48, 24.51, 24.40, 24.40, 24...

