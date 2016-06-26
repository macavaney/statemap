---
title       : US statistics by state
subtitle    : The beginnings of a widget
author      : K. MacAvaney
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## A picture is worth a thousand words

The quickest way to get your point across is with a diagram.

Whether you have a spreadsheet of census data or an ever-changing stream of sales data in your database, it would look better as an interactive map.

In the example widget, I've included some general US statistics. However, different data can very easily be substituted into this format.

Some potential uses for an interactive US map:
* Business presentation (can use sales data)
* Online article (can use election results, economic data - whatever you're reporting on)
* Website
* Landing page for a database (can substitute metrics or whatever changing data is in your database)

--- .class #id 

## What data is currently being used?

The state.x77 dataset in the `dataset` package.


```r
library(datasets)
str(data.frame(state.x77))
```

```
## 'data.frame':	50 obs. of  8 variables:
##  $ Population: num  3615 365 2212 2110 21198 ...
##  $ Income    : num  3624 6315 4530 3378 5114 ...
##  $ Illiteracy: num  2.1 1.5 1.8 1.9 1.1 0.7 1.1 0.9 1.3 2 ...
##  $ Life.Exp  : num  69 69.3 70.5 70.7 71.7 ...
##  $ Murder    : num  15.1 11.3 7.8 10.1 10.3 6.8 3.1 6.2 10.7 13.9 ...
##  $ HS.Grad   : num  41.3 66.7 58.1 39.9 62.6 63.9 56 54.6 52.6 40.6 ...
##  $ Frost     : num  20 152 15 65 20 166 139 103 11 60 ...
##  $ Area      : num  50708 566432 113417 51945 156361 ...
```

--- .class #id

## Looking under the hood at the server code


```r
states <- data.frame(state.name, state.x77) 
shinyServer(function(input, output){ 
    output$plot1 <- renderGvis({ 
            gvisGeoChart(data = states, 
                         #What variable indicates location data:
                         locationvar = "state.name",
                         #Values to base coloring:
                         colorvar = input$variable,
                         #What area to map:
                         options = list(region = "US",
                                        #Indicate data by areas, rather than pins:
                                        displayMode = "regions",
                                        #Break map up by state lines:
                                        resolution = "provinces", 
                                        width = 600,
                                        height = 400,
                                        #Set color gradient:
                                        colorAxis = "{colors: ['#C0CDC5','#F90409']}"))})})
```

--- .class #id

## Looking under the hood at the UI code


```r
shinyUI(fluidPage(theme = "bootstrap.css", #Refer to font/coloring CSS theme
                  #Title:
                  headerPanel(h1("US statistics by state")), 
                                    #Map:
                  fluidRow(column(12, align = "center",
                                  htmlOutput("plot1"))),
                  fluidRow(column(12, align = "center",
                                  h4(),
                                  #Drop-down menu:
                                  selectInput("variable", 
                                              "What information do you want to see?",
                                              #Variables to map:
                                              choices = names(states)),
                                  #Explanation of where data came from:
                                  em("Data is taken from the state.x77 R dataset. See 
                                  ?state.x77 for more details on each of the 
                                  variables.") 
                        ))))
```




