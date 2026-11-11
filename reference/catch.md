# Catch: return dataframe containing only rows that have changed

This function matches two dataframe objects by their unique identifier
(usually "time" or "datetime in a timeseries), and returns a new
dataframe which contains only rows that have changed compared to
previous data. It will not return any new rows.

## Usage

``` r
catch(df_current, df_previous, datetime_variable, ...)
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

A dataframe which contains only rows of `df_current` that have changes
from `df_previous`, but without new rows. Also returns a waldo object as
in [`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md).

## Details

The underlying functionality is handled by
[`create_object_list()`](https://docs.ropensci.org/butterfly/reference/create_object_list.md).

## See also

[`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md)

[`create_object_list()`](https://docs.ropensci.org/butterfly/reference/create_object_list.md)

## Examples

``` r
# Returning only matched rows which contain changes
df_caught <- butterfly::catch(
  butterflycount$march, # New or current dataset
  butterflycount$february, # Previous version you are comparing it to
  datetime_variable = "time" # Unique ID variable they have in common
)
#> The following rows are new in 'df_current': 
#>         time count
#> 1 2024-03-01    23
#> 
#> ℹ The following values have changes from the previous data.
#> old vs new
#>            count
#>   old[1, ]    17
#>   old[2, ]    22
#>   old[3, ]    55
#> - old[4, ]    18
#> + new[4, ]    11
#> 
#> `old$count`: 17.0 22.0 55.0 18.0
#> `new$count`: 17.0 22.0 55.0 11.0
#> 
#> ℹ Only these rows are returned.

df_caught
#>         time count
#> 1 2023-11-01    18
```
