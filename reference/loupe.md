# Loupe: compare new and old data in continuously updated timeseries

A loupe is a simple, small magnification device used to examine small
details more closely.

## Usage

``` r
loupe(df_current, df_previous, datetime_variable, ...)
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

A boolean where TRUE indicates no changes to previous data and FALSE
indicates unexpected changes.

## Details

This function is intended to aid in the verification of continually
updating timeseries data where we expect new values but want to ensure
previous values remains unchanged.

This function matches two dataframe objects by their unique identifier
(usually "time" or "datetime in a timeseries).

It informs the user of new (unmatched) rows which have appeared, and
then returns a
[`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html)
call to give a detailed breakdown of changes. If you are not familiar
with
[`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html),
this is an expanded and more verbose function similar to base R's
[`all.equal()`](https://rdrr.io/r/base/all.equal.html).

`loupe()` will then return TRUE if there are not changes to previous
data, or FALSE if there are unexpected changes. If you want to extract
changes as a dataframe, use
[`catch()`](https://docs.ropensci.org/butterfly/reference/catch.md), or
if you want to drop them, use
[`release()`](https://docs.ropensci.org/butterfly/reference/release.md).

The main assumption is that `df_current` and `df_previous` are a newer
and older versions of the same data, and that the `datetime_variable`
variable name always remains the same. Elsewhere new columns can of
appear, and these will be returned in the report.

The underlying functionality is handled by
[`create_object_list()`](https://docs.ropensci.org/butterfly/reference/create_object_list.md).

## See also

[`create_object_list()`](https://docs.ropensci.org/butterfly/reference/create_object_list.md)

## Examples

``` r
# Checking two dataframes for changes
# Returning TRUE (no changes) or FALSE (changes)
# This example contains no differences with previous data
butterfly::loupe(
  butterflycount$february, # New or current dataset
  butterflycount$january, # Previous version you are comparing it to
  datetime_variable = "time" # Unique ID variable they have in common
)
#> The following rows are new in 'df_current': 
#>         time count
#> 1 2024-02-01    17
#> ✔ And there are no differences with previous data.
#> [1] TRUE

# This example does contain differences with previous data
butterfly::loupe(
  butterflycount$march,
  butterflycount$february,
  datetime_variable = "time"
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
#> [1] FALSE
```
