# create_object_list: creates a list of objects used in all butterfly functions

This function creates a list of objects which is used by all of
[`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md),
[`catch()`](https://docs.ropensci.org/butterfly/reference/catch.md) and
[`release()`](https://docs.ropensci.org/butterfly/reference/release.md).

## Usage

``` r
create_object_list(df_current, df_previous, datetime_variable, ...)
```

## Arguments

- df_current:

  data.frame, the newest/current version of dataset x.

- df_previous:

  data.frame, the old version of dataset, for example x - t1.

- datetime_variable:

  string, which variable to use as unique ID to join `df_current` and
  `df_previous`. Usually a "datetime" variable.

- ...:

  Other
  [`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html)
  arguments can be supplied here, such as `tolerance` or `max_diffs`.
  See `?waldo::compare()` for a full list.

## Value

A list containing boolean where TRUE indicates no changes to previous
data and FALSE indicates unexpected changes, a dataframe of the current
data without new rows and a dataframe of new rows only

## Details

This function matches two dataframe objects by their unique identifier
(usually "time" or "datetime in a timeseries).

It informs the user of new (unmatched) rows which have appeared, and
then returns a
[`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html)
call to give a detailed breakdown of changes.

The main assumption is that `df_current` and `df_previous` are a newer
and older versions of the same data, and that the `datetime_variable`
variable name always remains the same. Elsewhere new columns can of
appear, and these will be returned in the report.

## Examples

``` r
butterfly_object_list <- butterfly::create_object_list(
  butterflycount$february, # New or current dataset
  butterflycount$january, # Previous version you are comparing to
  datetime_variable = "time" # Unique ID variable they have in common
)
#> The following rows are new in 'butterflycount$february': 
#>         time count
#> 1 2024-02-01    17
#> ✔ And there are no differences with previous data.

butterfly_object_list
#> $butterfly_status
#> [1] TRUE
#> 
#> $df_current_without_new_row
#>         time count
#> 1 2024-01-01    22
#> 2 2023-12-01    55
#> 3 2023-11-01    11
#> 
#> $df_current_new_rows
#>         time count
#> 1 2024-02-01    17
#> 

# You can pass other `waldo::compare()` options such as tolerance here
butterfly_object_list <- butterfly::create_object_list(
  butterflycount$march, # New or current dataset
  butterflycount$february, # Previous version you are comparing it to
  datetime_variable = "time", # Unique ID variable they have in common
  tolerance = 2
)
#> The following rows are new in 'butterflycount$march': 
#>         time count
#> 1 2024-03-01    23
#> ✔ And there are no differences with previous data.

butterfly_object_list
#> $butterfly_status
#> [1] TRUE
#> 
#> $df_current_without_new_row
#>         time count
#> 1 2024-02-01    17
#> 2 2024-01-01    22
#> 3 2023-12-01    55
#> 4 2023-11-01    18
#> 
#> $df_current_new_rows
#>         time count
#> 1 2024-03-01    23
#> 
```
