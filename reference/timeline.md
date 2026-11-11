# timeline: check if a timeseries is continuous

Check if a timeseries is continuous. Even if a timeseries does not
contain obvious gaps, this does not automatically mean it is also
continuous.

## Usage

``` r
timeline(df_current, datetime_variable, expected_lag = 1)
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

A boolean, TRUE if the timeseries is continuous, and FALSE if there are
more than one continuous timeseries within the dataset.

## Details

Measuring instruments can have different behaviours when they fail. For
example, during power failure an internal clock could reset to
"1970-01-01", or the manufacturing date (say, "2021-01-01"). This leads
to unpredictable ways of checking if a dataset is continuous.

The
[`timeline_group()`](https://docs.ropensci.org/butterfly/reference/timeline_group.md)
and `timeline()` functions attempt to give the user control over how to
check for continuity by providing an `expected_lag`. The difference
between timesteps in a dataset should not exceed the `expected_lag`.

Note: for monthly data it is recommended you convert your Date column to
a monthly format (e.g 2024-October, 10-2024, Oct-2024 etc.), so a
constant expected lag can be set (not a range of 29 - 31 days).

## See also

[`timeline_group()`](https://docs.ropensci.org/butterfly/reference/timeline_group.md)

## Examples

``` r
# A nice continuous dataset should return TRUE
butterfly::timeline(
  forestprecipitation$january,
  datetime_variable = "time",
  expected_lag = 1
)
#> ✔ There are no time lags which are greater than the expected lag: 1 days. By this measure, the timeseries is continuous.
#> [1] TRUE

# In February, our imaginary rain gauge's onboard computer had a failure.
# The timestamp was reset to 1970-01-01
butterfly::timeline(
  forestprecipitation$february,
  datetime_variable = "time",
  expected_lag = 1
)
#> ℹ There are time lags which are greater than the expected lag: 1 days. This indicates the timeseries is not continuous. There are 2 distinct continuous sequences. Use `timeline_group()` to extract.
#> [1] FALSE
```
