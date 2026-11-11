# Release: return current dataframe without changed old rows

This function matches two dataframe objects by their unique identifier
(usually "time" or "datetime in a timeseries), and returns a new
dataframe which contains the new rows (if present) but matched rows
which contain changes from previous data will be dropped.

## Usage

``` r
release(df_current, df_previous, datetime_variable, include_new = TRUE, ...)
```

## Arguments

- df_current:

  data.frame, the newest/current version of dataset x.

- df_previous:

  data.frame, the old version of dataset, for example x - t1.

- datetime_variable:

  string, which variable to use as unique ID to join `df_current` and
  `df_previous`. Usually a "datetime" variable.

- include_new:

  boolean, should new rows be included? Default is TRUE.

- ...:

  Other
  [`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html)
  arguments can be supplied here, such as `tolerance` or `max_diffs`.
  See `?waldo::compare()` for a full list.

## Value

A dataframe which contains only rows of `df_current` that have not
changed from `df_previous`, and includes new rows. Also returns a waldo
object as in
[`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md).

## See also

[`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md)

[`create_object_list()`](https://docs.ropensci.org/butterfly/reference/create_object_list.md)

## Examples

``` r
# Dropping matched rows which contain changes, and returning unchanged rows
df_released <- butterfly::release(
  butterflycount$march, # New or current dataset
  butterflycount$february, # Previous version you are comparing it to
  datetime_variable = "time", # Unique ID variable they have in common
  include_new = TRUE # Whether to include new rows or not, default is TRUE
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
#> ℹ These will be dropped, but new rows are included.

df_released
#>         time count
#> 1 2024-03-01    23
#> 2 2024-02-01    17
#> 3 2024-01-01    22
#> 4 2023-12-01    55
```
