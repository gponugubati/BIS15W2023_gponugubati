---
title: "Lab 11 Homework"
author: "Gautam Ponugubati"
date: "2023-02-20"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---



## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your final lab report should be organized, clean, and run free from errors. Remember, you must remove the `#` for the included code chunks to run. Be sure to add your name to the author header above. For any included plots, make sure they are clearly labeled. You are free to use any plot type that you feel best communicates the results of your analysis.  

**In this homework, you should make use of the aesthetics you have learned. It's OK to be flashy!**

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean!  

## Load the libraries

```r
library(tidyverse)
library(janitor)
library(here)
library(naniar)
```

## Resources
The idea for this assignment came from [Rebecca Barter's](http://www.rebeccabarter.com/blog/2017-11-17-ggplot2_tutorial/) ggplot tutorial so if you get stuck this is a good place to have a look.  

## Gapminder
For this assignment, we are going to use the dataset [gapminder](https://cran.r-project.org/web/packages/gapminder/index.html). Gapminder includes information about economics, population, and life expectancy from countries all over the world. You will need to install it before use. This is the same data that we will use for midterm 2 so this is good practice.

```r
#install.packages("gapminder")
library("gapminder")
```

## Questions
The questions below are open-ended and have many possible solutions. Your approach should, where appropriate, include numerical summaries and visuals. Be creative; assume you are building an analysis that you would ultimately present to an audience of stakeholders. Feel free to try out different `geoms` if they more clearly present your results.  

**1. Use the function(s) of your choice to get an idea of the overall structure of the data frame, including its dimensions, column names, variable classes, etc. As part of this, determine how NA's are treated in the data.**  

```r
glimpse(gapminder) #There are no NAs
```

```
## Rows: 1,704
## Columns: 6
## $ country   <fct> "Afghanistan", "Afghanistan", "Afghanistan", "Afghanistan", …
## $ continent <fct> Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia, …
## $ year      <int> 1952, 1957, 1962, 1967, 1972, 1977, 1982, 1987, 1992, 1997, …
## $ lifeExp   <dbl> 28.801, 30.332, 31.997, 34.020, 36.088, 38.438, 39.854, 40.8…
## $ pop       <int> 8425333, 9240934, 10267083, 11537966, 13079460, 14880372, 12…
## $ gdpPercap <dbl> 779.4453, 820.8530, 853.1007, 836.1971, 739.9811, 786.1134, …
```

**2. Among the interesting variables in gapminder is life expectancy. How has global life expectancy changed between 1952 and 2007?**

```r
gapminder %>% 
  filter(year >= 1952 & year <= 2007) %>%
  group_by(year) %>%
  summarize(mean_lifeExp = mean(lifeExp)) %>% 
  ggplot(aes(x = year, y = mean_lifeExp))+
  geom_point()+
  geom_smooth()+
  labs(x = "Year", y = "Life Expectancy", title = "Trend in Mean Life Expectancy between 1952 and 2007")
```

```
## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'
```

![](lab11_hw_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

**3. How do the distributions of life expectancy compare for the years 1952 and 2007?**

```r
gapminder %>% 
  group_by(year) %>% 
  filter(year >= 1952 & year <= 2007) %>%
  ggplot(aes(x = year, y = lifeExp, group = year))+
  geom_boxplot(alpha = 0.6)+
 # facet_wrap(~year, scales = "free")+
  labs(x = "Year", y = "Life Expectancy", title = "Distribution in Life Expectancy between 1952 and 2007")
```

![](lab11_hw_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

**4. Your answer above doesn't tell the whole story since life expectancy varies by region. Make a summary that shows the min, mean, and max life expectancy by continent for all years represented in the data.**

```r
gapminder %>%
  group_by(year, continent) %>%
  summarize(min_lifeExp = min(lifeExp),
            mean_lifeExp = mean(lifeExp),
            max_lifeExp = max(lifeExp))
```

```
## `summarise()` has grouped output by 'year'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 60 × 5
## # Groups:   year [12]
##     year continent min_lifeExp mean_lifeExp max_lifeExp
##    <int> <fct>           <dbl>        <dbl>       <dbl>
##  1  1952 Africa           30           39.1        52.7
##  2  1952 Americas         37.6         53.3        68.8
##  3  1952 Asia             28.8         46.3        65.4
##  4  1952 Europe           43.6         64.4        72.7
##  5  1952 Oceania          69.1         69.3        69.4
##  6  1957 Africa           31.6         41.3        58.1
##  7  1957 Americas         40.7         56.0        70.0
##  8  1957 Asia             30.3         49.3        67.8
##  9  1957 Europe           48.1         66.7        73.5
## 10  1957 Oceania          70.3         70.3        70.3
## # … with 50 more rows
```

**5. How has life expectancy changed between 1952-2007 for each continent?**

```r
gapminder %>%
  group_by(year, continent) %>%
  filter(year >= 1952 & year <= 2007) %>% 
  summarize(mean_lifeExp = mean(lifeExp)) %>% 
  ggplot(aes(x = year, y = mean_lifeExp, fill = continent)) +
  geom_col(position = "dodge") +
  labs(x = "Year", y = "Mean Life Expectancy", fill = "Continent", title = 
         "Change in life expectancy for each continent between 1952 and 2007")
```

```
## `summarise()` has grouped output by 'year'. You can override using the
## `.groups` argument.
```

![](lab11_hw_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

**6. We are interested in the relationship between per capita GDP and life expectancy; i.e. does having more money help you live longer?**

```r
gapminder %>% 
  ggplot(aes(x = gdpPercap, y = lifeExp))+
  geom_point()+
  geom_smooth()+
  labs(x = "GDP per capita", y = "Life Expectancy", title = "Relationship between per capita GDP and life expectancy")
```

```
## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'
```

![](lab11_hw_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
#Ignoring the outliers, there is a positive relationship between GDP per capita and life expectancy, meaning having more money helps you live longer.
```

**7. Which countries have had the largest population growth since 1952?**

```r
gapminder %>% 
  select(country, year, pop) %>% 
  filter(year==1952 | year==2007) %>% 
  pivot_wider(names_from = year,
              names_prefix = "yr",
              values_from = pop) %>% 
  mutate(difference = yr2007 - yr1952) %>% 
  arrange(desc(difference))
```

```
## # A tibble: 142 × 4
##    country          yr1952     yr2007 difference
##    <fct>             <int>      <int>      <int>
##  1 China         556263527 1318683096  762419569
##  2 India         372000000 1110396331  738396331
##  3 United States 157553000  301139947  143586947
##  4 Indonesia      82052000  223547000  141495000
##  5 Brazil         56602560  190010647  133408087
##  6 Pakistan       41346560  169270617  127924057
##  7 Bangladesh     46886859  150448339  103561480
##  8 Nigeria        33119096  135031164  101912068
##  9 Mexico         30144317  108700891   78556574
## 10 Philippines    22438691   91077287   68638596
## # … with 132 more rows
```

```r
#China had the most population growth, followed by India, USA, Indonesia and Brazil
```

**8. Use your results from the question above to plot population growth for the top five countries since 1952.**

```r
options(scipen = 999)

gapminder %>% 
  filter(country == "China" | country == "India" | country == "United States" | country == "Indonesia" | country == "Brazil") %>% 
  ggplot(aes(x = year, y = pop, color = country))+
  geom_line()
```

![](lab11_hw_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

**9. How does per-capita GDP growth compare between these same five countries?**

```r
gapminder %>% 
  filter(country=="China" | country=="India" | country=="United States" | country=="Indonesia" | country=="Brazil") %>% 
  ggplot(aes(x = year, y = gdpPercap, color = country))+
  geom_line()+
  labs(x = "Year", y = "GDP per capita", title = "Per-capita GDP growth over time")
```

![](lab11_hw_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

**10. Make one plot of your choice that uses faceting!**

```r
gapminder %>%
  ggplot(aes(x = year, y = gdpPercap, fill = continent)) +
  geom_col() +
  facet_wrap(~ continent) +
  labs(x = "year", y = "GDP per capita", color = "Continent", title = "Trend in GDP per capita for each continent over time")
```

![](lab11_hw_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences. 
