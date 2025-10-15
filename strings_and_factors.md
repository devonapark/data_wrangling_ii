14OCT_part2
================
2025-10-15

His usual starting point

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

Pulling data from online (rather than using read_csv)

``` r
library(tidyverse)
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)

library(p8105.datasets)
```

## Mostly use string vectors

``` r
#Define a vecotr and use a string operator on it

string_vec = c("my", "name", "is", "jeff") #strings are case sensitive

str_detect(string_vec, "jeff")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
str_detect(string_vec, "e") #finds anywhere where e exists. This is case sensitive.
```

    ## [1] FALSE  TRUE FALSE  TRUE

``` r
str_detect(string_vec, "Jeff") #all false
```

    ## [1] FALSE FALSE FALSE FALSE

``` r
str_replace(string_vec, "jeff", "Jeff")
```

    ## [1] "my"   "name" "is"   "Jeff"

``` r
str_replace(string_vec, "e", "E")
```

    ## [1] "my"   "namE" "is"   "jEff"

``` r
str_replace(string_vec, "e", "")
```

    ## [1] "my"  "nam" "is"  "jff"

``` r
#variation of directly above: (helpful to remove whitespace)
  str_remove(string_vec, "e")
```

    ## [1] "my"  "nam" "is"  "jff"

``` r
string_vec = c(
  "i think we all rule for participating",
  "i think i have been caught",
  "i think this will be quite fun actually",
  "it will be fun, i think"
  )

#phrases that start with i think versus end with i think may need to be treated differently
str_detect(string_vec, "i think") #looks everywhere
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
str_detect(string_vec, "^i think") #look only at beginning
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
str_detect(string_vec, "i think$") #look at end
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
str_remove(string_vec, "i think$")
```

    ## [1] "i think we all rule for participating"  
    ## [2] "i think i have been caught"             
    ## [3] "i think this will be quite fun actually"
    ## [4] "it will be fun, "

Pumpkin themed example:

``` r
#when the word shows up in different forms 

string_vec = c(
  "Time for a Pumpkin Spice Latte!",
  "went to the #pumpkinpatch last weekend",
  "Pumpkin Pie is obviously the best pie",
  "SMASHING PUMPKINS -- LIVE IN CONCERT!!"
  )

str_detect(string_vec,"pumpkin")
```

    ## [1] FALSE  TRUE FALSE FALSE

``` r
str_detect(string_vec,"Pumpkin")
```

    ## [1]  TRUE FALSE  TRUE FALSE

``` r
str_detect(string_vec,"[Pp]umpkin") #capital or lowercase p (anything in brackets counts) then everything outside needs to match exactly
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
#easiest way to get any version, create a lowercase version and detect on that (this way you keep the original if necessary) or force the original to all lowercase (if you dont need to keep the original)
```

Let’s get more complicated

``` r
string_vec = c(
  '7th inning stretch',
  '1st half soon to begin. Texas won the toss.',
  'she is 5 feet 4 inches tall',
  '3AM - cant sleep :('
  )

#(any) number followed by a (any) letter (upper or lower case)
str_detect(string_vec, "[0-9]")
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
str_detect(string_vec, "[0-9][a-zA-Z]") #same as writing [0123456789]
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
str_detect(string_vec, "^[0-9][a-zA-Z]") 
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
#fairly often used pattern 
```

``` r
string_vec = c(
  'Its 7:11 in the evening',
  'want to go to 7-11?',
  'my flight is AA711',
  'NetBios: scanning ip 203.167.114.66'
  )

str_detect(string_vec, "7.11") #the dot is a special character that means anything matches a dot. (letter, number, space, special character, anything)
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
#if you want to detect a special character, you need to indicate that it is a special character, put \\
str_detect(string_vec, "7\\.11")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
#another example
string_vec = c(
  'The CI is [2, 5]',
  ':-]',
  ':-[',
  'I found the answer on pages [6-7]'
  )

str_detect(string_vec, "\\[")
```

    ## [1]  TRUE FALSE  TRUE  TRUE

``` r
#if youre looking for a backslash, you may need to do 4 backslashes 
```

(Look up online what the regular expression is to find a validated email
address)

## Factors

``` r
#create a vector that is a binary sex variable
vec_sex = factor(c("male", "male", "female", "female")) #explicit way of making it a factor
vec_sex
```

    ## [1] male   male   female female
    ## Levels: female male

``` r
#when you print a string vector, it puts quotes around everything and tells you nothing else
#when you print a factor it does not have quotes (it is a label sitting on top of something) and it tells you the levels 
```

You can see this more explicitly when you convert it to a numeric

``` r
as.numeric(vec_sex)
```

    ## [1] 2 2 1 1

Update in a variety of ways

``` r
vec_sex = fct_inorder(vec_sex) #makes the first observation it sees as the first level

#aka now male is first
#default ordering is alphabetical

#in a dataframe it wont usually print out the levels for you 


vec_sex = fct_relevel(vec_sex, "male") #switches the order so what is put here becomes first level
```

## Revisit NSDUH

Import NSDUH data from the web:

``` r
#Define URL 

url = "https://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

#Read URL
drug_use_html = read_html(url)
```

This is an “easy” case

``` r
#Pull out everything that is a table (only a table)

nsduh_df = 
  drug_use_html |> 
  html_table() |> 
  first() |> 
  slice(-1) 

#[[x]] is how lists are indexed
```

Now do the tidying…

``` r
marj_df = 
  nsduh_df |>
  select(-contains("P Value")) |> #get rid of pvalue column
  pivot_longer(
    -State, #pivot everything but the state variable
    names_to = "age_year", 
    values_to = "percent") |>
  separate(age_year, into = c("age", "year"), sep = "\\(") |> #separate by delimeter (. (ise double backslash. gets rid of "(".)
  mutate(
    year = str_remove(year, "\\)"),
    percent = str_replace(percent, "[a-c]$", ""), #remove anything at the tail end. If there is some other string variable that exists that doesnt match, i want it to still be there so i can find it later. This does not automatically make it a numeric variable
    percent = as.numeric(percent)) |>
  filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West"))) #to make it not have the aggregated rows

#! says NOT.
#could write percent in mutate in one line:  as.numeric(str_replace(percent, "[a-c]$", ""))
```

== looks for an exact match %in% when you have a collection of options
(state can equal anything inside this set)

Let’s make a quick plot!

``` r
marj_df |>
  filter(age == "12-17") |> 
  ggplot(aes(x = State, y = percent, color = year)) + 
    geom_point() + 
    theme(axis.text.x = element_text(angle = 90, hjust = 1)) #rotate x axis labels
```

<img src="strings_and_factors_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

``` r
#this defaults with state on x axis by aphabetical


marj_df |>
  filter(age == "12-17") |> 
  mutate(State = fct_reorder(State, percent)) |> 
  ggplot(aes(x = State, y = percent, color = year)) + 
    geom_point() + 
    theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

<img src="strings_and_factors_files/figure-gfm/unnamed-chunk-14-2.png" width="90%" />

``` r
#didnt include mutate in datacleaning step becuase i am not sure i want it to exist forever. i only want it in the graph i am making in this specific step
```
