# Changelog

## butterfly 1.1.2 (2025-04-04)

CRAN release: 2025-04-12

- Rephrased the DESCRIPTION description to avoid “This package”
  completely ([\#44](https://github.com/ropensci/butterfly/issues/44)).
- I have added British Antarctic Survey as “cph”, as per the LICENSE
  ([\#44](https://github.com/ropensci/butterfly/issues/44)).

## butterfly 1.1.1 (2025-04-02)

#### DOCUMENTATION FIXES

- Adding rOpenSci Reviewers to DESCRIPTION.
- Updating URLs to match rOpenSci
  ([\#43](https://github.com/ropensci/butterfly/issues/43)).
- Adding rhub yaml
  ([\#43](https://github.com/ropensci/butterfly/issues/43)).
- Spelling checks in DESCRIPTION
  ([\#43](https://github.com/ropensci/butterfly/issues/43)).
- Elaborate package description in DESCRIPTION
  ([\#43](https://github.com/ropensci/butterfly/issues/43)).
- Changing [@returns](https://github.com/returns) to
  [@return](https://github.com/return) to comply with tags check
  ([\#43](https://github.com/ropensci/butterfly/issues/43))

## butterfly 1.1.0 (2025-03-04)

#### NEW FEATURES

- Adding new
  [`butterfly::timeline()`](https://docs.ropensci.org/butterfly/reference/timeline.md)
  function, which checks if a time series is continuous. The user can
  specify the difference between timesteps expected
  ([\#24](https://github.com/ropensci/butterfly/issues/24)).
- Adding new
  [`butterfly::timeline_group()`](https://docs.ropensci.org/butterfly/reference/timeline_group.md)
  function, which groups a time series in distinct, but continuous
  groups ([\#24](https://github.com/ropensci/butterfly/issues/24)).
- Adding new `butterflymess` dataset, which provides a “messy” version
  of `butterflycount` for testing purposes
  ([\#33](https://github.com/ropensci/butterfly/issues/33)).

#### MINOR IMPROVEMENTS

- Enabled further passing of `waldo` parameters (such as tolerance)
  ([\#18](https://github.com/ropensci/butterfly/issues/18)).
- Improved CONTRIBUTING.md
  ([\#29](https://github.com/ropensci/butterfly/issues/29)).
- Adding further tests, using `butterflymess`, to test function response
  to badly formatted datasets
  ([\#33](https://github.com/ropensci/butterfly/issues/33)).
- Improved
  [`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md)
  feedback when there are no new rows
  ([\#34](https://github.com/ropensci/butterfly/issues/34)).

#### DOCUMENTATION FIXES

- Added section on contributing to `README`
  ([\#32](https://github.com/ropensci/butterfly/issues/32)).
- Improved introduction of main vignette
  ([\#35](https://github.com/ropensci/butterfly/issues/35)).
- Explicitly mentioned shell scripts are run in Bash
  ([\#35](https://github.com/ropensci/butterfly/issues/35)).
- Improve description of what
  [`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md)
  does ([\#36](https://github.com/ropensci/butterfly/issues/36)).
- Elaborate on [`all.equal()`](https://rdrr.io/r/base/all.equal.html),
  in addition to
  [`waldo::compare()`](https://waldo.r-lib.org/reference/compare.html)
  ([\#36](https://github.com/ropensci/butterfly/issues/36)).
- Fix error in
  [`catch()`](https://docs.ropensci.org/butterfly/reference/catch.md)
  description, where it was mentioned the function uses `inner_join()`,
  when actually it uses `anti_join()`
  ([\#36](https://github.com/ropensci/butterfly/issues/36)).
- Clarified
  [`timeline()`](https://docs.ropensci.org/butterfly/reference/timeline.md)
  description on how the expected lag units work for different periods
  of time (days, weeks)
  ([\#39](https://github.com/ropensci/butterfly/issues/39)).
- Grammar and punctuation fixes
  ([\#35](https://github.com/ropensci/butterfly/issues/35),
  [\#36](https://github.com/ropensci/butterfly/issues/36)).

## butterfly 1.0.0 (2024-10-24)

#### NEW FEATURES

- Initial release:

  - [`butterfly::loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md) -
    examines in detail whether previous values have changed, and returns
    TRUE/FALSE for no change/change.
  - [`butterfly::catch()`](https://docs.ropensci.org/butterfly/reference/catch.md) -
    returns rows which contain previously changed values in a dataframe.
  - [`butterfly::release()`](https://docs.ropensci.org/butterfly/reference/release.md) -
    drops rows which contain previously changed values, and returns a
    dataframe containing new and unchanged rows.
  - [`butterfly::create_object_list()`](https://docs.ropensci.org/butterfly/reference/create_object_list.md) -
    returns a list of objects required by all of
    [`loupe()`](https://docs.ropensci.org/butterfly/reference/loupe.md),
    [`catch()`](https://docs.ropensci.org/butterfly/reference/catch.md)
    and
    [`release()`](https://docs.ropensci.org/butterfly/reference/release.md).
    Contains underlying functionality.
  - `butterflycount` - a list of monthly dataframes, which contain
    fictional butterfly counts for a given date.
