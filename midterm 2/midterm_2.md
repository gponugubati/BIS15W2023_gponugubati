---
title: "BIS 15L Midterm 2"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---



## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your code should be organized, clean, and run free from errors. Remember, you must remove the `#` for any included code chunks to run. Be sure to add your name to the author header above.  

After the first 50 minutes, please upload your code (5 points). During the second 50 minutes, you may get help from each other- but no copy/paste. Upload the last version at the end of this time, but be sure to indicate it as final. If you finish early, you are free to leave.

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean! Use the tidyverse and pipes unless otherwise indicated. To receive full credit, all plots must have clearly labeled axes, a title, and consistent aesthetics. This exam is worth a total of 35 points. 

Please load the following libraries.

```r
library("tidyverse")
library("janitor")
library("naniar")
```

## Data
These data are from a study on surgical residents. The study was originally published by Sessier et al. “Operation Timing and 30-Day Mortality After Elective General Surgery”. Anesth Analg 2011; 113: 1423-8. The data were cleaned for instructional use by Amy S. Nowacki, “Surgery Timing Dataset”, TSHS Resources Portal (2016). Available at https://www.causeweb.org/tshs/surgery-timing/.

Descriptions of the variables and the study are included as pdf's in the data folder.  

Please run the following chunk to import the data.

```r
surgery <- read_csv("data/surgery.csv")
```

1. (2 points) Use the summary function(s) of your choice to explore the data and get an idea of its structure. Please also check for NA's.

```r
glimpse(surgery) #doesn't appear to have any kind of NAs
```

```
## Rows: 32,001
## Columns: 25
## $ ahrq_ccs            <chr> "<Other>", "<Other>", "<Other>", "<Other>", "<Othe…
## $ age                 <dbl> 67.8, 39.5, 56.5, 71.0, 56.3, 57.7, 56.6, 64.2, 66…
## $ gender              <chr> "M", "F", "F", "M", "M", "F", "M", "F", "M", "F", …
## $ race                <chr> "Caucasian", "Caucasian", "Caucasian", "Caucasian"…
## $ asa_status          <chr> "I-II", "I-II", "I-II", "III", "I-II", "I-II", "IV…
## $ bmi                 <dbl> 28.04, 37.85, 19.56, 32.22, 24.32, 40.30, 64.57, 4…
## $ baseline_cancer     <chr> "No", "No", "No", "No", "Yes", "No", "No", "No", "…
## $ baseline_cvd        <chr> "Yes", "Yes", "No", "Yes", "No", "Yes", "Yes", "Ye…
## $ baseline_dementia   <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ baseline_diabetes   <chr> "No", "No", "No", "No", "No", "No", "Yes", "No", "…
## $ baseline_digestive  <chr> "Yes", "No", "No", "No", "No", "No", "No", "No", "…
## $ baseline_osteoart   <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ baseline_psych      <chr> "No", "No", "No", "No", "No", "Yes", "No", "No", "…
## $ baseline_pulmonary  <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ baseline_charlson   <dbl> 0, 0, 0, 0, 0, 0, 2, 0, 1, 2, 0, 1, 0, 0, 0, 0, 0,…
## $ mortality_rsi       <dbl> -0.63, -0.63, -0.49, -1.38, 0.00, -0.77, -0.36, -0…
## $ complication_rsi    <dbl> -0.26, -0.26, 0.00, -1.15, 0.00, -0.84, -1.34, 0.0…
## $ ccsmort30rate       <dbl> 0.0042508, 0.0042508, 0.0042508, 0.0042508, 0.0042…
## $ ccscomplicationrate <dbl> 0.07226355, 0.07226355, 0.07226355, 0.07226355, 0.…
## $ hour                <dbl> 9.03, 18.48, 7.88, 8.80, 12.20, 7.67, 9.53, 7.52, …
## $ dow                 <chr> "Mon", "Wed", "Fri", "Wed", "Thu", "Thu", "Tue", "…
## $ month               <chr> "Nov", "Sep", "Aug", "Jun", "Aug", "Dec", "Apr", "…
## $ moonphase           <chr> "Full Moon", "New Moon", "Full Moon", "Last Quarte…
## $ mort30              <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ complication        <chr> "No", "No", "No", "No", "No", "No", "No", "Yes", "…
```

```r
anyNA(surgery) #THERE ARE NAs
```

```
## [1] TRUE
```

2. (3 points) Let's explore the participants in the study. Show a count of participants by race AND make a plot that visually represents your output.

```r
surgery %>% 
  count(race) 
```

```
## # A tibble: 4 × 2
##   race                 n
##   <chr>            <int>
## 1 African American  3790
## 2 Caucasian        26488
## 3 Other             1243
## 4 <NA>               480
```

```r
surgery %>% #plot
  ggplot(aes(x = race, fill = race))+
  theme_linedraw()+
  geom_bar()+
  labs(x = "Race", y = "Number of Individuals", title = "Distribution of the number of individuals by race")
```

![](midterm_2_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

3. (2 points) What is the mean age of participants by gender? (hint: please provide a number for each) Since only three participants do not have gender indicated, remove these participants from the data.

```r
surgery %>% 
  filter(age != "NA" & gender != "NA") %>% 
  group_by(gender) %>% 
  summarize(mean_age = mean(age))
```

```
## # A tibble: 2 × 2
##   gender mean_age
##   <chr>     <dbl>
## 1 F          56.7
## 2 M          58.8
```

4. (3 points) Make a plot that shows the range of age associated with gender.

```r
surgery %>% 
  filter(age != "NA" & gender != "NA") %>% 
  group_by(gender) %>%
  ggplot(aes(x = gender, y = age, fill = gender))+
  geom_boxplot()+
  theme_linedraw()+
  labs(x = "Gender", y = "Age", title = "Range of distribution of age by Gender")
```

![](midterm_2_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

5. (2 points) How healthy are the participants? The variable `asa_status` is an evaluation of patient physical status prior to surgery. Lower numbers indicate fewer comorbidities (presence of two or more diseases or medical conditions in a patient). Make a plot that compares the number of `asa_status` I-II, III, and IV-V.

```r
surgery %>% 
  ggplot(aes(x = asa_status, fill = asa_status))+
  geom_bar()+
  theme_linedraw()+
  labs(x = "ASA status", y = "Number of individuals", title = "Distribution of the number of individuals by ASA status")
```

![](midterm_2_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

6. (3 points) Create a plot that displays the distribution of body mass index for each `asa_status` as a probability distribution- not a histogram. (hint: use faceting!)

```r
# names(surgery) body mass index = BMI
surgery %>% 
  ggplot(aes(y = bmi))+
  theme_linedraw()+
  facet_wrap(asa_status~.)+
  geom_density(fill = "skyblue1")+
  coord_flip()+
  labs(x = "Percentage of population", y = "Body Mass Index", title = "Distribution of body mass index by ASA status")
```

```
## Warning: Removed 3290 rows containing non-finite values (`stat_density()`).
```

![](midterm_2_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

The variable `ccsmort30rate` is a measure of the overall 30-day mortality rate associated with each type of operation. The variable `ccscomplicationrate` is a measure of the 30-day in-hospital complication rate. The variable `ahrq_ccs` lists each type of operation.  

7. (4 points) What are the 5 procedures associated with highest risk of 30-day mortality AND how do they compare with the 5 procedures with highest risk of complication? (hint: no need for a plot here)

```r
surgery %>% 
  select(ahrq_ccs, ccsmort30rate) %>% 
  group_by(ahrq_ccs) %>% 
  summarize(mean_ccsmort30ate = mean(ccsmort30rate)) %>% 
  arrange(desc(mean_ccsmort30ate)) %>% 
  slice(1:5)
```

```
## # A tibble: 5 × 2
##   ahrq_ccs                                             mean_ccsmort30ate
##   <chr>                                                            <dbl>
## 1 Colorectal resection                                           0.0167 
## 2 Small bowel resection                                          0.0129 
## 3 Gastrectomy; partial and total                                 0.0127 
## 4 Endoscopy and endoscopic biopsy of the urinary tract           0.00811
## 5 Spinal fusion                                                  0.00742
```

```r
#The 5 procedures with the highest 30 day mortality rate
```

```r
surgery %>% 
  select(ahrq_ccs, ccscomplicationrate) %>% 
  group_by(ahrq_ccs) %>% 
  summarize(mean_ccscomplicationrate = mean(ccscomplicationrate)) %>% 
  arrange(desc(mean_ccscomplicationrate)) %>% 
  slice(1:5)
```

```
## # A tibble: 5 × 2
##   ahrq_ccs                         mean_ccscomplicationrate
##   <chr>                                               <dbl>
## 1 Small bowel resection                               0.466
## 2 Colorectal resection                                0.312
## 3 Nephrectomy; partial or complete                    0.197
## 4 Gastrectomy; partial and total                      0.190
## 5 Spinal fusion                                       0.183
```

```r
#The 5 procedures with the highest complication rate
```

8. (3 points) Make a plot that compares the `ccsmort30rate` for all listed `ahrq_ccs` procedures.

```r
surgery %>% 
  ggplot(aes(x = ahrq_ccs, fill = ccsmort30rate))+
  theme_linedraw()+
  theme(axis.text.x = element_text(angle = 60, hjust = 1))+
  geom_bar()
```

![](midterm_2_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

9. (4 points) When is the best month to have surgery? Make a chart that shows the 30-day mortality and complications for the patients by month. `mort30` is the variable that shows whether or not a patient survived 30 days post-operation.

```r
surgery %>% 
  tabyl(month, mort30) #Best month to have surgery is August (3168 people did not die after 30 days and had some of the lowest number of deaths (9))
```

```
##  month   No Yes
##    Apr 2686  12
##    Aug 3168   9
##    Dec 1835   4
##    Feb 2489  17
##    Jan 2651  19
##    Jul 2313  12
##    Jun 2980  14
##    Mar 2685  12
##    May 2644  10
##    Nov 2539   5
##    Oct 2681   8
##    Sep 3192  16
```

```r
surgery %>% 
  tabyl(month, complication) #September (Most number of people with no complications)
```

```
##  month   No Yes
##    Apr 2377 321
##    Aug 2715 462
##    Dec 1602 237
##    Feb 2163 343
##    Jan 2263 407
##    Jul 2024 301
##    Jun 2584 410
##    Mar 2373 324
##    May 2321 333
##    Nov 2219 325
##    Oct 2312 377
##    Sep 2784 424
```

10. (4 points) Make a plot that visualizes the chart from question #9. Make sure that the months are on the x-axis. Do a search online and figure out how to order the months Jan-Dec.

```r
surgery %>% 
  ggplot(aes(x = month, fill = mort30))+
  geom_bar()+
  coord_flip()+
  theme_linedraw()+
  labs(x = "Number of people that died after 30 days", y = "Month", title = "30 Day Mortality rate by month")
```

![](midterm_2_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

```r
surgery %>% 
  ggplot(aes(x = month, fill = complication))+
  geom_bar()+
  coord_flip()+
  theme_linedraw()+
  labs(x = "Number of people that had complications after 30 days", y = "Month", title = "30 Day Complication rate by month")
```

![](midterm_2_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

Please provide the names of the students you have worked with with during the exam:

Kyle De Mariano

Please be 100% sure your exam is saved, knitted, and pushed to your github repository. No need to submit a link on canvas, we will find your exam in your repository.
