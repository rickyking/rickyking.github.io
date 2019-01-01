---
title: How to use rCharts - highcharts.js
date: 2014-05-21 20:29:10
tags: [r, shiny, rcharts, highcharts]
categories: [R]
---

## Introduction
Package `rCharts` provides a quick access to interactive plots. It includes the following javascript packages:

- Polycharts: rPlot
- Morris: mPlot
- NVD3: nPlot
- xCharts: xPlot
- Highcharts: hPlot
- Rickshaw: Rickshaw$new()

<!--more-->

Here is a list of existing javascript libraries for data visualization.

| language           | Library      | Package R | Library Python | Technology   | Compatibilité                        | Document                | Licence                                                | Type Graphs        | Commentaire         |
|-----------|--------|---------|-------------|-----------|--------------------------------------|-------------------------|--------------------------------------------------------|--------------------|---------------------|
| javascript       | D3.js        | None      | d3py           | svg          | IE9+, Chrome, Firefox, Safari, Opera | Bien Documenté          | Open Source                                            | ALL                |                     |
| javascript       | HighCharts   | rCharts   | js.highcharts  | svg/vml      | IE6+, Chrome, Firefox, Safari, Opera | Bien Documenté          | Commercial                                             | ALL Chart          |                     |
| javascript       | Raphaël      | None      | js.raphael     | svg/vml      | IE6+, Chrome, Firefox, Safari, Opera | Bien Documenté          | Open Source                                            | ALL                |                     |
| javascript/flash | Google Chart | googleVis | pygooglechart  | svg/vml      | IE6+, Chrome, Firefox, Safari, Opera | Bien Documenté          | Open Source                                            | ALL Chart          |                     |
| D3.js            | dimple       | rCharts   | None           | svg          | IE9+, Chrome, Firefox, Safari, Opera | Demo/Example/API        | Open Source                                            | ALL Chart          |                     |
| D3.js            | NVD3         | rCharts   | python-nvd3    | svg          | IE9+, Chrome, Firefox, Safari, Opera | Demo/Example            | Open Source                                            | ALL Chart          |                     |
| Raphaël          | PolyCharts   | rCharts   | None           | svg/vml      | IE6+, Chrome, Firefox, Safari, Opera | Editor/API              | Open Source (Licence required for external deployment) | ALL Chart          | Spotfire Like/Neo4j |
| Raphaël          | Morris       | rCharts   | None           | svg/vml      | IE6+, Chrome, Firefox, Safari, Opera | Demo/Example            | Open Source                                            | Non Rich           |                     |
| D3.js            | xCharts      | rCharts   | None           | svg          | IE9+, Chrome, Firefox, Safari, Opera | Demo/Example/API        | Open Source                                            | Riche en Animation |                     |
| D3.js            | RickShaw     | rCharts   | js.rickshaw    | svg          | IE9+, Chrome, Firefox, Safari, Opera | Demo/Example            | Open Source                                            | Time Series/Stream |                     |
| D3.js / Langage  | Vega         | rCharts   | Vincent        | svg          | IE9+, Chrome, Firefox, Safari, Opera | Editor/Example/Document | Open Source                                            | ALL                | ggplot2 Like        |
| D3.js            | uvCharts     | rCharts   | None           | svg          | IE9+, Chrome, Firefox, Safari, Opera | Demo/Example/API        | Open Source                                            | ALL Chart          |                     |
| javascript       | sigma.js     | rgexf     |                | Canvas/WebGL | IE9+, Chrome, Firefox, Safari, Opera | Demo/Example/API        | Open Source                                            | Network            | Gephi               |

## A quick interactive graph
Use the corresponding quick graph creation code (see previous section: `rPlot`, `hPlot`, `nPlot`, `xPlot`, etc) to create a fast interactive graph with the formula-[lattice](http://www.statmethods.net/advgraphs/trellis.html)-like graph grammar.

```r
require(rCharts)
r1 <- rPlot(mpg ~ wt | am + vs, data = mtcars, type = "point", color = "gear")
r1
```


The following code provides a better binding in Knitr (see [reproducible reporting]):

```r
require(rCharts)
r1 <- rPlot(mpg ~ wt | am + vs, data = mtcars, type = "point", color = "gear")
r1$show("inline", include_assets = T, cdn = T)
```

<script type='text/javascript' src=http://ramnathv.github.io/rCharts/libraries/widgets/polycharts/js/polychart2.standalone.js></script>
 <style>
  .rChart {
    display: block;
    margin-left: auto;
    margin-right: auto;
    width: 800px;
    height: 400px;
  }
  </style>
<div id = 'chart132456ea2ce4' class = 'rChart polycharts'></div>
<script type='text/javascript'>
    var chartParams = {
 "dom": "chart132456ea2ce4",
"width":    800,
"height":    400,
"layers": [
 {
 "x": "wt",
"y": "mpg",
"data": {
 "mpg": [     21,     21,   22.8,   21.4,   18.7,   18.1,   14.3,   24.4,   22.8,   19.2,   17.8,   16.4,   17.3,   15.2,   10.4,   10.4,   14.7,   32.4,   30.4,   33.9,   21.5,   15.5,   15.2,   13.3,   19.2,   27.3,     26,   30.4,   15.8,   19.7,     15,   21.4 ],
"cyl": [      6,      6,      4,      6,      8,      6,      8,      4,      4,      6,      6,      8,      8,      8,      8,      8,      8,      4,      4,      4,      4,      8,      8,      8,      8,      4,      4,      4,      8,      6,      8,      4 ],
"disp": [    160,    160,    108,    258,    360,    225,    360,  146.7,  140.8,  167.6,  167.6,  275.8,  275.8,  275.8,    472,    460,    440,   78.7,   75.7,   71.1,  120.1,    318,    304,    350,    400,     79,  120.3,   95.1,    351,    145,    301,    121 ],
"hp": [    110,    110,     93,    110,    175,    105,    245,     62,     95,    123,    123,    180,    180,    180,    205,    215,    230,     66,     52,     65,     97,    150,    150,    245,    175,     66,     91,    113,    264,    175,    335,    109 ],
"drat": [    3.9,    3.9,   3.85,   3.08,   3.15,   2.76,   3.21,   3.69,   3.92,   3.92,   3.92,   3.07,   3.07,   3.07,   2.93,      3,   3.23,   4.08,   4.93,   4.22,    3.7,   2.76,   3.15,   3.73,   3.08,   4.08,   4.43,   3.77,   4.22,   3.62,   3.54,   4.11 ],
"wt": [   2.62,  2.875,   2.32,  3.215,   3.44,   3.46,   3.57,   3.19,   3.15,   3.44,   3.44,   4.07,   3.73,   3.78,   5.25,  5.424,  5.345,    2.2,  1.615,  1.835,  2.465,   3.52,  3.435,   3.84,  3.845,  1.935,   2.14,  1.513,   3.17,   2.77,   3.57,   2.78 ],
"qsec": [  16.46,  17.02,  18.61,  19.44,  17.02,  20.22,  15.84,     20,   22.9,   18.3,   18.9,   17.4,   17.6,     18,  17.98,  17.82,  17.42,  19.47,  18.52,   19.9,  20.01,  16.87,   17.3,  15.41,  17.05,   18.9,   16.7,   16.9,   14.5,   15.5,   14.6,   18.6 ],
"vs": [      0,      0,      1,      1,      0,      1,      0,      1,      1,      1,      1,      0,      0,      0,      0,      0,      0,      1,      1,      1,      1,      0,      0,      0,      0,      1,      0,      1,      0,      0,      0,      1 ],
"am": [      1,      1,      1,      0,      0,      0,      0,      0,      0,      0,      0,      0,      0,      0,      0,      0,      0,      1,      1,      1,      0,      0,      0,      0,      0,      1,      1,      1,      1,      1,      1,      1 ],
"gear": [      4,      4,      4,      3,      3,      3,      3,      4,      4,      4,      4,      3,      3,      3,      3,      3,      3,      4,      4,      4,      3,      3,      3,      3,      3,      4,      5,      5,      5,      5,      5,      4 ],
"carb": [      4,      4,      1,      1,      2,      1,      4,      2,      2,      4,      4,      3,      3,      3,      4,      4,      4,      1,      2,      1,      1,      2,      2,      4,      2,      1,      2,      2,      4,      6,      8,      2 ]
},
"facet": [ "am", "vs" ],
"type": "point",
"color": "gear"
}
],
"facet": {
 "type": "grid",
"x": "am",
"y": "vs"
},
"guides": [],
"coord": [],
"id": "chart132456ea2ce4"
}
    _.each(chartParams.layers, function(el){
        el.data = polyjs.data(el.data)
    })
    var graph_chart132456ea2ce4 = polyjs.chart(chartParams);
</script>


And with the following code permits the binding in `shiny`:

- server.R
  - Always load package `rCharts`
  - Use shiny-ui function (rCharts personalized) `renderChart({})` for generate graph
  - Must use `addParams` methode to add a parameter `dom` and give the name of `outputid` (i.e. the name when you use for ui-output function)
  - Remember to return the object rCharts by function `return()`
- ui.R
  - use `showOutput("outputid","package-name")` to display the graph in the bootstrap grid
  - "package-name" should always be in lower case and correspond to the package function used in `server.R`


```
## server.r
require(rCharts)
shinyServer(function(input, output) {
  output$myChart <- renderChart({
    names(iris) = gsub("\\.", "", names(iris))
    p1 <- rPlot(input$x, input$y, data = iris, color = "Species",
      facet = "Species", type = 'point')
    p1$addParams(dom = 'myChart')
    return(p1)
  })
})
## ui.R
require(rCharts)
shinyUI(pageWithSidebar(
  headerPanel("rCharts: Interactive Charts from R using polychart.js"),
  sidebarPanel(
    selectInput(inputId = "x",
     label = "Choose X",
     choices = c('SepalLength', 'SepalWidth', 'PetalLength', 'PetalWidth'),
     selected = "SepalLength"),
    selectInput(inputId = "y",
      label = "Choose Y",
      choices = c('SepalLength', 'SepalWidth', 'PetalLength', 'PetalWidth'),
      selected = "SepalWidth")
  ),
  mainPanel(
    showOutput("myChart", "polycharts")
  )
))
```


**More basic/advanced using example, you can visit [https://github.com/ramnathv/rCharts/tree/master/inst/libraries], and in each libraries you can find `examples.R` for detailed usage.**

## Advanced usage
You could find some advanced usages in `examples.R` file. Here, we explain how to create an object rcharts and generate a more personalized plots. The example libraries will be based on HighCharts.

Here is an example code from `examples.R` of HighCharts.

```r
a <- rCharts::Highcharts$new()
a$chart(type = "column")
a$series(data = list(list(y = 15, drilldown = list(data = c(1, 2, 3))), list(y = 20,
    drilldown = list(data = c(1, 2, 3)))), name = "test")
a$xAxis(categories = c("Brand A", "Brand B"))
a$plotOptions(column = list(cursor = "pointer", point = list(events = list(click = drill_function))))
```

As we can see:

1. We first create a rCharts object of Highcharts which use the Highcharts template: `a <- rCharts::Highcharts$new()`
2. Then we modify in the object in the list "chart", the value of type equals to "column": `a$chart(type = "column")`
3. Create a data series by adding a list of sub-lists data to "series" of a.
4. Modify xAxis labels by giving "Brand A" and "Brand B"
5. The plotOptions is modified to adapt the requirement

If you are confused, that is normal. Here the very first step is to understand how a highcharts javascript works:

<iframe width="100%" height="500" src="http://jsfiddle.net/rickyking/Pxrjh/3/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

In the above we can see in "result" we have the same plot generated by the R code. Compare it!

1. we see that `a$chart(type = "column")` is `"chart": {"type": "column", "renderTo": "chart262c158f3b56"}` (ignore "renderTo", some parameters are set by defaut in the package for certain reasons: generate a working standalone page, shiny application support, etc.)
2. `a$series` generates `"series": [{"data": [{...`, and for a detailed reference of which format of data is accepted in highcharts, we should reference to [http://api.highcharts.com/highcharts#series], and how to create them, we will see that later.
3. `a$xAxis(categories = c("Brand A", "Brand B"))` this line generates `"xAxis": [{"categories": [ "Brand A", "Brand B" ] }]`
4. for plotOptions as well

Why use highcharts as an example? The reason is quite simple: well documented [API](http://api.highcharts.com/), a [gallery](http://www.highcharts.com/demo) of examples which you can see the javascript in the live and compare your code of javascript (generated by R to this api).
