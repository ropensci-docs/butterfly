# butterfly: Verification for Continually Updating Time Series Data

Verification of continually updating time series data where we expect
new values, but want to ensure previous data remains unchanged. Data
previously recorded could change for a number of reasons, such as
discovery of an error in model code, a change in methodology or
instrument recalibration. Monitoring data sources for these changes is
not always possible. Other unnoticed changes could include a jump in
time or measurement frequency, due to instrument failure or software
updates. Functionality is provided that can be used to check and flag
changes to previous data to prevent changes going unnoticed, as well as
unexpected jumps in time.

## See also

Useful links:

- <https://docs.ropensci.org/butterfly/>

- <https://github.com/ropensci/butterfly/>

- Report bugs at <https://github.com/ropensci/butterfly/issues>

## Author

**Maintainer**: Thomas Zwagerman <thozwa@bas.ac.uk>
([ORCID](https://orcid.org/0009-0003-3742-3234))

Other contributors:

- British Antarctic Survey \[copyright holder\]

- Quentin Read (Quentin reviewed the package (v. 1.1.0) for rOpenSci,
  see \<https://github.com/ropensci/software-review/issues/676\>)
  \[reviewer\]
