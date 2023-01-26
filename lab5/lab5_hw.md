---
title: "dplyr Superhero"
date: "2023-01-26"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
---

## Load the tidyverse

```r
library("tidyverse")
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.4.0      ✔ purrr   1.0.0 
## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
## ✔ tidyr   1.2.1      ✔ stringr 1.5.0 
## ✔ readr   2.1.3      ✔ forcats 0.5.2 
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

## Load the superhero data
These are data taken from comic books and assembled by fans. The include a good mix of categorical and continuous data.  Data taken from: https://www.kaggle.com/claudiodavi/superhero-set  

Check out the way I am loading these data. If I know there are NAs, I can take care of them at the beginning. But, we should do this very cautiously. At times it is better to keep the original columns and data intact.  

```r
superhero_info <- readr::read_csv("data/heroes_information.csv", na = c("", "-99", "-"))
```

```
## Rows: 734 Columns: 10
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (8): name, Gender, Eye color, Race, Hair color, Publisher, Skin color, A...
## dbl (2): Height, Weight
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
superhero_powers <- readr::read_csv("data/super_hero_powers.csv", na = c("", "-99", "-"))
```

```
## Rows: 667 Columns: 168
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr   (1): hero_names
## lgl (167): Agility, Accelerated Healing, Lantern Power Ring, Dimensional Awa...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Data tidy
1. Some of the names used in the `superhero_info` data are problematic so you should rename them here.  

```r
names(superhero_info)
```

```
##  [1] "name"       "Gender"     "Eye color"  "Race"       "Hair color"
##  [6] "Height"     "Publisher"  "Skin color" "Alignment"  "Weight"
```

```r
superhero_gotidy <- rename(superhero_info, gender = "Gender", eye_color = "Eye color", race = "Race", hair_color = "Hair color", height = "Height", publisher = "Publisher", skin_color = "Skin color", alignment = "Alignment", weight = "Weight")
#names(superhero_gotidy)
```

Yikes! `superhero_powers` has a lot of variables that are poorly named. We need some R superpowers...

```r
head(superhero_powers)
```

```
## # A tibble: 6 × 168
##   hero_…¹ Agility Accel…² Lante…³ Dimen…⁴ Cold …⁵ Durab…⁶ Stealth Energ…⁷ Flight
##   <chr>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl> 
## 1 3-D Man TRUE    FALSE   FALSE   FALSE   FALSE   FALSE   FALSE   FALSE   FALSE 
## 2 A-Bomb  FALSE   TRUE    FALSE   FALSE   FALSE   TRUE    FALSE   FALSE   FALSE 
## 3 Abe Sa… TRUE    TRUE    FALSE   FALSE   TRUE    TRUE    FALSE   FALSE   FALSE 
## 4 Abin S… FALSE   FALSE   TRUE    FALSE   FALSE   FALSE   FALSE   FALSE   FALSE 
## 5 Abomin… FALSE   TRUE    FALSE   FALSE   FALSE   FALSE   FALSE   FALSE   FALSE 
## 6 Abraxas FALSE   FALSE   FALSE   TRUE    FALSE   FALSE   FALSE   FALSE   TRUE  
## # … with 158 more variables: `Danger Sense` <lgl>,
## #   `Underwater breathing` <lgl>, Marksmanship <lgl>, `Weapons Master` <lgl>,
## #   `Power Augmentation` <lgl>, `Animal Attributes` <lgl>, Longevity <lgl>,
## #   Intelligence <lgl>, `Super Strength` <lgl>, Cryokinesis <lgl>,
## #   Telepathy <lgl>, `Energy Armor` <lgl>, `Energy Blasts` <lgl>,
## #   Duplication <lgl>, `Size Changing` <lgl>, `Density Control` <lgl>,
## #   Stamina <lgl>, `Astral Travel` <lgl>, `Audio Control` <lgl>, …
```

## `janitor`
The [janitor](https://garthtarr.github.io/meatR/janitor.html) package is your friend. Make sure to install it and then load the library.  

```r
#install.packages("janitor")
library("janitor")
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

The `clean_names` function takes care of everything in one line! Now that's a superpower!

```r
superhero_powers <- janitor::clean_names(superhero_powers)
```

## `tabyl`
The `janitor` package has many awesome functions that we will explore. Here is its version of `table` which not only produces counts but also percentages. Very handy! Let's use it to explore the proportion of good guys and bad guys in the `superhero_info` data.  


```r
tabyl(superhero_gotidy, alignment)
```

```
##  alignment   n     percent valid_percent
##        bad 207 0.282016349    0.28473177
##       good 496 0.675749319    0.68225585
##    neutral  24 0.032697548    0.03301238
##       <NA>   7 0.009536785            NA
```

2. Notice that we have some neutral superheros! Who are they?

```r
superhero_gotidy %>% 
  filter(alignment == "neutral")
```

```
## # A tibble: 24 × 10
##    name       gender eye_c…¹ race  hair_…² height publi…³ skin_…⁴ align…⁵ weight
##    <chr>      <chr>  <chr>   <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
##  1 Bizarro    Male   black   Biza… Black      191 DC Com… white   neutral    155
##  2 Black Fla… Male   <NA>    God … <NA>        NA DC Com… <NA>    neutral     NA
##  3 Captain C… Male   brown   Human Brown       NA DC Com… <NA>    neutral     NA
##  4 Copycat    Female red     Muta… White      183 Marvel… blue    neutral     67
##  5 Deadpool   Male   brown   Muta… No Hair    188 Marvel… <NA>    neutral     95
##  6 Deathstro… Male   blue    Human White      193 DC Com… <NA>    neutral    101
##  7 Etrigan    Male   red     Demon No Hair    193 DC Com… yellow  neutral    203
##  8 Galactus   Male   black   Cosm… Black      876 Marvel… <NA>    neutral     16
##  9 Gladiator  Male   blue    Stro… Blue       198 Marvel… purple  neutral    268
## 10 Indigo     Female <NA>    Alien Purple      NA DC Com… <NA>    neutral     NA
## # … with 14 more rows, and abbreviated variable names ¹​eye_color, ²​hair_color,
## #   ³​publisher, ⁴​skin_color, ⁵​alignment
```

## `superhero_info`
3. Let's say we are only interested in the variables name, alignment, and "race". How would you isolate these variables from `superhero_info`?

```r
superhero_gotidy %>% 
  select(name, alignment, race)
```

```
## # A tibble: 734 × 3
##    name          alignment race             
##    <chr>         <chr>     <chr>            
##  1 A-Bomb        good      Human            
##  2 Abe Sapien    good      Icthyo Sapien    
##  3 Abin Sur      good      Ungaran          
##  4 Abomination   bad       Human / Radiation
##  5 Abraxas       bad       Cosmic Entity    
##  6 Absorbing Man bad       Human            
##  7 Adam Monroe   good      <NA>             
##  8 Adam Strange  good      Human            
##  9 Agent 13      good      <NA>             
## 10 Agent Bob     good      Human            
## # … with 724 more rows
```

## Not Human
4. List all of the superheros that are not human.

```r
superhero_gotidy %>% 
  filter(race != "Human")
```

```
## # A tibble: 222 × 10
##    name       gender eye_c…¹ race  hair_…² height publi…³ skin_…⁴ align…⁵ weight
##    <chr>      <chr>  <chr>   <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
##  1 Abe Sapien Male   blue    Icth… No Hair    191 Dark H… blue    good        65
##  2 Abin Sur   Male   blue    Unga… No Hair    185 DC Com… red     good        90
##  3 Abominati… Male   green   Huma… No Hair    203 Marvel… <NA>    bad        441
##  4 Abraxas    Male   blue    Cosm… Black       NA Marvel… <NA>    bad         NA
##  5 Ajax       Male   brown   Cybo… Black      193 Marvel… <NA>    bad         90
##  6 Alien      Male   <NA>    Xeno… No Hair    244 Dark H… black   bad        169
##  7 Amazo      Male   red     Andr… <NA>       257 DC Com… <NA>    bad        173
##  8 Angel      Male   <NA>    Vamp… <NA>        NA Dark H… <NA>    good        NA
##  9 Angel Dust Female yellow  Muta… Black      165 Marvel… <NA>    good        57
## 10 Anti-Moni… Male   yellow  God … No Hair     61 DC Com… <NA>    bad         NA
## # … with 212 more rows, and abbreviated variable names ¹​eye_color, ²​hair_color,
## #   ³​publisher, ⁴​skin_color, ⁵​alignment
```

## Good and Evil
5. Let's make two different data frames, one focused on the "good guys" and another focused on the "bad guys".

```r
good_guys <- filter(superhero_gotidy, alignment == "good")
```


```r
bad_guys <- filter(superhero_gotidy, alignment == "bad")
```

6. For the good guys, use the `tabyl` function to summarize their "race".

```r
tabyl(superhero_gotidy, race)
```

```
##                race   n     percent valid_percent
##               Alien   7 0.009536785   0.016279070
##               Alpha   5 0.006811989   0.011627907
##              Amazon   2 0.002724796   0.004651163
##             Android   9 0.012261580   0.020930233
##              Animal   4 0.005449591   0.009302326
##           Asgardian   5 0.006811989   0.011627907
##           Atlantean   5 0.006811989   0.011627907
##             Bizarro   1 0.001362398   0.002325581
##          Bolovaxian   1 0.001362398   0.002325581
##               Clone   1 0.001362398   0.002325581
##       Cosmic Entity   4 0.005449591   0.009302326
##              Cyborg  11 0.014986376   0.025581395
##            Czarnian   1 0.001362398   0.002325581
##  Dathomirian Zabrak   1 0.001362398   0.002325581
##            Demi-God   2 0.002724796   0.004651163
##               Demon   6 0.008174387   0.013953488
##             Eternal   2 0.002724796   0.004651163
##      Flora Colossus   1 0.001362398   0.002325581
##         Frost Giant   2 0.002724796   0.004651163
##       God / Eternal  14 0.019073569   0.032558140
##             Gorilla   1 0.001362398   0.002325581
##              Gungan   1 0.001362398   0.002325581
##               Human 208 0.283378747   0.483720930
##          Human-Kree   2 0.002724796   0.004651163
##       Human-Spartoi   1 0.001362398   0.002325581
##        Human-Vulcan   1 0.001362398   0.002325581
##     Human-Vuldarian   1 0.001362398   0.002325581
##     Human / Altered   3 0.004087193   0.006976744
##       Human / Clone   1 0.001362398   0.002325581
##      Human / Cosmic   2 0.002724796   0.004651163
##   Human / Radiation  11 0.014986376   0.025581395
##       Icthyo Sapien   1 0.001362398   0.002325581
##             Inhuman   4 0.005449591   0.009302326
##               Kaiju   1 0.001362398   0.002325581
##     Kakarantharaian   1 0.001362398   0.002325581
##           Korugaran   1 0.001362398   0.002325581
##          Kryptonian   7 0.009536785   0.016279070
##           Luphomoid   1 0.001362398   0.002325581
##               Maiar   1 0.001362398   0.002325581
##             Martian   1 0.001362398   0.002325581
##           Metahuman   2 0.002724796   0.004651163
##              Mutant  63 0.085831063   0.146511628
##      Mutant / Clone   1 0.001362398   0.002325581
##             New God   3 0.004087193   0.006976744
##            Neyaphem   1 0.001362398   0.002325581
##           Parademon   1 0.001362398   0.002325581
##              Planet   1 0.001362398   0.002325581
##              Rodian   1 0.001362398   0.002325581
##              Saiyan   2 0.002724796   0.004651163
##             Spartoi   1 0.001362398   0.002325581
##           Strontian   1 0.001362398   0.002325581
##            Symbiote   9 0.012261580   0.020930233
##            Talokite   1 0.001362398   0.002325581
##          Tamaranean   1 0.001362398   0.002325581
##             Ungaran   1 0.001362398   0.002325581
##             Vampire   2 0.002724796   0.004651163
##     Xenomorph XX121   1 0.001362398   0.002325581
##              Yautja   1 0.001362398   0.002325581
##      Yoda's species   1 0.001362398   0.002325581
##       Zen-Whoberian   1 0.001362398   0.002325581
##              Zombie   1 0.001362398   0.002325581
##                <NA> 304 0.414168937            NA
```

7. Among the good guys, Who are the Asgardians?

```r
good_guys %>% 
  filter(race == "Asgardian")
```

```
## # A tibble: 3 × 10
##   name      gender eye_color race  hair_…¹ height publi…² skin_…³ align…⁴ weight
##   <chr>     <chr>  <chr>     <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
## 1 Sif       Female blue      Asga… Black      188 Marvel… <NA>    good       191
## 2 Thor      Male   blue      Asga… Blond      198 Marvel… <NA>    good       288
## 3 Thor Girl Female blue      Asga… Blond      175 Marvel… <NA>    good       143
## # … with abbreviated variable names ¹​hair_color, ²​publisher, ³​skin_color,
## #   ⁴​alignment
```

8. Among the bad guys, who are the male humans over 200 inches in height?

```r
bad_guys %>% 
  filter(height >= "200")
```

```
## # A tibble: 28 × 10
##    name       gender eye_c…¹ race  hair_…² height publi…³ skin_…⁴ align…⁵ weight
##    <chr>      <chr>  <chr>   <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
##  1 Abominati… Male   green   Huma… No Hair  203   Marvel… <NA>    bad        441
##  2 Alien      Male   <NA>    Xeno… No Hair  244   Dark H… black   bad        169
##  3 Amazo      Male   red     Andr… <NA>     257   DC Com… <NA>    bad        173
##  4 Anti-Moni… Male   yellow  God … No Hair   61   DC Com… <NA>    bad         NA
##  5 Apocalypse Male   red     Muta… Black    213   Marvel… grey    bad        135
##  6 Bane       Male   <NA>    Human <NA>     203   DC Com… <NA>    bad        180
##  7 Bloodaxe   Female blue    Human Brown    218   Marvel… <NA>    bad        495
##  8 Bloodwrai… Male   white   <NA>  No Hair   30.5 Marvel… <NA>    bad         NA
##  9 Darkseid   Male   red     New … No Hair  267   DC Com… grey    bad        817
## 10 Doctor Do… Male   brown   Human Brown    201   Marvel… <NA>    bad        187
## # … with 18 more rows, and abbreviated variable names ¹​eye_color, ²​hair_color,
## #   ³​publisher, ⁴​skin_color, ⁵​alignment
```

9. OK, so are there more good guys or bad guys that are bald (personal interest)?

There are more good guys that are bald (37) than bad guys that are bald (35)


```r
tabyl(good_guys$hair_color) #37 No hair (bald)
```

```
##  good_guys$hair_color   n     percent valid_percent
##                Auburn  10 0.020161290   0.026178010
##                 black   3 0.006048387   0.007853403
##                 Black 108 0.217741935   0.282722513
##                 blond   2 0.004032258   0.005235602
##                 Blond  85 0.171370968   0.222513089
##                  Blue   1 0.002016129   0.002617801
##                 Brown  55 0.110887097   0.143979058
##         Brown / Black   1 0.002016129   0.002617801
##         Brown / White   4 0.008064516   0.010471204
##                 Green   7 0.014112903   0.018324607
##                  Grey   2 0.004032258   0.005235602
##                Indigo   1 0.002016129   0.002617801
##               Magenta   1 0.002016129   0.002617801
##               No Hair  37 0.074596774   0.096858639
##                Orange   2 0.004032258   0.005235602
##        Orange / White   1 0.002016129   0.002617801
##                  Pink   1 0.002016129   0.002617801
##                Purple   1 0.002016129   0.002617801
##                   Red  40 0.080645161   0.104712042
##           Red / White   1 0.002016129   0.002617801
##                Silver   3 0.006048387   0.007853403
##      Strawberry Blond   4 0.008064516   0.010471204
##                 White  10 0.020161290   0.026178010
##                Yellow   2 0.004032258   0.005235602
##                  <NA> 114 0.229838710            NA
```

```r
tabyl(bad_guys$hair_color) #35 No hair (bald)
```

```
##  bad_guys$hair_color  n     percent valid_percent
##               Auburn  3 0.014492754   0.019480519
##                Black 42 0.202898551   0.272727273
##         Black / Blue  1 0.004830918   0.006493506
##                blond  1 0.004830918   0.006493506
##                Blond 11 0.053140097   0.071428571
##                 Blue  1 0.004830918   0.006493506
##                Brown 27 0.130434783   0.175324675
##               Brownn  1 0.004830918   0.006493506
##                 Gold  1 0.004830918   0.006493506
##                Green  1 0.004830918   0.006493506
##                 Grey  3 0.014492754   0.019480519
##              No Hair 35 0.169082126   0.227272727
##               Purple  3 0.014492754   0.019480519
##                  Red  9 0.043478261   0.058441558
##           Red / Grey  1 0.004830918   0.006493506
##         Red / Orange  1 0.004830918   0.006493506
##     Strawberry Blond  3 0.014492754   0.019480519
##                White 10 0.048309179   0.064935065
##                 <NA> 53 0.256038647            NA
```

10. Let's explore who the really "big" superheros are. In the `superhero_info` data, which have a height over 200 or weight greater than or equal to 450?

```r
superhero_gotidy %>% 
  filter(height >= 200 | weight >= 450)
```

```
## # A tibble: 60 × 10
##    name       gender eye_c…¹ race  hair_…² height publi…³ skin_…⁴ align…⁵ weight
##    <chr>      <chr>  <chr>   <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
##  1 A-Bomb     Male   yellow  Human No Hair    203 Marvel… <NA>    good       441
##  2 Abominati… Male   green   Huma… No Hair    203 Marvel… <NA>    bad        441
##  3 Alien      Male   <NA>    Xeno… No Hair    244 Dark H… black   bad        169
##  4 Amazo      Male   red     Andr… <NA>       257 DC Com… <NA>    bad        173
##  5 Ant-Man    Male   blue    Human Blond      211 Marvel… <NA>    good       122
##  6 Anti-Venom Male   blue    Symb… Blond      229 Marvel… <NA>    <NA>       358
##  7 Apocalypse Male   red     Muta… Black      213 Marvel… grey    bad        135
##  8 Bane       Male   <NA>    Human <NA>       203 DC Com… <NA>    bad        180
##  9 Beta Ray … Male   <NA>    <NA>  No Hair    201 Marvel… <NA>    good       216
## 10 Bloodaxe   Female blue    Human Brown      218 Marvel… <NA>    bad        495
## # … with 50 more rows, and abbreviated variable names ¹​eye_color, ²​hair_color,
## #   ³​publisher, ⁴​skin_color, ⁵​alignment
```

11. Just to be clear on the `|` operator,  have a look at the superheros over 300 in height...

```r
superhero_gotidy %>% 
  filter(height >= 300)
```

```
## # A tibble: 8 × 10
##   name        gender eye_c…¹ race  hair_…² height publi…³ skin_…⁴ align…⁵ weight
##   <chr>       <chr>  <chr>   <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
## 1 Fin Fang F… Male   red     Kaka… No Hair   975  Marvel… green   good        18
## 2 Galactus    Male   black   Cosm… Black     876  Marvel… <NA>    neutral     16
## 3 Groot       Male   yellow  Flor… <NA>      701  Marvel… <NA>    good         4
## 4 MODOK       Male   white   Cybo… Brownn    366  Marvel… <NA>    bad        338
## 5 Onslaught   Male   red     Muta… No Hair   305  Marvel… <NA>    bad        405
## 6 Sasquatch   Male   red     <NA>  Orange    305  Marvel… <NA>    good       900
## 7 Wolfsbane   Female green   <NA>  Auburn    366  Marvel… <NA>    good       473
## 8 Ymir        Male   white   Fros… No Hair   305. Marvel… white   good        NA
## # … with abbreviated variable names ¹​eye_color, ²​hair_color, ³​publisher,
## #   ⁴​skin_color, ⁵​alignment
```

12. ...and the superheros over 450 in weight. Bonus question! Why do we not have 16 rows in question #10?

We don't have 16 rows in question 10 because it doesn't include superheroes below 450 in weight and 200 in height, while the other questions dompletely ignore one of the other conditions.


```r
superhero_gotidy %>% 
  filter(weight >= 450)
```

```
## # A tibble: 8 × 10
##   name       gender eye_co…¹ race  hair_…² height publi…³ skin_…⁴ align…⁵ weight
##   <chr>      <chr>  <chr>    <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
## 1 Bloodaxe   Female blue     Human Brown    218   Marvel… <NA>    bad        495
## 2 Darkseid   Male   red      New … No Hair  267   DC Com… grey    bad        817
## 3 Giganta    Female green    <NA>  Red       62.5 DC Com… <NA>    bad        630
## 4 Hulk       Male   green    Huma… Green    244   Marvel… green   good       630
## 5 Juggernaut Male   blue     Human Red      287   Marvel… <NA>    neutral    855
## 6 Red Hulk   Male   yellow   Huma… Black    213   Marvel… red     neutral    630
## 7 Sasquatch  Male   red      <NA>  Orange   305   Marvel… <NA>    good       900
## 8 Wolfsbane  Female green    <NA>  Auburn   366   Marvel… <NA>    good       473
## # … with abbreviated variable names ¹​eye_color, ²​hair_color, ³​publisher,
## #   ⁴​skin_color, ⁵​alignment
```

## Height to Weight Ratio
13. It's easy to be strong when you are heavy and tall, but who is heavy and short? Which superheros have the highest height to weight ratio?

Groot has the highest height-weight ratio (157.25), followed by Galactus, Fin Fang Foom, etc. 


```r
superhero_gotidy %>% 
  mutate(height_weight_ratio=height/weight)%>% 
  arrange(desc(height_weight_ratio))
```

```
## # A tibble: 734 × 11
##    name       gender eye_c…¹ race  hair_…² height publi…³ skin_…⁴ align…⁵ weight
##    <chr>      <chr>  <chr>   <chr> <chr>    <dbl> <chr>   <chr>   <chr>    <dbl>
##  1 Groot      Male   yellow  Flor… <NA>       701 Marvel… <NA>    good         4
##  2 Galactus   Male   black   Cosm… Black      876 Marvel… <NA>    neutral     16
##  3 Fin Fang … Male   red     Kaka… No Hair    975 Marvel… green   good        18
##  4 Longshot   Male   blue    Human Blond      188 Marvel… <NA>    good        36
##  5 Jack-Jack  Male   blue    Human Brown       71 Dark H… <NA>    good        14
##  6 Rocket Ra… Male   brown   Anim… Brown      122 Marvel… <NA>    good        25
##  7 Dash       Male   blue    Human Blond      122 Dark H… <NA>    good        27
##  8 Howard th… Male   brown   <NA>  Yellow      79 Marvel… <NA>    good        18
##  9 Swarm      Male   yellow  Muta… No Hair    196 Marvel… yellow  bad         47
## 10 Yoda       Male   brown   Yoda… White       66 George… green   good        17
## # … with 724 more rows, 1 more variable: height_weight_ratio <dbl>, and
## #   abbreviated variable names ¹​eye_color, ²​hair_color, ³​publisher,
## #   ⁴​skin_color, ⁵​alignment
```

## `superhero_powers`
Have a quick look at the `superhero_powers` data frame.  

```r
glimpse(superhero_powers)
```

```
## Rows: 667
## Columns: 168
## $ hero_names                   <chr> "3-D Man", "A-Bomb", "Abe Sapien", "Abin …
## $ agility                      <lgl> TRUE, FALSE, TRUE, FALSE, FALSE, FALSE, F…
## $ accelerated_healing          <lgl> FALSE, TRUE, TRUE, FALSE, TRUE, FALSE, FA…
## $ lantern_power_ring           <lgl> FALSE, FALSE, FALSE, TRUE, FALSE, FALSE, …
## $ dimensional_awareness        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ cold_resistance              <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ durability                   <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, T…
## $ stealth                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ flight                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ danger_sense                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ underwater_breathing         <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ marksmanship                 <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ weapons_master               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ power_augmentation           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animal_attributes            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ longevity                    <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, F…
## $ intelligence                 <lgl> FALSE, FALSE, TRUE, FALSE, TRUE, TRUE, FA…
## $ super_strength               <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, TRUE, TRUE…
## $ cryokinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ telepathy                    <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ energy_armor                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_blasts                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ duplication                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ size_changing                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ density_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ stamina                      <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, FALSE, FAL…
## $ astral_travel                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ audio_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ dexterity                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnitrix                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ super_speed                  <lgl> TRUE, FALSE, FALSE, FALSE, TRUE, TRUE, FA…
## $ possession                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animal_oriented_powers       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ weapon_based_powers          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ electrokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ darkforce_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ death_touch                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ teleportation                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ enhanced_senses              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ telekinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_beams                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ magic                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ hyperkinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ jump                         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ clairvoyance                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ dimensional_travel           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ power_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ shapeshifting                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ peak_human_condition         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ immortality                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, TRUE, F…
## $ camouflage                   <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALSE, …
## $ element_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ phasing                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ astral_projection            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ electrical_transport         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ fire_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ projection                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ summoning                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_memory              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ reflexes                     <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ invulnerability              <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, T…
## $ energy_constructs            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ force_fields                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ self_sustenance              <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALSE, …
## $ anti_gravity                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ empathy                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_nullifier              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ radiation_control            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ psionic_powers               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ elasticity                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ substance_secretion          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ elemental_transmogrification <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ technopath_cyberpath         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ photographic_reflexes        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ seismic_power                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animation                    <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALSE, …
## $ precognition                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ mind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ fire_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_absorption             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_hearing             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ nova_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ insanity                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ hypnokinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animal_control               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ natural_armor                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ intangibility                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_sight               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ molecular_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ heat_generation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ adaptation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ gliding                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_suit                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ mind_blast                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ probability_manipulation     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ gravity_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ regeneration                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ light_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ echolocation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ levitation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ toxin_and_disease_control    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ banish                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_manipulation          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ heat_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ natural_weapons              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ time_travel                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_smell               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ illusions                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ thirstokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ hair_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ illumination                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnipotent                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ cloaking                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ changing_armor               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_cosmic                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ biokinesis                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ water_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ radiation_immunity           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_telescopic            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ toxin_and_disease_resistance <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ spatial_awareness            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_resistance            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ telepathy_resistance         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ molecular_combustion         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnilingualism               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ portal_creation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ magnetism                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ mind_control_resistance      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ plant_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ sonar                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ sonic_scream                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ time_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_touch               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ magic_resistance             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ invisibility                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ sub_mariner                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ radiation_absorption         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ intuitive_aptitude           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_microscopic           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ melting                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ wind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ super_breath                 <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALSE, …
## $ wallcrawling                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_night                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_infrared              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ grim_reaping                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ matter_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ the_force                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ resurrection                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ terrakinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_heat                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vitakinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ radar_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ qwardian_power_ring          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ weather_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_x_ray                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_thermal               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ web_creation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ reality_warping              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ odin_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ symbiote_costume             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ speed_force                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ phoenix_force                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ molecular_dissipation        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_cryo                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnipresent                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omniscient                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
```

14. How many superheros have a combination of accelerated healing, durability, and super strength?

```r
superhero_powers%>%
  filter(accelerated_healing == TRUE & durability == TRUE & super_strength == TRUE)
```

```
## # A tibble: 97 × 168
##    hero_names   agility accele…¹ lante…² dimen…³ cold_…⁴ durab…⁵ stealth energ…⁶
##    <chr>        <lgl>   <lgl>    <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>  
##  1 A-Bomb       FALSE   TRUE     FALSE   FALSE   FALSE   TRUE    FALSE   FALSE  
##  2 Abe Sapien   TRUE    TRUE     FALSE   FALSE   TRUE    TRUE    FALSE   FALSE  
##  3 Angel        TRUE    TRUE     FALSE   FALSE   FALSE   TRUE    TRUE    FALSE  
##  4 Anti-Monitor FALSE   TRUE     FALSE   TRUE    FALSE   TRUE    FALSE   TRUE   
##  5 Anti-Venom   FALSE   TRUE     FALSE   FALSE   FALSE   TRUE    FALSE   FALSE  
##  6 Aquaman      TRUE    TRUE     FALSE   FALSE   TRUE    TRUE    TRUE    FALSE  
##  7 Arachne      TRUE    TRUE     FALSE   FALSE   FALSE   TRUE    FALSE   FALSE  
##  8 Archangel    TRUE    TRUE     FALSE   FALSE   FALSE   TRUE    FALSE   FALSE  
##  9 Ardina       TRUE    TRUE     FALSE   FALSE   TRUE    TRUE    FALSE   FALSE  
## 10 Ares         TRUE    TRUE     FALSE   FALSE   FALSE   TRUE    FALSE   FALSE  
## # … with 87 more rows, 159 more variables: flight <lgl>, danger_sense <lgl>,
## #   underwater_breathing <lgl>, marksmanship <lgl>, weapons_master <lgl>,
## #   power_augmentation <lgl>, animal_attributes <lgl>, longevity <lgl>,
## #   intelligence <lgl>, super_strength <lgl>, cryokinesis <lgl>,
## #   telepathy <lgl>, energy_armor <lgl>, energy_blasts <lgl>,
## #   duplication <lgl>, size_changing <lgl>, density_control <lgl>,
## #   stamina <lgl>, astral_travel <lgl>, audio_control <lgl>, dexterity <lgl>, …
```

## Your Favorite
15. Pick your favorite superhero and let's see their powers!

```r
superhero_powers %>% 
  filter(hero_names == "Batman")
```

```
## # A tibble: 1 × 168
##   hero_…¹ agility accel…² lante…³ dimen…⁴ cold_…⁵ durab…⁶ stealth energ…⁷ flight
##   <chr>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl>   <lgl> 
## 1 Batman  TRUE    FALSE   FALSE   FALSE   FALSE   TRUE    TRUE    FALSE   FALSE 
## # … with 158 more variables: danger_sense <lgl>, underwater_breathing <lgl>,
## #   marksmanship <lgl>, weapons_master <lgl>, power_augmentation <lgl>,
## #   animal_attributes <lgl>, longevity <lgl>, intelligence <lgl>,
## #   super_strength <lgl>, cryokinesis <lgl>, telepathy <lgl>,
## #   energy_armor <lgl>, energy_blasts <lgl>, duplication <lgl>,
## #   size_changing <lgl>, density_control <lgl>, stamina <lgl>,
## #   astral_travel <lgl>, audio_control <lgl>, dexterity <lgl>, …
```

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.   
