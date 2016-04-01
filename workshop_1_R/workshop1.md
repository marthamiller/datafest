Introduction to R
========================================================
author: Grace Yoo, Galen Long
date: 2016-04-01

Installation
========================================================

- Google it

Loading data
========================================================


```r
setwd("/Users/mm40078/Documents/projects/datafest2016/data/")
workshops <- read.csv("workshops.csv", stringsAsFactors = FALSE, sep = ",")
workshops[1:2,1:3]
```

```
  first_name last_name     pronouns
1      grace       yoo      she/her
2      galen      long    they/them
```

Slide With Plot
========================================================

![plot of chunk unnamed-chunk-2](workshop1-figure/unnamed-chunk-2-1.png) 
