---
title: "Data wrangling with tidyverse"
description: "Some introductionary information about tidyverse"
authors: [thierryo]
date: 2018-02-09T14:14:49+01:00
categories: ["r"]
tags: ["tidyverse", "r"]
---

Real life datasources seldom provide data in exactly the format you need for the analysis. Hence most of the time you need to manipulate the data after reading it into R. There are several ways to do this, each with their pros and cons. We highly recommend the [`tidyverse`](https://www.tidyverse.org) collection of packages. The command `library(tidyverse)` will actually load the following packages: `ggplot2`, `dplyr`, `tidyr`, `readr`, `purrr`, `tibble`, `stringr` and `forecats`.

## Where to find good information on these packages:

- official [tidyverse](https://www.tidyverse.org/) website
- the R for data science book ([R4DS](http://r4ds.had.co.nz/)) by Garrett Grolemund
and Hadley Wickham. Note that this book is freely available online. A printed version is available at the INBO library.
- video tutorials:
    - [Data wrangling with R and RStudio](https://www.rstudio.com/resources/webinars/data-wrangling-with-r-and-rstudio/): a good introduction on `dplyr` and `tidyr` by Garrett Grolemund
    - `dplyr` tutorial at [useR!2014](https://www.r-project.org/nosvn/conferences/useR-2014/) by Hadley Wickham (video [part 1](https://www.youtube.com/watch?v=8SGif63VW6E) and [part 2](https://www.youtube.com/watch?v=Ue08LVuk790))
    - [tidyverse, visualization, and manipulation basics](https://www.rstudio.com/resources/webinars/tidyverse-visualization-and-manipulation-basics/): a high-level overview of `tidyverse` by Garrett Grolemund
- [Data Transformation Cheat Sheet](https://github.com/rstudio/cheatsheets/raw/master/data-transformation.pdf): a two page document which covers the most important function for `dplyr`


