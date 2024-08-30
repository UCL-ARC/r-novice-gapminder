[![01 Build and Deploy Site](https://github.com/UCL-ARC/r-amr-epidemiology/actions/workflows/sandpaper-main.yaml/badge.svg)](https://github.com/UCL-ARC/r-amr-epidemiology/actions/workflows/sandpaper-main.yaml)

# R for AMR Epidemiology adapted from R for Reproducible Scientific Analysis

This lesson was commisioned by the EPSRC Digital Health Hub for AMR, for delivery
to the UK HSA w/c 16th September 2024.

An introduction to R for non-programmers using the [Gapminder][gapminder] data.
In addition, this lesson makes use of open data from the [Centre for Consumer Research Data](https://data.cdrc.ac.uk/)
and bespoke synthetic data provided by UKHSA.

Please see [https://ucl-arc.github.io/r-amr-epidemiology](https://ucl-arc.github.io/r-amr-epidemiology) for a rendered version of this material,
[the lesson template documentation][lesson-example]
for instructions on formatting, building, and submitting material,
or run `make` in this directory for a list of helpful commands.

The goal of this lesson is to teach novice programmers to write modular code
and best practices for using R for data analysis. R is commonly used in many
scientific disciplines for statistical analysis and its array of third-party
packages. We find that many scientists who come to Software Carpentry workshops
use R and want to learn more. The emphasis of these materials is to give
attendees a strong foundation in the fundamentals of R, and to teach best
practices for scientific computing: breaking down analyses into modular units,
task automation, and encapsulation.

This lesson has been expanded to incorporate additional Software Carpentry content 
on the use of Git for Version Control, navigating files and directories in a terminal, 
SQL and new content on the creation and validation of regression models.

A variety of third party packages are used throughout this workshop. These
are not necessarily the best, nor are they comprehensive, but they are
packages we find useful, and have been chosen primarily for their
usability.

## R for Reproducible Scientific Analysis

Current Maintainers:

- [Naupaka Zimmerman][zimmerman_naupaka]
- [Sehrish Kanwal](https://github.com/skanwal)
- [Matthieu Bruneaux](https://github.com/matthieu-bruneaux)

Previous Maintainers:

- [David Mawdsley][mawdsley_david]
- [Jeff Oliver][oliver_jeffrey]
- Tom Wright

[gapminder]: https://www.gapminder.org/
[lesson-example]: https://carpentries.github.io/lesson-example
[zimmerman_naupaka]: https://carpentries.org/maintainers/#naupaka
[mawdsley_david]: https://carpentries.org/maintainers/#mawds
[oliver_jeffrey]: https://carpentries.org/maintainers/#jcoliver

## Additional Carpentries Lessons incorporated
- [SWC The Unix Shell: Navigating Files and Directories](https://swcarpentry.github.io/shell-novice/02-filedir.html)
- [SWC Version Control with Git](https://swcarpentry.github.io/git-novice), modified for context.
- [LC SQL](https://librarycarpentry.org/lc-sql/)

## Additional non-Carpentries materials referenced
- [https://libguides.princeton.edu/R-linear_regression](https://libguides.princeton.edu/R-linear_regression)
- [https://www.statisticssolutions.com/free-resources/directory-of-statistical-analyses/assumptions-of-linear-regression/](https://www.statisticssolutions.com/free-resources/directory-of-statistical-analyses/assumptions-of-linear-regression/)
- [https://cran.r-project.org/web/packages/broom/vignettes/broom.html](https://cran.r-project.org/web/packages/broom/vignettes/broom.html)
- [https://jmsallan.netlify.app/blog/linear-regression-with-broom/](https://jmsallan.netlify.app/blog/linear-regression-with-broom/)
- [https://stats.oarc.ucla.edu/r/dae/logit-regression/](https://stats.oarc.ucla.edu/r/dae/logit-regression/)
- [https://github.com/jenineharris/logistic-regression-tutorial/blob/main/20211210-logistic-regression-tutorial-code.R](https://github.com/jenineharris/logistic-regression-tutorial/blob/main/20211210-logistic-regression-tutorial-code.R)
- [https://dept.stat.lsa.umich.edu/~jerrick/courses/stat506_f23/08-sql.html](https://dept.stat.lsa.umich.edu/~jerrick/courses/stat506_f23/08-sql.html)
- [https://www.quackit.com/sqlite/tutorial/about_sqlite.cfm](https://www.quackit.com/sqlite/tutorial/about_sqlite.cfm)

