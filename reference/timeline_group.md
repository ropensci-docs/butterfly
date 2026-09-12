# timeline_group: check if a timeseries is continuous

If after using
[`timeline()`](https://docs.ropensci.org/butterfly/reference/timeline.md)
you have established a timeseries is not continuous, or if you are
working with data where you expect distinct sequences or events, you can
use `timeline_group()` to extract and classify different distinct
continuous chunks of your data.

## Usage

``` r
timeline_group(df_current, datetime_variable, expected_lag = 1)
```

## Arguments

- df_current:

  data.frame, the newest/current version of dataset x.

- datetime_variable:

  string, the "datetime" variable that should be checked for continuity.

- expected_lag:

  numeric, the acceptable difference between timestep for a timeseries
  to be classed as continuous. Any difference greater than
  `expected_lag` will indicate a timeseries is not continuous. Default
  is 1. The smallest units of measurement present in the column will be
  used. In a column formatted YYYY-MM-DD day will be used, therefore 1
  would be 1 day, 7 would be a week.

## Value

A data.frame, identical to `df_current`, but with extra columns
`timeline_group`, which assigns a number to each continuous sets of data
and `timelag` which specifies the time lags between rows.

## Details

We attempt to do this without sorting, or changing the data for a couple
of reasons:

1.  There are no difference in dates: Some instruments might record
    dates that appear identical, but are still in chronological order.
    For example, high-frequency data in fractional seconds. This is a
    rare use case though.

2.  Dates are generally ascending/descending, but the instrument has
    returned to origin. Probably more common, and will results in a
    non-continuous dataset, however the records are still in
    chronological order This is something we would like to discover.
    This is accounted for in the logic in case_when().

Note: for monthly data it is recommended you convert your Date column to
a monthly format (e.g 2024-October, 10-2024, Oct-2024 etc.), so a
constant expected lag can be set (not a range of 29 - 31 days).

## Examples

``` r
# A nice continuous dataset should return TRUE
# In February, our imaginary rain gauge's onboard computer had a failure.
# The timestamp was reset to 1970-01-01

# We want to group these different distinct continuous sequences:
butterfly::timeline_group(
  forestprecipitation$february,
  datetime_variable = "time",
  expected_lag = 1
)
#>                  time rainfall_mm        timelag timeline_group
#> 1 2024-02-01 00:00:00         1.1        NA days              1
#> 2 2024-02-02 00:00:00         0.0      1.00 days              1
#> 3 2024-02-03 00:00:00         1.4      1.00 days              1
#> 4 2024-02-04 00:00:00         2.2      1.00 days              1
#> 5 1969-12-31 23:00:00         3.4 -19757.04 days              2
#> 6 1970-01-01 23:00:00         0.6      1.00 days              2
```
