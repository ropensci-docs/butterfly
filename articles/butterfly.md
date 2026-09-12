# butterfly

The goal of butterfly is to aid in the verification of continually
updating timeseries data, where we expect new values over time, but want
to ensure previous data remains unchanged, and timesteps remain
continuous.

Unnoticed changes in previous data could have unintended consequences,
such as invalidating DOIs, or altering future predictions if used as
input in forecasting models.

Other unnoticed changes could include a jump in time or measurement
frequency, due to instrument failure or software updates.

This package provides functionality that can be used as part of a data
pipeline, to check and flag changes to previous data to prevent changes
going unnoticed.

You can provide butterfly with a timeseries dataset to check for
continuity, with
[`timeline()`](https://docs.ropensci.org/butterfly/reference/timeline.md),
and if it is not fully continuous as expected, split it into continuous
chunks with
[`timeline_group()`](https://docs.ropensci.org/butterfly/reference/timeline_group.md).
To check for changes to previous data, you can provide two versions of
the *same* dataset, and
[`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md) will
check if there are changes to matching rows, and tell you which rows are
new. You can use
[`catch()`](https://docs.ropensci.org/butterfly/reference/catch.md) and
[`release()`](https://docs.ropensci.org/butterfly/reference/release.md)
to extract or remove rows with changes. Full examples of functionality
are provided below.

## Data

This packages includes a small dummy dataset, `butterflycount`, which
contains a list of monthly dataframes of butterfly counts for a given
date.

``` r

library(butterfly)
butterflycount
#> $january
#>         time count
#> 1 2024-01-01    22
#> 2 2023-12-01    55
#> 3 2023-11-01    11
#> 
#> $february
#>         time count
#> 1 2024-02-01    17
#> 2 2024-01-01    22
#> 3 2023-12-01    55
#> 4 2023-11-01    11
#> 
#> $march
#>         time count
#> 1 2024-03-01    23
#> 2 2024-02-01    17
#> 3 2024-01-01    22
#> 4 2023-12-01    55
#> 5 2023-11-01    18
#> 
#> $april
#>         time value species
#> 1 2024-04-01    12 Admiral
#> 2 2024-03-01    23 Admiral
#> 3 2024-02-01    NA Admiral
#> 4 2024-01-01    22 Admiral
#> 5 2023-12-01    55 Admiral
#> 6 2023-11-01    18 Admiral
#> 
#> $may
#>         time value species
#> 1 2024-05-01    70 Admiral
#> 2 2024-04-01    12 Admiral
#> 3 2024-03-01    23 Admiral
#> 4 2024-01-01    22 Admiral
#> 5 2024-12-01    55 Admiral
#> 6 2024-11-01    18 Admiral
```

This dataset is entirely fictional, and merely included to aid in
demonstrating butterfly’s functionality.

Another dummy dataset, `forestprecipitation`, also contains a list of
monthly dataframes, but for fictional rainfall data. This dataset is
intended to illustrate an instance of instrument failure leading to
timesteps being recorded out of sync.

``` r

forestprecipitation
#> $january
#>         time rainfall_mm
#> 1 2024-01-01         0.0
#> 2 2024-01-02         2.6
#> 3 2024-01-03         0.0
#> 4 2024-01-04         0.0
#> 5 2024-01-05         3.7
#> 6 2024-01-06         0.8
#> 
#> $february
#>                  time rainfall_mm
#> 1 2024-02-01 00:00:00         1.1
#> 2 2024-02-02 00:00:00         0.0
#> 3 2024-02-03 00:00:00         1.4
#> 4 2024-02-04 00:00:00         2.2
#> 5 1969-12-31 23:00:00         3.4
#> 6 1970-01-01 23:00:00         0.6
```

## Examining datasets: `loupe()`

We can use
[`butterfly::loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md)
to examine in detail whether previous values have changed.

``` r

butterfly::loupe(
  butterflycount$february,
  butterflycount$january,
  datetime_variable = "time"
)
#> The following rows are new in 'df_current': 
#>         time count
#> 1 2024-02-01    17
#> ✔ And there are no differences with previous data.
#> [1] TRUE

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

[`butterfly::loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md)
uses
[`dplyr::semi_join()`](https://dplyr.tidyverse.org/reference/filter-joins.html)
to match the new and old objects using a common unique identifier, which
in a timeseries will be the timestep.
[`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html) is
then used to compare these and provide a detailed report of the
differences.

In addition to a report,
[`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md) also
returns `TRUE` if there are no differences and `FALSE` when there are
differences. This is especially useful when using butterfly in a
pipeline that runs in a shell environment, a check for differences to
fail gracefully.

`butterfly` follows the `waldo` philosophy of erring on the side of
providing too much information, rather than too little. It will give a
detailed feedback message on the status between two objects.

### Additional arguments from `waldo::compare()`

You have the flexibility to pass further arguments accepted by
[`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html)to
any of
[`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md),
[`catch()`](https://docs.ropensci.org/butterfly/reference/catch.md) or
[`release()`](https://docs.ropensci.org/butterfly/reference/release.md).

One such argument is `tolerance`. If we add a tolerance of 2 to the
previous example, no differences should be returned:

``` r

butterfly::loupe(
  butterflycount$march,
  butterflycount$february,
  datetime_variable = "time",
  tolerance = 2 # <- setting a tolerance of 2
)
#> The following rows are new in 'df_current': 
#>         time count
#> 1 2024-03-01    23
#> ✔ And there are no differences with previous data.
#> [1] TRUE
```

See `?waldo::compare()` for the full list of arguments.

## Extracting unexpected changes: `catch()`

You might want to return changed rows as a dataframe, instead of
returning `TRUE`/`FALSE`. For this
[`butterfly::catch()`](https://docs.ropensci.org/butterfly/reference/catch.md)is
provided.

[`butterfly::catch()`](https://docs.ropensci.org/butterfly/reference/catch.md)
only returns rows which have **changed** from the previous version. It
will not return new rows.

``` r

df_caught <- butterfly::catch(
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
#> 
#> ℹ Only these rows are returned.

df_caught
#>         time count
#> 1 2023-11-01    18
```

## Dropping unexpected changes: `release()`

Conversely,
[`butterfly::release()`](https://docs.ropensci.org/butterfly/reference/release.md)
drops all rows which have changed from the previous version. Note it
retains new rows, as these were expected.

``` r

df_released <- butterfly::release(
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
#> 
#> ℹ These will be dropped, but new rows are included.

df_released
#>         time count
#> 1 2024-03-01    23
#> 2 2024-02-01    17
#> 3 2024-01-01    22
#> 4 2023-12-01    55
```

However, you do have the option to exclude new rows as well with the
argument `include_new` set to `FALSE`.

``` r

df_release_without_new <- butterfly::release(
  butterflycount$march,
  butterflycount$february,
  datetime_variable = "time",
  include_new = FALSE
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
#> ℹ These will be dropped, along with new rows.

df_release_without_new
#>         time count
#> 1 2024-02-01    17
#> 2 2024-01-01    22
#> 3 2023-12-01    55
```

## Checking for continuity: `timeline()`

To check if a timeseries is continuous,
[`timeline()`](https://docs.ropensci.org/butterfly/reference/timeline.md)
and
[`timeline_group()`](https://docs.ropensci.org/butterfly/reference/timeline_group.md)
are provided. Even if a timeseries does not contain obvious gaps, this
does not automatically mean it is also continuous.

Measuring instruments can have different behaviours when they fail. For
example, during power failure an internal clock could reset to
“1970-01-01”, or the manufacturing date (e.g. “2021-01-01”, this is
common behaviour for Raspberry Pi’s). This leads to unpredictable ways
of checking if a dataset is continuous.

``` r

# A rain gauge which measures precipitation every day
butterfly::forestprecipitation$january
#>         time rainfall_mm
#> 1 2024-01-01         0.0
#> 2 2024-01-02         2.6
#> 3 2024-01-03         0.0
#> 4 2024-01-04         0.0
#> 5 2024-01-05         3.7
#> 6 2024-01-06         0.8

# In February there is a power failure in the instrument
butterfly::forestprecipitation$february
#>                  time rainfall_mm
#> 1 2024-02-01 00:00:00         1.1
#> 2 2024-02-02 00:00:00         0.0
#> 3 2024-02-03 00:00:00         1.4
#> 4 2024-02-04 00:00:00         2.2
#> 5 1969-12-31 23:00:00         3.4
#> 6 1970-01-01 23:00:00         0.6
```

To check if a timeseries is continuous:

``` r

butterfly::timeline(
   forestprecipitation$january,
   datetime_variable = "time",
   expected_lag = 1
 )
#> ✔ There are no time lags which are greater than the expected lag: 1 days. By this measure, the timeseries is continuous.
#> [1] TRUE
```

As expected January is a continuous dataset, where there is no more than
a difference of 1 day between timesteps.

However, in February our imaginary rain gauge’s onboard computer had a
failure.

The timestamp was reset to `1970-01-01`:

``` r

forestprecipitation$february
#>                  time rainfall_mm
#> 1 2024-02-01 00:00:00         1.1
#> 2 2024-02-02 00:00:00         0.0
#> 3 2024-02-03 00:00:00         1.4
#> 4 2024-02-04 00:00:00         2.2
#> 5 1969-12-31 23:00:00         3.4
#> 6 1970-01-01 23:00:00         0.6

butterfly::timeline(
  forestprecipitation$february,
   datetime_variable = "time",
   expected_lag = 1
 )
#> ℹ There are time lags which are greater than the expected lag: 1 days. This indicates the timeseries is not continuous. There are 2 distinct continuous sequences. Use `timeline_group()` to extract.
#> [1] FALSE
```

## Grouping distinct continuous sequences: `timeline_group()`

If we wanted to group chunks of our timeseries that are distinct, or
broken up in some way, but still continuous, we can use
[`timeline_group()`](https://docs.ropensci.org/butterfly/reference/timeline_group.md):

``` r

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

We now have groups 1 & 2, which are both continuous sets of data, but
there is no continuity between them.

## Using `butterfly` in a data processing pipeline

If you would like to know more about using `butterfly` in an operational
data processing pipeline, please refer to the article on [using
`butterfly` in an operational
pipeline](https://docs.ropensci.org/butterfly/articles/butterfly_in_pipeline.html).

## A note on controlling verbosity

Although verbosity is mostly the purpose if this package, **should** you
wish to silence messages and warnings, you can do so with
`options(rlib_message_verbosity = "quiet")` and options
`(rlib_warning_verbosity = "quiet")`.

## Rationale

There are a lot of other data comparison and QA/QC packages out there,
why butterfly?

### Unexpected changes in models

This package was originally developed to deal with
[ERA5](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels?tab=documentation)’s
initial release data, ERA5T. ERA5T data for a month is overwritten with
the final ERA5 data two months after the month in question.

Usually ERA5 and ERA5T are identical, but occasionally an issue with
input data can (for example for [09/21 -
12/21](https://confluence.ecmwf.int/display/CKB/ERA5T+issue+in+snow+depth),
and
[07/24](https://forum.ecmwf.int/t/final-validated-era5-product-to-differ-from-era5t-in-july-2024/6685))
force a recalculation, meaning previously published data differs from
the final product.

When publishing ERA5-derived datasets, and minting it with a DOI, it is
possible to continuously append without invalidating that DOI. However,
recalculation would overwrite previously published data, thereby forcing
a new publication and DOI to be minted.

We use the functionality in this package in an automated data processing
pipeline to detect changes, stop data transfer and notify the user.

### Unexpected changes in data acquisition

Measuring instruments can have different behaviours when they have a
power failure. For example, during power failure an internal clock could
reset to “1970-01-01”, or the manufacturing date (say, “2021-01-01”). If
we are automatically ingesting and processing this data, it would be
great to get a head’s up that a timeseries is no longer continuous in
the way we expect it to be. This could have consequences for any
calculation happening downstream.

To prevent writing different ways of checking for this depending on the
instrument, we wrote
[`butterfly::timeline()`](https://docs.ropensci.org/butterfly/reference/timeline.md).

### Variable measurement frequencies

In other cases, a non-continuous timeseries is intentional, for example
when there is temporal variability in the measurements taken depending
on events. At BAS, we collect data from a penguin weighbridge on Bird
Island, South Georgia. This weighbridge measure weight on two different
load cells (scales), one on the colony side and one on the ocean side,
to determine penguin weight and the direction they came from. The idea
is that we may be able to derive information on their diet, as they
return from the ocean to the colony.

You can read about this work in more detail in [Afanasyev et
al. (2015)](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0126292),
but the important point here is that the weighbridge does not collect
continuous measurements. In a remote, off-grid location this would drain
batteries far too quickly.

Therefore, when no weight is detected on the load cells it only samples
at 1hz, but as soon as any change in weight is detected it will start
collecting data at 100hz. This is of course intentional, to reduce the
sheer volume of data we need to process, but also has another benefit in
isolating (or attempting to) individual crossings.

The individual crossings are the most valuables pieces of data, as these
allow us to deduce information on weight, direction and ultimately,
diet.

In this case separating distinct, but continuous segments of data is
required. This is the reasoning behind
[`timeline_group()`](https://docs.ropensci.org/butterfly/reference/timeline_group.md).
This function allows us to split our timeseries in groups of individual
crossings.

### In summary

This package has intentionally been generalised to accommodate other,
but similar, use cases. Other examples could include a correction in
instrument calibration, compromised data transfer or unnoticed changes
in the parameterisation of a model.
