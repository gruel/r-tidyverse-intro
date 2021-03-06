---
title: Manipulating tibbles with dplyr
teaching: 40
exercises: 15
questions:
- "How can I manipulate tibbles without repeating myself?"
objectives:
- " To be able to use the six main `dplyr` data manipulation 'verbs' with pipes."
keypoints:
- "Use the `dplyr` package to manipulate tibbles."
- "Use `select()` to choose variables from a tibbles."
- "Use `filter()` to choose data based on values."
- "Use `group_by()` and `summarize()` to work with subsets of data."
- "Use `mutate()` to create new variables."
source: Rmd
---



In the previous episode we used the `readr` package to load tabular data into a tibble within R.  The `readr` package is part of a family of packages known as the   [tidyverse](http://tidyverse.org/).  The tidyverse packages are designed to work well together; they provide a modern and streamlined approach to data-analysis, and deal with some of the idiosyncrasies of base R.


This loads the most commonly used packages in the tidyverse; we used `readr` in the previous episode.  We will cover all of the other main packages, with the exception of `purrr` in this course. There are other [libraries included](https://github.com/tidyverse/tidyverse) but these are less widely used, and must be loaded manually if they are required; these aren't covered in this course. 

Let's dive in and look at how we can use the tidyverse to analyse and, in a couple of episodes' time,  plot data from the [gapminder project](https://www.gapminder.org/).  At [the start of the course]({{ page.root}}/02-project-intro), you should have copied the file `gapminder-FiveYearData.csv` to your `data` directory.     Take a look at it using a text editor such as notepad.   The first line contains variable names, and values are separated by commas.  Each record starts on a new line. 

As we did with the [previous episode]({{ page.root }}/03-loading-data-into-R) we use the `read_csv()` function to load data from a comma separated file. Let's make a new script (using the file menu), and load the tidyverse: (in the previous episode we only loaded `readr`; since we'll be using several packages in the tidyverse, we load them all)


~~~
library("tidyverse")
~~~
{: .language-r}



~~~
Loading tidyverse: ggplot2
Loading tidyverse: tibble
Loading tidyverse: tidyr
Loading tidyverse: readr
Loading tidyverse: purrr
Loading tidyverse: dplyr
~~~
{: .output}



~~~
Conflicts with tidy packages ----------------------------------------------
~~~
{: .output}



~~~
filter(): dplyr, stats
lag():    dplyr, stats
~~~
{: .output}



~~~
gapminder <- read_csv("./data/gapminder-FiveYearData.csv")
~~~
{: .language-r}



~~~
Parsed with column specification:
cols(
  country = col_character(),
  year = col_integer(),
  pop = col_double(),
  continent = col_character(),
  lifeExp = col_double(),
  gdpPercap = col_double()
)
~~~
{: .output}

As we discussed in the [previous episode]({{ page.root }}/03-loading-data-into-R), variables in R can be character, integer, double, etc.   A tibble (and R's built in equivalent; the data-frame) require that all the values in a particular column have the same data type.  The `read_csv()` function will attempt to infer the data type of each column, and prints the column types it has guessed to the screen.  If the wrong column types have been generated, you can pass the `col_types=` option to `read_csv()`.  

For example, if we wanted to load the `pop` column as a character string, we would use:


~~~
gapminderPopChar <- read_csv("./data/gapminder-FiveYearData.csv", 
                             col_types = cols(
                               country = col_character(),
                               year = col_integer(),
                               pop = col_character(),
                               continent = col_character(),
                               lifeExp = col_double(),
                               gdpPercap = col_double()
) )
~~~
{: .language-r}

> ## Setting column types
> 
> Try reading a file using the `read_csv()` defaults (i.e. guessing column types).
> If this fails you can cut and paste the guessed column specification, and modify
> this with the correct column types.  It is good practice to do this anyway; it makes
> the data types of your columns explicit, and will help protect you if the format 
> of your data changes.
{: .callout}

## Manipulating tibbles 

Manipulation of tibbles means many things to many researchers. We often
select only certain observations (rows) or variables (columns). We often group the
data by a certain variable(s), or calculate summary statistics.

## The `dplyr` package

The  [`dplyr`](https://cran.r-project.org/web/packages/dplyr/dplyr.pdf)
package is part of the tidyverse.  It provides a number of very useful functions for manipulating tibbles (and their base-R cousin, the `data.frame`) 
in a way that will reduce repetition, reduce the probability of making
errors, and probably even save you some typing. 

We will cover:

1. selecting variables with `select()`
2. subsetting observations with `filter()`
3. grouping observations with `group_by()`
4. generating summary statistics using `summarize()`
5. generating new variables using `mutate()`
6. Sorting tibbles using `arrange()`
7. chaining operations together using pipes `%>%` 

## Using `select()`

If, for example, we wanted to move forward with only a few of the variables in
our tibble we use the `select()` function. This will keep only the
variables you select.


~~~
year_country_gdp <- select(gapminder,year,country,gdpPercap)
print(year_country_gdp)
~~~
{: .language-r}



~~~
# A tibble: 1,704 x 3
    year     country gdpPercap
   <int>       <chr>     <dbl>
 1  1952 Afghanistan  779.4453
 2  1957 Afghanistan  820.8530
 3  1962 Afghanistan  853.1007
 4  1967 Afghanistan  836.1971
 5  1972 Afghanistan  739.9811
 6  1977 Afghanistan  786.1134
 7  1982 Afghanistan  978.0114
 8  1987 Afghanistan  852.3959
 9  1992 Afghanistan  649.3414
10  1997 Afghanistan  635.3414
# ... with 1,694 more rows
~~~
{: .output}

Select will select _columns_ of data.  What if we want to select rows that meet certain criteria?  

## Using `filter()`

The `filter()` function is used to select rows of data.  For example, to select only countries in 
Europe:


~~~
gapminder_Europe <- filter(gapminder, continent=="Europe") 
print(gapminder_Europe)
~~~
{: .language-r}



~~~
# A tibble: 360 x 6
   country  year     pop continent lifeExp gdpPercap
     <chr> <int>   <dbl>     <chr>   <dbl>     <dbl>
 1 Albania  1952 1282697    Europe  55.230  1601.056
 2 Albania  1957 1476505    Europe  59.280  1942.284
 3 Albania  1962 1728137    Europe  64.820  2312.889
 4 Albania  1967 1984060    Europe  66.220  2760.197
 5 Albania  1972 2263554    Europe  67.690  3313.422
 6 Albania  1977 2509048    Europe  68.930  3533.004
 7 Albania  1982 2780097    Europe  70.420  3630.881
 8 Albania  1987 3075321    Europe  72.000  3738.933
 9 Albania  1992 3326498    Europe  71.581  2497.438
10 Albania  1997 3428038    Europe  72.950  3193.055
# ... with 350 more rows
~~~
{: .output}

Only rows of the data where the condition (i.e. `continent=="Europe") is `TRUE` are kept.

## Using pipes and dplyr

We've now seen how to choose certain columns of data (using `select()`) and certain rows of data (using `filter()`).  In an analysis we often want to do both of these things (and many other things, like calculating summary statistics, which we'll come to shortly).    How do we combine these?

There are several ways of doing this; the method we will learn about today is using _pipes_.  

The pipe operator `%>%` lets us pipe the output of one command into the next.   This allows us to build up a data-processing pipeline.  This approach has several advantages:

* We can build the pipeline piecemeal - building the pipeline step-by-step is easier than trying to 
perform a complex series of operations in one go
* It is easy to modify and reuse the pipeline
* We don't have to make temporary tibbles as the analysis progresses

> ## Pipelines and the shell
>
> If you're familiar with the Unix shell, you may already have used pipes to
> pass the output from one command to the next.  The concept is the same, except
> the shell uses the `|` character rather than R's pipe operator `%>%`
{: .callout}


> ## Keyboard shortcuts and getting help
> 
> The pipe operator can be tedious to type.  In Rstudio pressing <kbd>Ctrl</kbd> + <kbd>Shift</kbd>+<kbd>M</kbd> under
> Windows / Linux will insert the pipe operator.  On the mac, use <kbd>&#8984;</kbd> + <kbd>Shift</kbd>+<kbd>M</kbd>.
>
> We can use tab completion to complete variable names when entering commands.
> This saves typing and reduces the risk of error.
> 
> RStudio includes a helpful "cheat sheet", which summarises the main functionality
> and syntax of `dplyr` and the related package `tidyr`.  This can be accessed via the
> help menu --> cheatsheets --> data manipulation with dplyr, tidyr. 
> (In RStudio 1.1 this has been replaced with the cheat sheet "data transformation with dplyr".)
>
{: .callout}

Let's rewrite the select command example using the pipe operator:


~~~
year_country_gdp <- gapminder %>% select(year,country,gdpPercap)
print(year_country_gdp)
~~~
{: .language-r}



~~~
# A tibble: 1,704 x 3
    year     country gdpPercap
   <int>       <chr>     <dbl>
 1  1952 Afghanistan  779.4453
 2  1957 Afghanistan  820.8530
 3  1962 Afghanistan  853.1007
 4  1967 Afghanistan  836.1971
 5  1972 Afghanistan  739.9811
 6  1977 Afghanistan  786.1134
 7  1982 Afghanistan  978.0114
 8  1987 Afghanistan  852.3959
 9  1992 Afghanistan  649.3414
10  1997 Afghanistan  635.3414
# ... with 1,694 more rows
~~~
{: .output}

To help you understand why we wrote that in that way, let's walk through it step
by step. First we summon the gapminder tibble and pass it on, using the pipe
symbol `%>%`, to the next step, which is the `select()` function. In this case
we don't specify which data object we use in the `select()` function since in
gets that from the previous pipe. 

What if we wanted to combine this with the filter example? I.e. we want to select year, country and GDP per capita, but only for countries in Europe?  We can join these two operations using a pipe; feeding the output of one command directly into the next:



~~~
year_country_gdp_euro <- gapminder %>%
    filter(continent == "Europe") %>%
    select(year,country,gdpPercap)
print(year_country_gdp_euro)
~~~
{: .language-r}



~~~
# A tibble: 360 x 3
    year country gdpPercap
   <int>   <chr>     <dbl>
 1  1952 Albania  1601.056
 2  1957 Albania  1942.284
 3  1962 Albania  2312.889
 4  1967 Albania  2760.197
 5  1972 Albania  3313.422
 6  1977 Albania  3533.004
 7  1982 Albania  3630.881
 8  1987 Albania  3738.933
 9  1992 Albania  2497.438
10  1997 Albania  3193.055
# ... with 350 more rows
~~~
{: .output}

Note that the order of these operations matters; if we reversed the order of the `select()` and `filter()` functions, the `continent` variable wouldn't exist in the data-set when we came to apply the filter.  

What about if we wanted to match more than one item?  To do this we use the `%in%` operator:


~~~
gapminder_scandinavia <- gapminder %>% 
  filter(country %in% c("Denmark",
                        "Norway",
                        "Sweden"))
~~~
{: .language-r}


> ## Another way of thinking about pipes
>
> It might be useful to think of the statement
> 
> ~~~
>  gapminder %>%
>     filter(continent=="Europe") %>%
>     select(year,country,gdpPercap)
> ~~~
> {: .language-r}
>  as a sentence, which we can read as
> "take the gapminder data *and then* `filter` records where continent == Europe
> *and then* `select` the year, country and gdpPercap
> 
> We can think of the `filter()` and `select()` functions as verbs in the sentence; 
> they do things to the data flowing through the pipeline.  
>
{: .callout}

> ## Splitting your commands over multiple lines
> 
> It's generally a good idea to put one command per line when
> writing your analyses.  This makes them easier to read.   When
> doing this, it's important that the `%>%` goes at the _end_ of the
> line, as in the example above.  If we put it at the beginning of a line, e.g.:
> 
> 
> ~~~
> gapminder_benelux <- gapminder 
> %>% filter(country %in% c("Belgium", "Netherlands", "France"))
> ~~~
> {: .language-r}
> 
> 
> 
> ~~~
> Error: <text>:2:1: unexpected SPECIAL
> 1: gapminder_benelux <- gapminder 
> 2: %>%
>    ^
> ~~~
> {: .error}
> 
> the first line makes a valid R command.  R will then treat the next line 
> as a new command, which won't work.
{: .callout}


> ## Challenge 1
>
> Write a single command (which can span multiple lines and includes pipes) that
> will produce a tibble that has the values of `lifeExp`, `country`
> and `year`, for the countries in Africa, but not for other Continents.  How many rows does your tibble  
> have? (You can use the `nrow()` function to find out how many rows are in a tibble.)
>
> > ## Solution to Challenge 1
> >
> >~~~
> >year_country_lifeExp_Africa <- gapminder %>%
> >                            filter(continent=="Africa") %>%
> >                            select(year,country,lifeExp)
> > nrow(year_country_lifeExp_Africa)
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> >[1] 624
> >~~~
> >{: .output}
> > As with last time, first we pass the gapminder tibble to the `filter()`
> > function, then we pass the filtered version of the gapminder tibble  to the
> > `select()` function. **Note:** The order of operations is very important in this
> > case. If we used 'select' first, filter would not be able to find the variable
> > continent since we would have removed it in the previous step.
> {: .solution}
{: .challenge}


## Sorting tibbles

The `arrange()` function will sort a tibble by one or more of the variables in it:


~~~
gapminder %>%
  filter(continent == "Europe", year == 2007) %>% 
  arrange(pop)
~~~
{: .language-r}



~~~
# A tibble: 30 x 6
                  country  year     pop continent lifeExp gdpPercap
                    <chr> <int>   <dbl>     <chr>   <dbl>     <dbl>
 1                Iceland  2007  301931    Europe  81.757 36180.789
 2             Montenegro  2007  684736    Europe  74.543  9253.896
 3               Slovenia  2007 2009245    Europe  77.926 25768.258
 4                Albania  2007 3600523    Europe  76.423  5937.030
 5                Ireland  2007 4109086    Europe  78.885 40675.996
 6                Croatia  2007 4493312    Europe  75.748 14619.223
 7 Bosnia and Herzegovina  2007 4552198    Europe  74.852  7446.299
 8                 Norway  2007 4627926    Europe  80.196 49357.190
 9                Finland  2007 5238460    Europe  79.313 33207.084
10        Slovak Republic  2007 5447502    Europe  74.663 18678.314
# ... with 20 more rows
~~~
{: .output}
We can use the `desc()` function to sort a variable in reverse order:


~~~
gapminder %>%
  filter(continent == "Europe", year == 2007) %>% 
  arrange(desc(pop))
~~~
{: .language-r}



~~~
# A tibble: 30 x 6
          country  year      pop continent lifeExp gdpPercap
            <chr> <int>    <dbl>     <chr>   <dbl>     <dbl>
 1        Germany  2007 82400996    Europe  79.406 32170.374
 2         Turkey  2007 71158647    Europe  71.777  8458.276
 3         France  2007 61083916    Europe  80.657 30470.017
 4 United Kingdom  2007 60776238    Europe  79.425 33203.261
 5          Italy  2007 58147733    Europe  80.546 28569.720
 6          Spain  2007 40448191    Europe  80.941 28821.064
 7         Poland  2007 38518241    Europe  75.563 15389.925
 8        Romania  2007 22276056    Europe  72.476 10808.476
 9    Netherlands  2007 16570613    Europe  79.762 36797.933
10         Greece  2007 10706290    Europe  79.483 27538.412
# ... with 20 more rows
~~~
{: .output}

## Generating new variables

The `mutate()` function lets us add new variables to our tibble.  It will often be the case that these are variables we _derive_ from existing variables in the data-frame. 

As an example, the gapminder data contains the population of each country, and its GDP per capita.  We can use this to calculate the total GDP of each country:


~~~
gapminder_totalgdp <- gapminder %>% 
  mutate(gdp = gdpPercap * pop)
~~~
{: .language-r}

We can also use functions within mutate to generate new variables.  For example, to take the log of `gdpPercap` we could use:


~~~
gapminder %>% 
  mutate(logGdpPercap = log(gdpPercap))
~~~
{: .language-r}



~~~
# A tibble: 1,704 x 7
       country  year      pop continent lifeExp gdpPercap logGdpPercap
         <chr> <int>    <dbl>     <chr>   <dbl>     <dbl>        <dbl>
 1 Afghanistan  1952  8425333      Asia  28.801  779.4453     6.658583
 2 Afghanistan  1957  9240934      Asia  30.332  820.8530     6.710344
 3 Afghanistan  1962 10267083      Asia  31.997  853.1007     6.748878
 4 Afghanistan  1967 11537966      Asia  34.020  836.1971     6.728864
 5 Afghanistan  1972 13079460      Asia  36.088  739.9811     6.606625
 6 Afghanistan  1977 14880372      Asia  38.438  786.1134     6.667101
 7 Afghanistan  1982 12881816      Asia  39.854  978.0114     6.885521
 8 Afghanistan  1987 13867957      Asia  40.822  852.3959     6.748051
 9 Afghanistan  1992 16317921      Asia  41.674  649.3414     6.475959
10 Afghanistan  1997 22227415      Asia  41.763  635.3414     6.454162
# ... with 1,694 more rows
~~~
{: .output}

The dplyr cheat sheet contains many useful functions which can be used with dplyr.  This can be found in the help menu of RStudio. You will use one of these functions in the next challenge.

> ## Challenge 2
> 
> Create a tibble containing each country in Europe, its life expectancy in 2007
> and the rank of the country's life expectancy. (note that ranking the countries _will not_ sort the table; the row order will be unchanged.  You can use the `arrange()` function to sort the table).
>
> Hint: First `filter()` to get the rows you want, and then use `mutate()` to create a new variable with the rank in it.  The cheat-sheet contains useful
> functions you can use when you make new variables (the cheat-sheets can be found in the help menu in RStudio).  
> There are several functions for ranking observations, which handle tied values differently.  For this exercise it 
> doesn't matter which function you choose.
>
> Can you reverse the ranking order
> so that the country with the longest life expectancy gets the lowest rank?
> Hint: This is similar to sorting in reverse order
>
> > ## Solution to challenge 2
> > 
> > ~~~
> > europeLifeExp <- gapminder %>% 
> >   filter(continent == "Europe", year == 2007) %>% 
> >   select(country, lifeExp) %>% 
> >   mutate(rank = min_rank(lifeExp))
> > print(europeLifeExp, n=100)
> > ~~~
> > {: .language-r}
> > 
> > 
> > 
> > ~~~
> > # A tibble: 30 x 3
> >                   country lifeExp  rank
> >                     <chr>   <dbl> <int>
> >  1                Albania  76.423    11
> >  2                Austria  79.829    23
> >  3                Belgium  79.441    20
> >  4 Bosnia and Herzegovina  74.852     8
> >  5               Bulgaria  73.005     3
> >  6                Croatia  75.748    10
> >  7         Czech Republic  76.486    12
> >  8                Denmark  78.332    15
> >  9                Finland  79.313    17
> > 10                 France  80.657    26
> > 11                Germany  79.406    18
> > 12                 Greece  79.483    21
> > 13                Hungary  73.338     4
> > 14                Iceland  81.757    30
> > 15                Ireland  78.885    16
> > 16                  Italy  80.546    25
> > 17             Montenegro  74.543     6
> > 18            Netherlands  79.762    22
> > 19                 Norway  80.196    24
> > 20                 Poland  75.563     9
> > 21               Portugal  78.098    14
> > 22                Romania  72.476     2
> > 23                 Serbia  74.002     5
> > 24        Slovak Republic  74.663     7
> > 25               Slovenia  77.926    13
> > 26                  Spain  80.941    28
> > 27                 Sweden  80.884    27
> > 28            Switzerland  81.701    29
> > 29                 Turkey  71.777     1
> > 30         United Kingdom  79.425    19
> > ~~~
> > {: .output}
> > 
> > To reverse the order of the ranking, use the `desc` function, i.e.
> > `mutate(rank = min_rank(desc(lifeExp)))`
> > 
> > There are several functions for calculating ranks; you may have used, e.g. `dense_rank()`
> > The functions handle ties differently.  The help file for `dplyr`'s ranking functions
> > explains the differences, and can be accessed with `?ranking`
> {: .solution}
{: .challenge}

## Calculating summary statistics

We often wish to calculate a summary statistic (the mean, standard deviation, etc.)
for a variable.  We frequently want to calculate a separate summary statistic for several
groups of data (e.g. the experiment and control group).    We can calculate a summary statistic
for the whole data-set using the dplyr's `summarise()` function:


~~~
gapminder %>% 
  filter(year == 2007) %>% 
  summarise(meanlife = mean(lifeExp), medianlife = median(lifeExp))
~~~
{: .language-r}



~~~
# A tibble: 1 x 2
  meanlife medianlife
     <dbl>      <dbl>
1 67.00742    71.9355
~~~
{: .output}

To generate summary statistics for each value of another variable we use the 
`group_by()` function:


~~~
gapminder %>% 
  filter(year == 2007) %>% 
  group_by(continent) %>% 
  summarise(meanlife = mean(lifeExp), medianlife = median(lifeExp))
~~~
{: .language-r}



~~~
# A tibble: 5 x 3
  continent meanlife medianlife
      <chr>    <dbl>      <dbl>
1    Africa 54.80604    52.9265
2  Americas 73.60812    72.8990
3      Asia 70.72848    72.3960
4    Europe 77.64860    78.6085
5   Oceania 80.71950    80.7195
~~~
{: .output}

> ## Challenge 3
>
> For each combination of continent and year, calculate the average life expectancy.
>
> > ## Solution to Challenge 3
> >
> >
> >~~~
> > lifeExp_bycontinentyear <- gapminder %>% 
> >    group_by(continent, year) %>% 
> >   summarise(mean_lifeExp = mean(lifeExp))
> > print(lifeExp_bycontinentyear)
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 60 x 3
> ># Groups:   continent [?]
> >   continent  year mean_lifeExp
> >       <chr> <int>        <dbl>
> > 1    Africa  1952     39.13550
> > 2    Africa  1957     41.26635
> > 3    Africa  1962     43.31944
> > 4    Africa  1967     45.33454
> > 5    Africa  1972     47.45094
> > 6    Africa  1977     49.58042
> > 7    Africa  1982     51.59287
> > 8    Africa  1987     53.34479
> > 9    Africa  1992     53.62958
> >10    Africa  1997     53.59827
> ># ... with 50 more rows
> >~~~
> >{: .output}
> {: .solution}
{: .challenge}

## `count()` and `n()`
A very common operation is to count the number of observations for each
group. The `dplyr` package comes with two related functions that help with this.


If we need to use the number of observations in calculations, the `n()` function
is useful. For instance, if we wanted to get the standard error of the life
expectancy per continent:


~~~
gapminder %>%
    filter(year == 2002) %>%	
    group_by(continent) %>%
    summarize(se_pop = sd(lifeExp)/sqrt(n()))
~~~
{: .language-r}



~~~
# A tibble: 5 x 2
  continent    se_pop
      <chr>     <dbl>
1    Africa 1.3294078
2  Americas 0.9599411
3      Asia 1.4578299
4    Europe 0.5335146
5   Oceania 0.6300000
~~~
{: .output}

Although we could use the `group_by()`, `n()` and `summarize()` functions to calculate the number of observations in each group, `dplyr` provides the `count()` function which automatically groups the data, calculates the totals and then ungroups it. 

For instance, if we wanted to check the number of countries included in the
dataset for the year 2002, we can use:


~~~
gapminder %>%
    filter(year == 2002) %>%
    count(continent, sort = TRUE)
~~~
{: .language-r}



~~~
# A tibble: 5 x 2
  continent     n
      <chr> <int>
1    Africa    52
2      Asia    33
3    Europe    30
4  Americas    25
5   Oceania     2
~~~
{: .output}
We can optionally sort the results in descending order by adding `sort=TRUE`:



## Connect mutate with logical filtering: `ifelse()`

When creating new variables, we can hook this with a logical condition. A simple combination of 
`mutate()` and `ifelse()` facilitates filtering right where it is needed: in the moment of creating something new.
This easy-to-read statement is a fast and powerful way of discarding certain data (even though the overall dimension
of the tibble will not change) or for updating values depending on this given condition.

The `ifelse()` function takes three parameters.  The first it the logical test.  The second is the value to use if the test is TRUE for that observation, and the third is the value to use if the test is FALSE.


~~~
## keeping all data but "filtering" after a certain condition
# calculate GDP only for people with a life expectation above 50
gdp_pop_bycontinents_byyear_above25 <- gapminder %>%
    mutate(gdp_billion = ifelse(lifeExp > 50, gdpPercap * pop / 10^9, NA)) 
~~~
{: .language-r}


> ## Challenge 4
>
> (More complicated)
>
> Filter the data to only contain observations for the year 2002.
> Select two countries at random from each continent.  For each continent, calculate the 
> average life expectancy of the two countries.
> 
> Hints: 
> There are several steps in this challenge.   It will be easier to build your solution
> step by step, testing it after each step.  For example, first filter the data, then group,
> then take a sample, etc.
> The `dplyr` function `sample_n()` can be used to select a sample of rows.  
>
> The final tibble should contain the columns `continent` and the average life expectancy. You do
> *not* need to include the countries that were selected in it.
>
> > ## Solution to Challenge 4
> >
> >~~~
> >set.seed(171124)
> >lifeExp_2countries_bycontinents <- gapminder %>%
> >    filter(year==2002) %>%
> >    group_by(continent) %>%
> >    sample_n(2) %>%
> >    summarize(mean_lifeExp=mean(lifeExp)) 
> >print(lifeExp_2countries_bycontinents)
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 5 x 2
> >  continent mean_lifeExp
> >      <chr>        <dbl>
> >1    Africa      58.9130
> >2  Americas      74.1675
> >3      Asia      55.3465
> >4    Europe      77.2100
> >5   Oceania      79.7400
> >~~~
> >{: .output}
> >
> > Discussion: Do get the same answer as your neighbour?  What about if you run the command again? Do you get the
> > same answer as last time?
> > 
> > As we're sampling the rows at random we expect to get a different answer from our neighbour, and each time
> > we run the command.   You can set the random number _seed_ used by R using `set.seed(**a number**)`, as shown in the example above.
> > By default
> > R generates a seed when one is first requried using the process ID of R and the current time (i.e. essentially at
> > random). If you are using random numbers in your work you should set the seed at the start of your analysis
> > so that your results are reproducible. 
> {: .solution}
{: .challenge}


> ## Equivalent functions in base R
>
> In this course we've taught the tidyverse.  You are likely come across
> code written others in base R.  You can find a guide to some base R functions
> and their tidyverse equivalents [here](http://www.significantdigits.org/2017/10/switching-from-base-r-to-tidyverse/),
> which may be useful when reading their code.
>
{: .callout}
## Other great resources

* [Data Wrangling tutorial](https://suzan.rbind.io/categories/tutorial/) - an excellent four part tutorial covering selecting data, filtering data, summarising and transforming your data.
* [R for Data Science](http://r4ds.had.co.nz/)
* [Data Wrangling Cheat sheet](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)
* [Introduction to dplyr](https://cran.r-project.org/web/packages/dplyr/vignettes/dplyr.html) - this is the package vignette.  It can be viewed within R using `vignette(package="dplyr", "dplyr")`
* [Data wrangling with R and RStudio](https://www.rstudio.com/resources/webinars/data-wrangling-with-r-and-rstudio/)
