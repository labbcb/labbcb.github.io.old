Basics ggplot2, dplyr and data.table
========================================================
author: Benilton Carvalho
date: November 2014
width: 1440
height: 900

Loading useful packages
========================================================

- The `ggplot2` package is one of the best packages for the production of high-quality plots.
- `ggplot2` requires the data to be in a specific format, for which I use the `dplyr` package.
- Given that these data can get very large, I also use the `data.table` package to improve data access and management.


```r
library(ggplot2)
library(dplyr)
library(data.table)
```

Reading the data
========================================================

- Reading CSV file
- Converting the resulting data.frame into a data.table for faster manipulation


```r
fname = '~/Downloads/data/AirlineSubset1pct.csv'
airline = read.csv(fname, header=TRUE)
airline = data.table(airline)
airline
```

```
         Year Month DayofMonth DayOfWeek DepTime CRSDepTime ArrTime
      1: 1987    10         17         6     741        730     918
      2: 1987    10         27         2     916        915    1014
      3: 1987    10         29         4     745        745     804
      4: 1987    10         27         2     645        645     800
      5: 1987    10         26         1    1028       1029    1140
     ---                                                           
1235019: 2008    12         13         6    1433       1435    1613
1235020: 2008    12         13         6     929        930    1117
1235021: 2008    12         13         6    1851       1849    2009
1235022: 2008    12         13         6    1136       1056    1427
1235023: 2008    12         13         6    1422       1355    2007
         CRSArrTime UniqueCarrier FlightNum TailNum ActualElapsedTime
      1:        849            PS      1451      NA                97
      2:       1001            PS      1451      NA                58
      3:        759            PS      1457      NA                79
      4:        804            PS      1471      NA                75
      5:       1144            PS      1475      NA                72
     ---                                                             
1235019:       1621            DL       863  N121DE               100
1235020:       1115            DL       872  N670DN               108
1235021:       2022            DL      1044  N675DL                78
1235022:       1356            DL      1458  N994DL               171
1235023:       1929            DL      1476   N6702               225
         CRSElapsedTime AirTime ArrDelay DepDelay Origin Dest Distance
      1:             79      NA       29       11    SAN  SFO      447
      2:             46      NA       13        1    SFO  RNO      192
      3:             74      NA        5        0    PHX  LAX      370
      4:             79      NA       -4        0    SAN  OAK      446
      5:             75      NA       -4       -1    SAN  OAK      446
     ---                                                              
1235019:            106      79       -8       -2    ATL  FLL      581
1235020:            105      81        2       -1    RSW  ATL      515
1235021:             93      62      -13        2    ATL  RIC      481
1235022:            180     147       31       40    FLL  LGA     1076
1235023:            214     175       38       27    SLC  ATL     1589
         TaxiIn TaxiOut Cancelled CancellationCode Diverted CarrierDelay
      1:     NA      NA         0               NA        0           NA
      2:     NA      NA         0               NA        0           NA
      3:     NA      NA         0               NA        0           NA
      4:     NA      NA         0               NA        0           NA
      5:     NA      NA         0               NA        0           NA
     ---                                                                
1235019:      4      17         0                         0           NA
1235020:     11      16         0                         0           NA
1235021:      5      11         0                         0           NA
1235022:      5      19         0                         0            0
1235023:     33      17         0                         0            0
         WeatherDelay NASDelay SecurityDelay LateAircraftDelay
      1:           NA       NA            NA                NA
      2:           NA       NA            NA                NA
      3:           NA       NA            NA                NA
      4:           NA       NA            NA                NA
      5:           NA       NA            NA                NA
     ---                                                      
1235019:           NA       NA            NA                NA
1235020:           NA       NA            NA                NA
1235021:           NA       NA            NA                NA
1235022:            0        0             0                31
1235023:           10       11             0                17
```


Operating with a `data.table` object
========================================================

- A `data.table` object is pretty much a `data.frame`


```r
summary(airline)
```

```
      Year          Month          DayofMonth      DayOfWeek    
 Min.   :1987   Min.   : 1.000   Min.   : 1.00   Min.   :1.000  
 1st Qu.:1993   1st Qu.: 4.000   1st Qu.: 8.00   1st Qu.:2.000  
 Median :1999   Median : 7.000   Median :16.00   Median :4.000  
 Mean   :1999   Mean   : 6.548   Mean   :15.73   Mean   :3.941  
 3rd Qu.:2004   3rd Qu.:10.000   3rd Qu.:23.00   3rd Qu.:6.000  
 Max.   :2008   Max.   :12.000   Max.   :31.00   Max.   :7.000  
                                                                
    DepTime        CRSDepTime      ArrTime        CRSArrTime  
 Min.   :   1    Min.   :   0   Min.   :   1    Min.   :   0  
 1st Qu.: 934    1st Qu.: 930   1st Qu.:1116    1st Qu.:1115  
 Median :1334    Median :1328   Median :1522    Median :1520  
 Mean   :1349    Mean   :1335   Mean   :1494    Mean   :1492  
 3rd Qu.:1738    3rd Qu.:1729   3rd Qu.:1917    3rd Qu.:1912  
 Max.   :2533    Max.   :2400   Max.   :2701    Max.   :2400  
 NA's   :22874                  NA's   :25659                 
 UniqueCarrier      FlightNum            TailNum       ActualElapsedTime
 DL     :164966   Min.   :   1   UNKNOW      :  5681   Min.   : -85.0   
 WN     :159713   1st Qu.: 450   0           :  3474   1st Qu.:  70.0   
 AA     :150298   Median : 946   <e4>NKNO<e6>:  1777   Median : 101.0   
 US     :140148   Mean   :1367               :  1419   Mean   : 120.1   
 UA     :132727   3rd Qu.:1715   000000      :   523   3rd Qu.: 151.0   
 NW     :103395   Max.   :9899   (Other)     :850505   Max.   :1635.0   
 (Other):383776                  NA's        :371644   NA's   :25691    
 CRSElapsedTime    AirTime           ArrDelay            DepDelay        
 Min.   : -10   Min.   :-1425.0   Min.   :-1283.000   Min.   :-1180.000  
 1st Qu.:  71   1st Qu.:   54.0   1st Qu.:   -7.000   1st Qu.:   -2.000  
 Median : 102   Median :   84.0   Median :    0.000   Median :    0.000  
 Mean   : 121   Mean   :  102.8   Mean   :    7.075   Mean   :    8.201  
 3rd Qu.: 152   3rd Qu.:  133.0   3rd Qu.:   11.000   3rd Qu.:    6.000  
 Max.   :1497   Max.   : 1594.0   Max.   : 1435.000   Max.   : 1439.000  
 NA's   :276    NA's   :391649    NA's   :25691       NA's   :22874      
     Origin            Dest           Distance          TaxiIn      
 ORD    : 65654   ORD    : 66337   Min.   :  11.0   Min.   :   0.0  
 ATL    : 61162   ATL    : 60597   1st Qu.: 307.0   1st Qu.:   4.0  
 DFW    : 57069   DFW    : 57823   Median : 544.0   Median :   5.0  
 LAX    : 41157   LAX    : 40799   Mean   : 701.6   Mean   :   6.5  
 PHX    : 34797   PHX    : 35179   3rd Qu.: 936.0   3rd Qu.:   7.0  
 DEN    : 33156   DEN    : 33575   Max.   :4983.0   Max.   :1455.0  
 (Other):942028   (Other):940713   NA's   :2062     NA's   :373155  
    TaxiOut         Cancelled       CancellationCode    Diverted       
 Min.   :   0.0   Min.   :0.00000       :388913      Min.   :0.000000  
 1st Qu.:  10.0   1st Qu.:0.00000   A   :  3134      1st Qu.:0.000000  
 Median :  13.0   Median :0.00000   B   :  2678      Median :0.000000  
 Mean   :  15.2   Mean   :0.01853   C   :  1452      Mean   :0.002275  
 3rd Qu.:  18.0   3rd Qu.:0.00000   D   :     7      3rd Qu.:0.000000  
 Max.   :1436.0   Max.   :1.00000   NA's:838839      Max.   :1.000000  
 NA's   :373014                                                        
  CarrierDelay     WeatherDelay       NASDelay      SecurityDelay   
 Min.   :   0.0   Min.   :   0.0   Min.   :  0.0    Min.   :  0     
 1st Qu.:   0.0   1st Qu.:   0.0   1st Qu.:  0.0    1st Qu.:  0     
 Median :   0.0   Median :   0.0   Median :  0.0    Median :  0     
 Mean   :   3.8   Mean   :   0.8   Mean   :  4.1    Mean   :  0     
 3rd Qu.:   0.0   3rd Qu.:   0.0   3rd Qu.:  0.0    3rd Qu.:  0     
 Max.   :1328.0   Max.   :1162.0   Max.   :693.0    Max.   :212     
 NA's   :893110   NA's   :893110   NA's   :893110   NA's   :893110  
 LateAircraftDelay
 Min.   :  0.0    
 1st Qu.:  0.0    
 Median :  0.0    
 Mean   :  4.8    
 3rd Qu.:  0.0    
 Max.   :542.0    
 NA's   :893110   
```

Customizing data representation
=========================================================

- Will convert Year/Month/DayOfWeek/DayofMonth to ordered factors


```r
dim(airline)
```

```
[1] 1235023      29
```

```r
airline$Year = factor(airline$Year, ordered=TRUE)
airline$Month = factor(airline$Month, ordered=TRUE)
airline$DayOfWeek = factor(airline$DayOfWeek, ordered=TRUE)
airline$DayofMonth = factor(airline$DayofMonth, ordered=TRUE)
```

Cleaning dataset
=================

- Keep only flights that were not cancelled


```r
airline = subset(airline, Cancelled==0)
dim(airline)
```

```
[1] 1212142      29
```

Making histograms
========================================================

- `ggplot2` requires data in the long-format
- always use `ggplot` to start and combine `aes()` to define plot details (x, y, color)
- always use a `geom_` to define plot type


```r
myp = ggplot(airline, aes(ArrDelay)) + geom_histogram(binwidth=1)
myp + xlim(-60, 60)
```

![plot of chunk unnamed-chunk-5](ggplotBasics-figure/unnamed-chunk-5-1.png) 

Making stratified density plots - I
========================================================


```r
myp = ggplot(airline, aes(ArrDelay)) + geom_density() 
myp + facet_grid(DayOfWeek ~ . ) + xlim(-100, 100)
```

![plot of chunk unnamed-chunk-6](ggplotBasics-figure/unnamed-chunk-6-1.png) 


Making stratified density plots - II
========================================================


```r
myp = ggplot(airline, aes(ArrDelay, colour=DayOfWeek))
myp + geom_density() + xlim(-100, 100)
```

![plot of chunk unnamed-chunk-7](ggplotBasics-figure/unnamed-chunk-7-1.png) 

Operating with `data.table` for plotting
========================================================

- Proportion of flights delayed (by more than 15min) per date


```r
tmp = group_by(airline, Year, Month, DayofMonth)
delays = summarise(tmp, prop=mean(ArrDelay > 15 & !is.na(ArrDelay)))
head(delays)
```

```
  Year Month DayofMonth      prop
1 1987    10          1 0.2170543
2 1987    10          2 0.1986755
3 1987    10          3 0.1833333
4 1987    10          4 0.1200000
5 1987    10          5 0.1265823
6 1987    10          6 0.1621622
```

Helper function
========================================================

- Function to return row/column for dates as in a calendar


```r
library(lubridate)
cal = function(dt){
  year = year(dt)
  month = month(dt)
  day = day(dt)
  first = ymd(paste(year, month, 1, sep='-'))
  wday_first = wday(first)
  offset = 5 + wday_first
  weekrow = ((day + offset) %/% 7) - 1
  daycol = (day + offset) %% 7
  cbind(weekrow, daycol)
}
```

Calendar with delays
===============


```r
tmp = cal( with(delays, paste(Year, Month, DayofMonth, sep='-')))
delays$weekrow = factor(tmp[,1], levels=5:0, ordered=TRUE)
delays$daycol = factor(tmp[,2], levels=0:6, ordered=TRUE)
logit = function(p) log(p/(1-p))
head(delays)
```

```
  Year Month DayofMonth      prop weekrow daycol
1 1987    10          1 0.2170543       0      4
2 1987    10          2 0.1986755       0      5
3 1987    10          3 0.1833333       0      6
4 1987    10          4 0.1200000       1      0
5 1987    10          5 0.1265823       1      1
6 1987    10          6 0.1621622       1      2
```

Preparing the final plot
===


```r
myp = ggplot(delays, aes(daycol, weekrow, fill=logit(prop)))
myp = myp + geom_tile() + facet_grid(Month ~ Year)
myp = myp + scale_fill_continuous(low='green', high='red')
myp = myp + theme(axis.ticks=element_blank(),
                  axis.title.x=element_blank(),
                  axis.title.y=element_blank(),
                  axis.text.x=element_blank(),
                  axis.text.y=element_blank(),
                  legend.position='none')
```

The final plot
===

<img src="ggplotBasics-figure/unnamed-chunk-12-1.png" title="plot of chunk unnamed-chunk-12" alt="plot of chunk unnamed-chunk-12" width="1280px" height="720px" />
