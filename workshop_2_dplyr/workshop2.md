<style>
.small-code pre code {
  font-size: 1.2em;
}
</style>

Thinking fast with dplyr
========================================================
author: Freddie Sanchez, Galen Long
date: 2016-04-01

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
- Do some carriers have more delays than others on average?
- Do longer flights tend to have more delays?

Organize questions
========================================================

Do some questions seem to be related to each other? Try and group them, and sort them in order of fruitfulness/ease of exploration.

Examples for the flights data:

1. Which planes fly the most?
2. How do the number of flights change over time?
3. Which variables have relationships with delays? Can we predict delays?

dplyr
========================================================

There are five basic functions in dplyr syntax:

- `select()`
- `filter()`
- `arrange()`
- `group_by()`
- `summarize()`

Let's first investigate which planes fly the most.

select()
========================================================
class: small-code

Use `select` to choose columns. Has equivalent `[]` syntax.


```r
# install.packages("dplyr")
library(dplyr)

# flights[,c('year', 'month', 'day', 'origin', 'dest')]
select(flights, year, month, day, origin, dest)
```

```
Source: local data frame [336,776 x 5]

    year month   day origin  dest
   (int) (int) (int)  (chr) (chr)
1   2013     1     1    EWR   IAH
2   2013     1     1    LGA   IAH
3   2013     1     1    JFK   MIA
4   2013     1     1    JFK   BQN
5   2013     1     1    LGA   ATL
6   2013     1     1    EWR   ORD
7   2013     1     1    EWR   FLL
8   2013     1     1    LGA   IAD
9   2013     1     1    JFK   MCO
10  2013     1     1    LGA   ORD
..   ...   ...   ...    ...   ...
```

filter()
========================================================
class: small-code

Use `filter` to select rows. Has equivalent `[]` syntax.


```r
# flights[flights$year != 2013,]
filter(flights, year != 2013)
```

```
Source: local data frame [0 x 16]

Variables not shown: year (int), month (int), day (int), dep_time (int),
  dep_delay (dbl), arr_time (int), arr_delay (dbl), carrier (chr), tailnum
  (chr), flight (int), origin (chr), dest (chr), air_time (dbl), distance
  (dbl), hour (dbl), minute (dbl)
```

Since there's no rows in the data frame, we can infer that the only year in the data is `2013`.

arrange()
========================================================
class: small-code

Use `arrange()` to sort rows by column.


```r
flights %>% # pipe data using %>%
  select(year, month, day, arr_delay, origin, dest) %>%
  arrange(-arr_delay)
```

```
Source: local data frame [336,776 x 6]

    year month   day arr_delay origin  dest
   (int) (int) (int)     (dbl)  (chr) (chr)
1   2013     1     9      1272    JFK   HNL
2   2013     6    15      1127    JFK   CMH
3   2013     1    10      1109    EWR   ORD
4   2013     9    20      1007    JFK   SFO
5   2013     7    22       989    JFK   CVG
6   2013     4    10       931    JFK   TPA
7   2013     3    17       915    LGA   MSP
8   2013     7    22       895    LGA   ATL
9   2013    12     5       878    EWR   MIA
10  2013     5     3       875    EWR   ORD
..   ...   ...   ...       ...    ...   ...
```

Reminder
========================================================
class: small-code


```r
select(flights, year, month, day, arr_delay, origin, dest)
filter(flights, year != 2013)
arrange(flights, -arr_delay)
# mutate as date
```
