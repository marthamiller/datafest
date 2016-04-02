<style>
.small-code pre code {
  font-size: 1.2em;
}
</style>

Introduction to R
========================================================
author: Grace Yoo, Galen Long
date: 2016-04-01
css: style.css 


Download data
========================================================

https://www.dropbox.com/s/7v6i3rc58juy6z6/workshops.csv?dl=0


Loading data
========================================================
class: small-code

Save and load the data.


```r
setwd("/Users/mm40078/Documents/projects/datafest2016/data/")
workshops <- read.csv("workshops.csv", 
                      stringsAsFactors = FALSE, 
                      sep = ",",
                      strip.white = TRUE)
```

You can store it as an `.Rda` to speed up reloading.


```r
setwd("/Users/mm40078/Documents/projects/datafest2016/data/")
save(workshops, file = 'workshops.Rda')
load('workshops.Rda') # loads the workshops data frame
```

Look at the whole data frame:


```r
workshops # don't do this with large data!
View(workshops) # nicer way to view data
```

Sampling data
========================================================
class: small-code

Good for testing out ideas quickly.


```r
x <- sample(nrow(workshops), size = 3)
x
```

```
[1] 2 3 1
```

```r
workshops_sample <- workshops[x, ]
workshops_sample[,1:5]
```

```
  first_name last_NAME  pronouns gender num_pets
2      galen      long they/them lizard        0
3      galen      long they/them lizard       NA
1      grace       yoo   she/her      W        2
```

Looking at data
========================================================
class: small-code

The basics:


```r
names(workshops) # column names
nrow(workshops)
ncol(workshops)
```

Looking at subsets:


```r
head(workshops) # first 6 rows
tail(workshops) # last 6 rows
workshops[1,] # 1st row for all columns
workshops[,3] # 3rd column for all rows
workshops$pronouns # column by name
```

How could we look at the last two rows of the `pronouns` and `gender` columns?

Looking at data
========================================================
class: small-code

How could we look at the last two rows of the `pronouns` and `gender` columns?


```r
workshops[5:6, 3:4]
# or...
workshops[(nrow(workshops) - 1):nrow(workshops), 
          c("pronouns", "gender")]
```

How did that last example work?


```r
(nrow(workshops) - 1):nrow(workshops) # a list of row numbers
```

```
[1] 5 6
```

```r
c("pronouns", "gender") # a list of column names
```

```
[1] "pronouns" "gender"  
```

Filtering data
========================================================
class: small-code


```r
workshops$first_name == "galen"
```

```
[1] FALSE  TRUE  TRUE FALSE FALSE FALSE
```

We can use this to filter data.


```r
workshops[workshops$first_name == "galen",1:6]
```

```
  first_name last_NAME  pronouns gender num_pets pet_names
2      galen      long they/them lizard        0      <NA>
3      galen      long they/them lizard       NA      NULL
```

Filtering data
========================================================
class: small-code

We can also do this with columns:


```r
cols <- c(FALSE, FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, FALSE, TRUE, FALSE) # 4th and 9th column
workshops[,cols]
```

```
  gender                                        worst_trait
1      W                                 resting stink face
2 lizard                     wears sweatpants to work daily
3 lizard                     wears sweatpants to work daily
4      M                                               puns
5      W does hw so far ahead of deadline that she loses it
6      M                                 lactose intolerant
```

*General principle*: make a vector of booleans with the same length as the number of rows/columns and subset the data frame by that.

Filtering data
========================================================
class: small-code


```r
# previous example
workshops[workshops$first_name == "galen",1:6]
# operators: ==, !=, <, >, !
```

1. How can we get all rows where the `workshop_code` is `1`?

2. How can we get all rows where the number of pets is greater than 0 *and* the gender is non-male?

3. How can we get all columns with `name` in the column name?

Filtering data
========================================================
class: small-code

1) All rows where the `workshop_code` is `1`.


```r
workshops[workshops$workshop_code == 1,1:6]
```

2) All rows where `num_pets` > 0 and `gender` is non-male.


```r
workshops[(workshops$num_pets > 0 
           & ! is.na(workshops$num_pets)
           & workshops$gender != "male")
          ,1:6]
```

3) All columns with `name` in the column name.


```r
workshops[, grepl("name", names(workshops), ignore.case = TRUE)]
```

What is `grepl`? Try: `?grepl`

Missing values
========================================================
class: small-code

How many missing values are in the `pet_names` column?


```r
workshops$pet_names
```

```
[1] "Sundance, Bahngee"   NA                    "NULL"               
[4] "n/a"                 "Koji, Bailey, Daisy" "Bay"                
```

```r
is.na(workshops$pet_names)
```

```
[1] FALSE  TRUE FALSE FALSE FALSE FALSE
```

```r
sum(is.na(workshops$pet_names)) # count number of NAs
```

```
[1] 1
```

Missing values
========================================================
class: small-code

How can we convert all missing values to `NA`? 


```r
workshops$pet_names[2] # this one was converted
```

```
[1] NA
```

```r
workshops$pet_names[3] # but this one's still a string
```

```
[1] "NULL"
```

Hint: `?read.csv`

Missing values
========================================================
class: small-code


```r
setwd("/Users/mm40078/Documents/projects/datafest2016/data/")
workshops <- read.csv("workshops.csv", 
                      stringsAsFactors = FALSE, 
                      sep = ",",
                      strip.white = TRUE,
                      na.string = c('NA', 'n/a', 
                                    'NULL', '', '<NA>'))
workshops$pet_names
```

```
[1] "Sundance, Bahngee"   NA                    NA                   
[4] NA                    "Koji, Bailey, Daisy" "Bay"                
```

```r
sum(is.na(workshops$pet_names))
```

```
[1] 3
```


Saving data
========================================================
class: small-code


```r
setwd("/Users/mm40078/Documents/projects/datafest2016/data/")
write.csv(workshops, "workshops_cleaned.csv", 
          row.names = FALSE)
```


Summary statistics
========================================================
class: small-code

Basic summary statistics:


```r
mean(workshops$num_pets, na.rm = TRUE) # exclude NAs
median(workshops$num_pets, na.rm = TRUE)
min(workshops$num_pets, na.rm = TRUE)
max(workshops$num_pets, na.rm = TRUE)
sum(is.na(workshops$num_pets))
```


```r
# does all of the above and more
summary(workshops$num_pets)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
   0.00    0.75    1.50    1.50    2.25    3.00       2 
```

What about these?


```r
min(workshops$pet_names, na.rm = TRUE)
max(workshops$pet_names, na.rm = TRUE)
mean(workshops$pet_names, na.rm = TRUE)
```

Summary statistics, categorical variables
========================================================
class: small-code


```r
# treat categorical as strings, not numbers
# you can do the opposite with as.numeric()
workshops$workshop_code <- as.character(
  workshops$workshop_code)

unique(workshops$workshop_name)
```

```
[1] "intro to R"                    "intro to S"                   
[3] "thinking fast with dplyr"      "visualizing with ggplot2"     
[5] "handling dates with lubridate"
```

```r
table(workshops$undergrad[-2]) # exclude row 2
```

```

Mount Holyoke College         Smith College                  UCLA 
                    1                     2                     1 
                UMass 
                    1 
```


Aggregating
========================================================
class: small-code

What is the unit of observation of this data frame?


Aggregating
========================================================
class: small-code

How could we change the unit of observation to "person"?

What would the code look like?

Thank you!
========================================================

Stick around and find out in our next workshop: `Thinking fast with dplyr`.

Github: https://github.com/massmutual/datafest2016<br />
Feedback: http://goo.gl/forms/rHnscEWjTL

Contact: gyoo, galenlong [at] massmutual [dot] com
