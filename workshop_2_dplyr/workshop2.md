<style>
.small-code pre code {
  font-size: 1.2em;
}
</style>

Thinking fast with dplyr
========================================================
author: Freddie Sanchez, Galen Long
date: 2016-04-01

Resources
========================================================

Dplyr cheat sheet: https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf

Workshop data
========================================================
class: small-code


```r
# install.packages("nycflights13")
library(nycflights13) # flights, airports, airlines, planes, weather
flights[1:3,1:8]
```

```
  year month day dep_time dep_delay arr_time arr_delay carrier
1 2013     1   1      517         2      830        11      UA
2 2013     1   1      533         4      850        20      UA
3 2013     1   1      542         2      923        33      AA
```

```r
names(flights)
```

```
 [1] "year"      "month"     "day"       "dep_time"  "dep_delay"
 [6] "arr_time"  "arr_delay" "carrier"   "tailnum"   "flight"   
[11] "origin"    "dest"      "air_time"  "distance"  "hour"     
[16] "minute"   
```

```r
nrow(flights)
```

```
[1] 336776
```

Generate questions
========================================================

Think of 10 *specific*, *testable* questions you have about the Ticketmaster data.

Examples for the flights data:

- Which planes fly the most?
- Do some days of the week have more flights than others?
- Do some carriers have more delays than others?
- Do longer flights tend to have more delays?

Organize questions
========================================================

Do some questions seem to be related to each other? Try and group them, and sort them in order of fruitfulness/ease of exploration.

Examples for the flights data:

1. Which planes fly the most?
2. How do the number of flights change over time?
3. Which variables have relationships with delays?

dplyr
========================================================
class: small-code


```r
# install.packages("dplyr")
library(dplyr)
```

Looking at data
========================================================
class: small-code

- Use `select()` to choose columns. Has equivalent `[]` syntax.
- Use `filter()` to select rows. Has equivalent `[]` syntax.
- Use `arrange()` to sort rows by column.


```r
# flights[,c('year', 'month', 'day', 'origin', 'dest')]
select(flights, year, month, day, origin, dest)

# flights[flights$year != 2013 & day == 1 | flight == 1613,]
filter(flights, year != 2013 & day == 1 | flight == 1613)

flights %>% # pipe data using %>%
  select(year, month, day, arr_delay, origin, dest) %>%
  arrange(-arr_delay)
```


Which planes fly the most?
========================================================
class: small-code

Tailnum uniquely identifies a plane.


```r
flights %>%
  group_by(tailnum) %>%
  summarize(num_flights = n(),
            total_air_time = sum(air_time, na.rm = TRUE),
            max_distance = max(distance)) %>%
  arrange(-num_flights, -total_air_time)
```

```
Source: local data frame [4,044 x 4]

   tailnum num_flights total_air_time max_distance
     (chr)       (int)          (dbl)        (dbl)
1                 2512              0         4963
2   N725MQ         575          48921         1147
3   N722MQ         513          42664         1147
4   N723MQ         507          41496         1147
5   N711MQ         486          40709         1147
6   N713MQ         483          40031         1147
7   N258JB         427          35835         1598
8   N298JB         407          31020         1598
9   N353JB         404          33687         1598
10  N351JB         402          30840         1598
..     ...         ...            ...          ...
```

How do the number of flights change over time?
========================================================
class: small-code

You can utilize multiple functions to modify and aggregate variables.


```r
flights %>%
  mutate(date = paste(year, month, day, sep='-'),
         date = as.Date(date)) %>%
  group_by(date) %>%
  summarise(count = n()) %>% head()
```

```
Source: local data frame [6 x 2]

        date count
      (date) (int)
1 2013-01-01   842
2 2013-01-02   943
3 2013-01-03   914
4 2013-01-04   915
5 2013-01-05   720
6 2013-01-06   832
```

How do the number of flights change over time?
========================================================
class: small-code

<img src="workshop2-figure/unnamed-chunk-6-1.png" title="plot of chunk unnamed-chunk-6" alt="plot of chunk unnamed-chunk-6" width="600px" />

How do the number of flights change over time?
========================================================
class: small-code

Regular R functions can piped as well.


```r
select(flights, month) %>%
  table() %>%
  barplot()
```


```
.
    1     2     3     4     5     6     7     8     9    10    11    12 
27004 24951 28834 28330 28796 28243 29425 29327 27574 28889 27268 28135 
```

How do the number of flights change over time?
========================================================
class: small-code

Regular R functions can piped as well.

<img src="workshop2-figure/unnamed-chunk-9-1.png" title="plot of chunk unnamed-chunk-9" alt="plot of chunk unnamed-chunk-9" width="500px" />


Do some carriers have more delays than others?
========================================================
class: small-code


```r
flights %>%
  select(carrier, arr_delay) %>%
  group_by(carrier) %>%
  summarize(num_flights = n(),
            total_arr_delay = sum(arr_delay, na.rm = TRUE),
            delay_per_flight = total_arr_delay/num_flights) %>%
  arrange(-delay_per_flight) %>%
  head(n = 7)
```

```
Source: local data frame [7 x 4]

  carrier num_flights total_arr_delay delay_per_flight
    (chr)       (int)           (dbl)            (dbl)
1      F9         685           14928        21.792701
2      FL        3260           63868        19.591411
3      EV       54173          807324        14.902701
4      YV         601            8463        14.081531
5      OO          32             346        10.812500
6      MQ       26397          269767        10.219608
7      WN       12275          116214         9.467536
```


Does destination contribute to arrival delay?
========================================================
class: small-code



```r
flights %>%
  select(dest, arr_delay) %>%
  group_by(dest) %>%
    summarize(num_flights = n(),
            total_arr_delay = sum(arr_delay, na.rm = TRUE),
            delay_per_flight = total_arr_delay / num_flights) %>%
  filter(num_flights > 25) %>%
  arrange(delay_per_flight) %>%
  top_n(5, delay_per_flight)
```

```
Source: local data frame [5 x 4]

   dest num_flights total_arr_delay delay_per_flight
  (chr)       (int)           (dbl)            (dbl)
1   MSN         572           11229         19.63112
2   TYS         631           13912         22.04754
3   OKC         346            9645         27.87572
4   TUL         315            9896         31.41587
5   CAE         116            4427         38.16379
```


Does destination contribute to arrival delay?
========================================================
class: small-code

Order table by 'arr_delay', and visualize differences between destinations.

<img src="workshop2-figure/unnamed-chunk-12-1.png" title="plot of chunk unnamed-chunk-12" alt="plot of chunk unnamed-chunk-12" width="475px" />

Summary
========================================================
class: small-code


```r
select(flights, year, month, day, arr_delay, origin, dest)
filter(flights, year != 2013)
arrange(flights, -arr_delay)
group_by(flights, tailnum) %>% summarize(num_flights = n())
mutate(flights,
       flight_date = as.Date(paste(year, month, day, sep = '-')))
  %>% select(year, month, day, flight_date)
```


Thank you!
========================================================

Saturday workshops:<br />
Saturday 11 - 11:45 AM, *Visualizing data with ggplot2*, Deirdre<br />
Saturday 1:30 - 2:15 PM, *Handling dates with lubridate*, John

Github: https://github.com/massmutual/datafest2016<br />
Feedback: http://goo.gl/forms/rHnscEWjTL

Contact: gyoo, galenlong [at] massmutual [dot] com
