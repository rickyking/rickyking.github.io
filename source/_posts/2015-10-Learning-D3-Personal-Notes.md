---
title: "Learning D3 - Personal Notes"
date: 2015-10-19 12:35:33
tags: [javascript, d3, visualization]
categories: [Notes]
---

## Brief introduction

D3.js is the library most used for artist level Data Visualization. For a basic / easy way of doing data visualization with javascript please see [highcharts](http://www.highcharts.com) or [ECharts](http://echarts.baidu.com/).

## Notes

**JQuery like manipulation**

```html
<html>
  <head>
        <meta charset="utf-8">
        <title>HelloWorld</title>
  </head>
    <body>
        <p>Hello World 1</p>
        <p>Hello World 2</p>
        <script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
        <script>  
        d3.select("body").selectAll("p").text("http://jinyi.me");      
        </script>
    </body>
</html>
```

**Choose elements and bind the data**

```javascript
var body = d3.select("body"); //selection
var p1 = body.select("p");      //selection
var p = body.selectAll("p");    //selection
var svg = body.select("svg");   //selection
var rects = svg.selectAll("rect");  //selection
```

- datum()：bind one data to elements
- data()： bind an array to elements

```javascript
var dataset = ["I like dog","I like cat","I like snake"];
var body = d3.select("body");
var p = body.selectAll("p");

p.data(dataset)
  .text(function(d, i){
      return d;
  });
```

**SVG Canvas & Scales & Axis**

D3.js uses SVG as canvas to draw.

```javascript
var width = 300;  //canvas width
var height = 300;   //canvas heigth

var svg = d3.select("body")     
    .append("svg")          //add a svg element
    .attr("width", width)       //set width
    .attr("height", height);    //set height

var dataset = [ 2.5 , 2.1 , 1.7 , 1.3 , 0.9 ]; // data set for histogram

var linear = d3.scale.linear()
        .domain([0, d3.max(dataset)])
        .range([0, 250]); // linear scale to rescale the pixels

var axis = d3.svg.axis() // axis
     .scale(linear)      // scale for axis
     .orient("bottom")   // scale's orientation
     .ticks(7);          // scales' ticks

svg.append("g") // draw axis with tag 'g'
  .attr("class","axis")
  .attr("transform","translate(20,130)")
  .call(axis); // call accept a fuction as parameter and it gives itself as parameter

var rectHeight = 25;   //height of each rectangle

svg.selectAll("rect")
    .data(dataset)
    .enter()
    .append("rect") // above code create if not enough "rect" tags
    .attr("x",20)
    .attr("y",function(d,i){
         return i * rectHeight;
    }) // position (x,y) of left upper corner
    .attr("width",function(d){
         return linear(d); // scale is used here
    })
    .attr("height",rectHeight-2)
    .attr("fill","steelblue");
```

**Animation**

```javascript
var svg = d3.select('body')
        .append('svg');
var circle1=svg.append('circle')
        .attr("cx", 100)
        .attr("cy", 100)
        .attr("r", 45)
        .style("fill","green"); // create a circle

circle1.transition() // initialize the transition, afterward is the new state
        .duration(2000) // duration for the transition
        .ease('linear') // Ways of transition: linear, circle, elastic, bounce
        .attr("cx", 300)
        .style('fill','red')
        .attr('r', 25);
```

Or `delay()` which is used for a transformation delay.

**User interaction**

Use function `.on('eventlisener', func(){})`.

**Layout**

A list of bult-in layout:

- Bundle - apply Holten's hierarchical bundling algorithm to edges.
- Chord - produce a chord diagram from a matrix of relationships.
- Cluster - cluster entities into a dendrogram.
- Force - position linked nodes using physical simulation.
- Hierarchy - derive a custom hierarchical layout implementation.
- Histogram - compute the distribution of data using quantized bins.
- Pack - produce a hierarchical layout using recursive circle-packing.
- Partition - recursively partition a node tree into a sunburst or icicle.
- Pie - compute the start and end angles for arcs in a pie or donut chart.
- Stack - compute the baseline for each series in a stacked bar or area chart.
- Tree - position a tree of nodes tidily.
- Treemap - use recursive spatial subdivision to display a tree of nodes.

## Reference

- D3.js basic example: [http://d3js.org/]()
- D3.js wiki: [https://github.com/mbostock/d3/wiki]()
- Tutorials: [https://github.com/mbostock/d3/wiki/Tutorials]()
- Reference tutorial in chinese: [http://wiki.jikexueyuan.com/project/d3wiki/]()
