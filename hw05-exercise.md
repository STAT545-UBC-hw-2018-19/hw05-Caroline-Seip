hw05CarolineSeip
================
Caroline Seip
October 16, 2018

Table of Contents
=================

Load packages
=============

Load the required packages for this assignment:

``` r
suppressPackageStartupMessages(library(tidyverse))      
suppressPackageStartupMessages(library(knitr)) 
suppressPackageStartupMessages(library(gapminder)) 
suppressPackageStartupMessages(library(plotly)) 
```

Part 1: Factor management
=========================

In this section we will determine whether we are working with factors, then drop factors and reorder the levels.

First let's check if we are working with factors:

``` r
glimpse(gapminder)
```

    ## Observations: 1,704
    ## Variables: 6
    ## $ country   <fct> Afghanistan, Afghanistan, Afghanistan, Afghanistan, ...
    ## $ continent <fct> Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia...
    ## $ year      <int> 1952, 1957, 1962, 1967, 1972, 1977, 1982, 1987, 1992...
    ## $ lifeExp   <dbl> 28.801, 30.332, 31.997, 34.020, 36.088, 38.438, 39.8...
    ## $ pop       <int> 8425333, 9240934, 10267083, 11537966, 13079460, 1488...
    ## $ gdpPercap <dbl> 779.4453, 820.8530, 853.1007, 836.1971, 739.9811, 78...

Ok, so now we know that country and continent are both factors, and there are 1704 observations in the gapminder dataset.

Let's drop the continent Oceania from the gapminder dataset now:

Drop Oceania
------------

``` r
noO <- gapminder %>% 
  filter(continent != "Oceania") %>% #filter gapminder dataset for continents that are not Oceania
  glimpse()
```

    ## Observations: 1,680
    ## Variables: 6
    ## $ country   <fct> Afghanistan, Afghanistan, Afghanistan, Afghanistan, ...
    ## $ continent <fct> Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia...
    ## $ year      <int> 1952, 1957, 1962, 1967, 1972, 1977, 1982, 1987, 1992...
    ## $ lifeExp   <dbl> 28.801, 30.332, 31.997, 34.020, 36.088, 38.438, 39.8...
    ## $ pop       <int> 8425333, 9240934, 10267083, 11537966, 13079460, 1488...
    ## $ gdpPercap <dbl> 779.4453, 820.8530, 853.1007, 836.1971, 739.9811, 78...

Now we can see that the tibble only contains 1680 observations. So it worked! We removed Oceania.

Let's check the levels of the continent factor of our new object:

``` r
unique(noO$continent) #check levels of continent
```

    ## [1] Asia     Europe   Africa   Americas
    ## Levels: Africa Americas Asia Europe Oceania

Uh oh! There are no values for Oceania in the new object, but it still exists as a level. Let's get rid of it.

Remove unused factor levels
---------------------------

Using `fct_drop` let's remove the unused factor level:

``` r
dropO <- noO %>% #make a new variable
  mutate(continent = fct_drop(continent)) #drop unused factors of continent

unique(dropO$continent) #check levels of continent in the new variable
```

    ## [1] Asia     Europe   Africa   Americas
    ## Levels: Africa Americas Asia Europe

Now we can see that Oceania was removed as a factor, yay!

Reorder the levels of country
=============================

For this part I will reorder the levels of continent by minimum life expectancy using `fct_reorder`.

``` r
gapminder %>% 
  #Make a plot, reordering continent by minimum life expectancy
  ggplot(aes(fct_reorder(continent, lifeExp, .fun = min), lifeExp, fill = continent)) +
  #Add a violin plot
  geom_violin() +
  #Add an x label
  xlab("Continent") +
  #Add a y label
  ylab("Life expectancy (years)")
```

![](hw05-exercise_files/figure-markdown_github/unnamed-chunk-6-1.png)

Yay! We reordered the continents by minimum life expectancy.
