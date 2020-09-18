# Interactive Choropleth Map

This chapter has an example of creating an interactive leaflet map using 2016 county-level election results. I left it mostly unchanged from what you did in 380 so you'll recognize it and can see how bookdown uses RMD files you've used in the past and just puts them together as a book.






```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(leaflet)
library(tigris)
```

```
## To enable 
## caching of data, set `options(tigris_use_cache = TRUE)` in your R script or .Rprofile.
```

```r
options(tigris_use_cache = TRUE)
```

This assignment builds on what you did in HW-02-12 and what we discussed in class Tuesday (Feb. 12).

## Shapefiles

You can use the same packages and GIS shapefiles as you did in HW-02-12. 


```r
#load GIS shapefiles using tigris package functions
statesGIS <- states(cb = TRUE,resolution = "20m")
countiesGIS <- counties(cb = TRUE,resolution = "20m")

## Drop  Puerto Rico (since we don't have election data for it)
countiesGIS <- subset(countiesGIS,STATEFP != "72")
statesGIS <- subset(statesGIS,STATEFP != "72")

## Let's also drop Alaska and Hawaii
countiesGIS <- subset(countiesGIS,!(STATEFP %in% c("02","15")))
statesGIS <- subset(statesGIS,!(STATEFP %in% c("02","15")))

## Keep only the columns we need (GEOID is 5-character FIPS)
## This part isn't necessary. It just makes the resulting file a little smaller, so the final map will load a little quicker. I won't bother for statesGIS because it's small already
countiesGIS <- countiesGIS[,(names(countiesGIS) %in% c("GEOID","NAME"))]
```


## Data
For this assignment you can use data for the 2016 presidential election. [The website we used for the 2004, 2008, and 2012 election results for HW-02-12](https://www.baruch.cuny.edu/confluence/display/geoportal/US+Presidential+Election+County+Results) linked to a [github page with data for the 2016 election](https://github.com/tonmcg/US_County_Level_Election_Results_08-16). The site has Python code to scrape the data from a website (which we're not going to look at but some of you might find interesting at some point). We'll use the csv file they named [2016_US_County_Level_Presidential_Results.csv](https://github.com/tonmcg/US_County_Level_Election_Results_08-16). When you click on the link to that file you see it displayed in GitHub. If you click on the "Raw" button near the top of the data it opens up the actual csv file, i.e., a page with values separated by commas. Copy the URL of this page. Paste the URL as the file name in the `read.csv()` function. Then manipulate the data so that it can be used to make the maps. Make sure to fix the FIPS code for Oglala Lakota, SD by changing FIPS code 46113 in the 2016 voting data to FIPS code 46102 before merging with the county shape file (`countiesGIS`). 

To make sure you understand how this works, here is an example using the 2012 data from HW-02-12. The URL for that data is (http://faculty.baruch.cuny.edu/geoportal/data/county_election/elpo12p010g.csv). You could read this data using the command `dta2012 <- read.csv("http://faculty.baruch.cuny.edu/geoportal/data/county_election/elpo12p010g.csv")` instead of downloading the file. 



```r
## Read the data from URL
# dta2016 <- read.csv("put URL here")
# Write rest of code to manipulate data and merge it with countiesGIS
```



```r
## Read the data from URL
dta2016 <- read.csv("https://raw.githubusercontent.com/tonmcg/US_County_Level_Election_Results_08-16/master/2016_US_County_Level_Presidential_Results.csv")

## Remember to examine the column names
colnames(dta2016)
```

```
##  [1] "X"              "votes_dem"      "votes_gop"      "total_votes"   
##  [5] "per_dem"        "per_gop"        "diff"           "per_point_diff"
##  [9] "state_abbr"     "county_name"    "combined_fips"
```

```r
dta2016$Winner2016 <- ifelse(dta2016$votes_gop > dta2016$votes_dem,"Trump","Clinton")

colnames(dta2016)[colnames(dta2016)=="combined_fips"] <- "FIPS"
colnames(dta2016)[colnames(dta2016)=="state_abbr"] <- "STATE"
colnames(dta2016)[colnames(dta2016)=="per_dem"] <- "PctDem2016"
colnames(dta2016)[colnames(dta2016)=="per_gop"] <- "PctRep2016"
colnames(dta2016)[colnames(dta2016)=="total_votes"] <- "TotalVote2016"
dta2016$PctWinner2016 <- pmax(dta2016$PctDem2016,dta2016$PctRep2016)
dta2016$FontColorWinner2016 <- ifelse(dta2016$votes_gop > dta2016$votes_dem,"red",
                                  ifelse(dta2016$votes_gop < dta2016$votes_dem,"blue",
                                  "purple"))



dta2016[dta2016$FIPS==46113,]
```

```
##         X votes_dem votes_gop TotalVote2016 PctDem2016 PctRep2016  diff
## 2412 2411      2504       241          2896  0.8646409 0.08321823 2,263
##      per_point_diff STATE   county_name  FIPS Winner2016 PctWinner2016
## 2412         78.14%    SD Oglala County 46113    Clinton     0.8646409
##      FontColorWinner2016
## 2412                blue
```

```r
dta2016[dta2016$FIPS==46102,]
```

```
##  [1] X                   votes_dem           votes_gop          
##  [4] TotalVote2016       PctDem2016          PctRep2016         
##  [7] diff                per_point_diff      STATE              
## [10] county_name         FIPS                Winner2016         
## [13] PctWinner2016       FontColorWinner2016
## <0 rows> (or 0-length row.names)
```

```r
dta2016$FIPS <- ifelse(dta2016$FIPS==46113,46102,dta2016$FIPS)
dta2016[dta2016$FIPS==46113,]
```

```
##  [1] X                   votes_dem           votes_gop          
##  [4] TotalVote2016       PctDem2016          PctRep2016         
##  [7] diff                per_point_diff      STATE              
## [10] county_name         FIPS                Winner2016         
## [13] PctWinner2016       FontColorWinner2016
## <0 rows> (or 0-length row.names)
```

```r
dta2016[dta2016$FIPS==46102,]
```

```
##         X votes_dem votes_gop TotalVote2016 PctDem2016 PctRep2016  diff
## 2412 2411      2504       241          2896  0.8646409 0.08321823 2,263
##      per_point_diff STATE   county_name  FIPS Winner2016 PctWinner2016
## 2412         78.14%    SD Oglala County 46102    Clinton     0.8646409
##      FontColorWinner2016
## 2412                blue
```

```r
dtaAllYears <- select(dta2016,"FIPS","STATE","PctDem2016",
                      "PctRep2016","PctWinner2016","TotalVote2016",
                      "Winner2016","FontColorWinner2016")


#Create GEOID string from int FIPS
dtaAllYears$GEOID <- sprintf("%05d", dtaAllYears$FIPS)

## Merge mydata with shapefile data
county_dta <- geo_join(countiesGIS, dtaAllYears,"GEOID","GEOID")
```

```
## Warning: `group_by_()` is deprecated as of dplyr 0.7.0.
## Please use `group_by()` instead.
## See vignette('programming') for more help
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.
```

```r
## MODIFY/ADD TO THIS CODE to include 2004 just like 2008 and 2012:
popup_dta <- paste0("<strong>",county_dta$NAME
                    ,", ",county_dta$STATE," (",county_dta$GEOID,")</strong>",
                    "<br><font color='",county_dta$FontColorWinner2012,"'>2012: ",
                    format(county_dta$Winner2016,big.mark=",", trim=TRUE),
                    " (",
                    format(county_dta$PctWinner2016,digits=3, trim=TRUE),
                    "%)</font>"
                    )

labels <- popup_dta %>% lapply(htmltools::HTML)
```


## Maps
Each map should be what we called "Popup on mouseover" map in HW-02-12 (i.e. one layer with a popup that opens without the need to click). Each map should be a...

1. choropleth map of Percent Voting for the Democrat in 2016 (e.g., `PctDem2016`) with a...

2. popup displaying County name, ST (e.g., "Outagamie, WI"), the name of the winner (Trump or Clinton), and the percent voting for the winner.


The only difference between the maps should be the color function (and the legend and legend label).Make one map per code chunk below. Each code chunk should have the following: 

1. Color palette function (i.e., `pal <- ...`). Before this function we'll include a line `pal <- NULL` to make sure the previous palette isn't being re-used.

2. Variable with the legend title

3. Code to make the map



## Map 1: Quantiles with diverging Red-Blue Scale
This map is identical to HW-02-12 so I'll copy/paste the code for you. It uses the "RdBu" palette. Technically this is called a "diverging palette". See this website for details about RColorBrewer: https://moderndata.plot.ly/create-colorful-graphs-in-r-with-rcolorbrewer-and-plotly/



```r
#Make Color Palette
pal <- NULL
pal <- colorQuantile("RdBu", NULL, n = 9)
#Legend title
sLegendTitle <- "Percentiles: % Vote for Dem"
## Map with state borders and popup if click
leaflet(data= county_dta) %>% addTiles() %>%
  setView(-97, 39, zoom = 4) %>% 
  addPolygons(
    fillColor = ~pal(county_dta$PctDem2016),
    weight = 1,
    opacity = 1,
    color = "white",
    dashArray = "3",
    fillOpacity = 0.7,
    highlight = highlightOptions(
      weight = 5,
      color = "#666",
      dashArray = "",
      fillOpacity = 0.7,
      bringToFront = TRUE),
    label = labels,
    labelOptions = labelOptions(
      style = list("font-weight" = "normal", padding = "3px 8px"),
      textsize = "15px",
      direction = "auto")) %>%
addPolygons(data = statesGIS,fill = FALSE,color="yellow",weight = 1) %>%
  addLegend(pal = pal,values = ~county_dta$PctDem2016, opacity = 0.7, title = sLegendTitle,position = "bottomright") 
```

```
## Warning: sf layer has inconsistent datum (+proj=longlat +datum=NAD83 +no_defs).
## Need '+proj=longlat +datum=WGS84'

## Warning: sf layer has inconsistent datum (+proj=longlat +datum=NAD83 +no_defs).
## Need '+proj=longlat +datum=WGS84'
```

<!--html_preserve--><div id="htmlwidget-d8b8b9d507b644586654" style="width:100%;height:480px;" class="leaflet html-widget"></div>
