hw05CarolineSeip
================
Caroline Seip
October 16, 2018

Table of Contents
=================

-   Load packages
-   Part 1: Factor management
-   Part 2: File I/O
-   Part 3: Visualization design
-   Part 4: Writing figures to file

Load packages
=============

Load the required packages for this assignment:

``` r
suppressPackageStartupMessages(library(tidyverse))      
suppressPackageStartupMessages(library(knitr)) 
suppressPackageStartupMessages(library(gapminder)) 
suppressPackageStartupMessages(library(plotly)) 
suppressPackageStartupMessages(library(htmlwidgets))
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

Reorder the levels of continent
-------------------------------

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
  ylab("Life expectancy (years)") +
  #Remove the legend
  guides(fill = FALSE)
```

![](hw05-exercise_files/figure-markdown_github/unnamed-chunk-6-1.png)

Yay! We reordered the continents by minimum life expectancy.

Part 2: File I/O
================

For this section I will create a new dataset by filtering the gapminder dataset for only Oceania, reordering by population size, write the file as a .csv to my local drive, and read the .csv back into R.

``` r
OceanaData <- gapminder %>% #make a new dataset
  filter(continent == "Oceania") %>% #filter for Oceana
  arrange(pop) #arrange by population

write_csv(OceanaData, "OceanaData.csv") #write the new data to a csv

readOceana <- read_csv("OceanaData.csv") #read the csv back in
```

    ## Parsed with column specification:
    ## cols(
    ##   country = col_character(),
    ##   continent = col_character(),
    ##   year = col_integer(),
    ##   lifeExp = col_double(),
    ##   pop = col_integer(),
    ##   gdpPercap = col_double()
    ## )

Let's look at what the data looked like before we wrote it to a csv

``` r
OceanaData %>% 
  kable()
```

| country     | continent |  year|  lifeExp|       pop|  gdpPercap|
|:------------|:----------|-----:|--------:|---------:|----------:|
| New Zealand | Oceania   |  1952|   69.390|   1994794|   10556.58|
| New Zealand | Oceania   |  1957|   70.260|   2229407|   12247.40|
| New Zealand | Oceania   |  1962|   71.240|   2488550|   13175.68|
| New Zealand | Oceania   |  1967|   71.520|   2728150|   14463.92|
| New Zealand | Oceania   |  1972|   71.890|   2929100|   16046.04|
| New Zealand | Oceania   |  1977|   72.220|   3164900|   16233.72|
| New Zealand | Oceania   |  1982|   73.840|   3210650|   17632.41|
| New Zealand | Oceania   |  1987|   74.320|   3317166|   19007.19|
| New Zealand | Oceania   |  1992|   76.330|   3437674|   18363.32|
| New Zealand | Oceania   |  1997|   77.550|   3676187|   21050.41|
| New Zealand | Oceania   |  2002|   79.110|   3908037|   23189.80|
| New Zealand | Oceania   |  2007|   80.204|   4115771|   25185.01|
| Australia   | Oceania   |  1952|   69.120|   8691212|   10039.60|
| Australia   | Oceania   |  1957|   70.330|   9712569|   10949.65|
| Australia   | Oceania   |  1962|   70.930|  10794968|   12217.23|
| Australia   | Oceania   |  1967|   71.100|  11872264|   14526.12|
| Australia   | Oceania   |  1972|   71.930|  13177000|   16788.63|
| Australia   | Oceania   |  1977|   73.490|  14074100|   18334.20|
| Australia   | Oceania   |  1982|   74.740|  15184200|   19477.01|
| Australia   | Oceania   |  1987|   76.320|  16257249|   21888.89|
| Australia   | Oceania   |  1992|   77.560|  17481977|   23424.77|
| Australia   | Oceania   |  1997|   78.830|  18565243|   26997.94|
| Australia   | Oceania   |  2002|   80.370|  19546792|   30687.75|
| Australia   | Oceania   |  2007|   81.235|  20434176|   34435.37|

Great, it filtered for Oceania and ordered by population, like I wanted.

Now let's see what the dataset looks like after we read it back in:

``` r
readOceana %>% 
  kable()
```

| country     | continent |  year|  lifeExp|       pop|  gdpPercap|
|:------------|:----------|-----:|--------:|---------:|----------:|
| New Zealand | Oceania   |  1952|   69.390|   1994794|   10556.58|
| New Zealand | Oceania   |  1957|   70.260|   2229407|   12247.40|
| New Zealand | Oceania   |  1962|   71.240|   2488550|   13175.68|
| New Zealand | Oceania   |  1967|   71.520|   2728150|   14463.92|
| New Zealand | Oceania   |  1972|   71.890|   2929100|   16046.04|
| New Zealand | Oceania   |  1977|   72.220|   3164900|   16233.72|
| New Zealand | Oceania   |  1982|   73.840|   3210650|   17632.41|
| New Zealand | Oceania   |  1987|   74.320|   3317166|   19007.19|
| New Zealand | Oceania   |  1992|   76.330|   3437674|   18363.32|
| New Zealand | Oceania   |  1997|   77.550|   3676187|   21050.41|
| New Zealand | Oceania   |  2002|   79.110|   3908037|   23189.80|
| New Zealand | Oceania   |  2007|   80.204|   4115771|   25185.01|
| Australia   | Oceania   |  1952|   69.120|   8691212|   10039.60|
| Australia   | Oceania   |  1957|   70.330|   9712569|   10949.65|
| Australia   | Oceania   |  1962|   70.930|  10794968|   12217.23|
| Australia   | Oceania   |  1967|   71.100|  11872264|   14526.12|
| Australia   | Oceania   |  1972|   71.930|  13177000|   16788.63|
| Australia   | Oceania   |  1977|   73.490|  14074100|   18334.20|
| Australia   | Oceania   |  1982|   74.740|  15184200|   19477.01|
| Australia   | Oceania   |  1987|   76.320|  16257249|   21888.89|
| Australia   | Oceania   |  1992|   77.560|  17481977|   23424.77|
| Australia   | Oceania   |  1997|   78.830|  18565243|   26997.94|
| Australia   | Oceania   |  2002|   80.370|  19546792|   30687.75|
| Australia   | Oceania   |  2007|   81.235|  20434176|   34435.37|

Great! It looks like the filtering and arranging survived through being written and read as a csv.

Part 3: Visualization design
============================

In this section I will recreate a graph I made in hw01.

My graph from hw01 looked like this:

``` r
plot(gapminder$continent, gapminder$lifeExp)
```

![](hw05-exercise_files/figure-markdown_github/unnamed-chunk-10-1.png)

Let's see how we could clean this up.

-   Use `ggplot2` instead of base R graphics
-   Use violin plots over boxplots to better portray the shape of the data
-   Reorder your data in a meaningful way, for this I ordered by median life expectancy
-   Colour each continent differently
-   Add meaningful x and y labels, and a title
-   Remove unnecessary information, in this case, the legend
-   Add a theme, I chose `minimal` to keep it clean, and then added a background of `ghostwhite` :ghost: spooky

``` r
#Store graph as variable `l`
l <- (gapminder %>%
#Plot continent vs lifeExp, reorder by lifeExp
ggplot(aes(fct_reorder(continent, lifeExp, .fun = median), lifeExp, fill = continent)) +
#Add a violin plot
  geom_violin() +
  #Add an x label
  xlab("Continent") +
  #Add a y label
  ylab("Life expectancy (years)") +
  #Add a title
  ggtitle("Life expectancy by continent (1952-2007)") +
  #Remove the legend
  guides(fill = FALSE) +
  #Add a theme
  theme_minimal() +
  #Change font size of axis text
  theme(axis.text = element_text(size=10),
        #Change panel background colour
        panel.background = element_rect(fill="ghostwhite")))
#Print graph
l
```

![](hw05-exercise_files/figure-markdown_github/unnamed-chunk-11-1.png)

Now we will make this graph using `plotly`

``` r
#Make plotly graph with object `l`
gg <- ggplotly(l)
#Save plotly as html
htmlwidgets::saveWidget(gg, file = "gg.html")
```

Check out my html of the plotly graph here: <https://github.com/STAT545-UBC-students/hw05-Caroline-Seip/blob/master/gg.html>

Part 4: Writing figures to file
===============================

Use `ggsave()`
--------------

``` r
#Save last plot as png
ggsave("lifeExpplot.png", width = 10, height = 8, dpi = 900)
```

Check out the png here:<https://github.com/STAT545-UBC-students/hw05-Caroline-Seip/blob/master/lifeExpplot.png>
