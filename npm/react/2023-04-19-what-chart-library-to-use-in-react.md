---
layout: post
author: Onur Bugdayci
title: "What chart library to use in react?"
subTitle: ""
date: 2023-04-19
categories: javascript
tags: [react]
img:
  url: chart.png
  desc: Photo by clipartmax
  origin: https://www.clipartmax.com/middle/m2H7H7d3b1G6Z5m2_line-chart-icon-line-graph-icon-png/
bigimg:
  url: chart-big.jpg
  desc: Photo by Supachai Promrit
  origin: https://www.vecteezy.com/vector-art/1846151-cryptocurrency-stock-chart-light-futuristic-banner-background
desc: >
  A showcase and comparison of some chart libraries in react
extras:
  - githubproject: https://github.com/itenium-be/
    githubtext: ""
interesting:
  - git: 
    desc: ""
toc:
  icon: icon-react
  title: Charts
package-versions:
  - package: recharts
    version: 2.5.0
  - package: visx
    version: 3.1.2
  - package: nivo
    version: 0.80.0
  - package: victory
    version: 36.6.0
  - package: react-vis
    version: 1.11.7
  - package: react-chartjs-2
    version: 5.2.0
---

The question "What chart library to use in React?" came about when I had to actually find one to use in a project. As suggested by a colleague, I took it upon myself to also create a blogpost around this, so I could potentially save some other developers or my future self in my current situation from doing the research.


<!--more-->

**The chart libraries I will be covering are ones tailored to React and this is the list**:
{% include github-stars.html url="recharts/recharts" desc="Recharts is a Redefined chart library built with React and D3" %}
{% include github-stars.html url="airbnb/visx" desc="Visx is a collection of reusable low-level visualization components. visx combines the power of d3 to generate your visualization with the benefits of react for updating the DOM." %}
{% include github-stars.html url="plouc/nivo" desc="Nivo provides supercharged React components to easily build dataviz apps, it's built on top of d3." %}
{% include github-stars.html url="FormidableLabs/victory" desc="A collection of composable React components for building interactive data visualizations. Built on D3" %}
{% include github-stars.html url="uber/react-vis" desc="A collection of react components to render common data visualization charts" %}
{% include github-stars.html url="reactchartjs/react-chartjs-2" desc="React components for Chart.js, the most popular charting library" %}

# Overview of the chart libraries

Data acquired on 19/04/2023

|                 | Package size | Weekly downloads | Rendering type | monetization| documentation | setup time demo |
|-----------------|:------------:|:----------------:|:--------------:|:-----------:|:-------------:|----------------:|
| Recharts        | 4.52 MB      | 1,278,432        | Svg            | Free/MIT License | Very good | 00:15:10 |
| Visx            | 440,89 kB    | 363,002          | Svg            | Free/MIT License | Very good | 00:28:58 |
| Nivo            | 2.81 MB      | 296,292          | Svg/HTML/Canvas| Free/MIT License | Good      | 00:28:31 |
| Victory         | 2.95 MB      | 192,203          | Svg            | Free             | Very good | 00:11:10 |
| React-vis       | 1.8 MB       | 155,768          | Svg/Canvas     | Free/MIT License | ok        | 00:34:39 |
| React-chartjs-2 | 4.88 MB      | 948,010          | Canvas         | Free/MIT License | ok        | 00:35:11 |
{: .table-code}

# Installation and Usage

In the next few sections I will go over each chart library installation and try and create a line chart

## Recharts

![Rechart line chart](/assets/blog-images/rechart.png "Pretty slick")

## Visx

![Visx line chart](/assets/blog-images/visx.png "Pretty slick")

## Nivo

![Nivo line chart](/assets/blog-images/nivo.png "Pretty slick")

## Victory

![Victory line chart](/assets/blog-images/victory.png "Pretty slick")

## React-vis

![React-vis line chart](/assets/blog-images/react-vis.png "Pretty slick")

## React-chartsj-2

![React-chartjs-2 line chart](/assets/blog-images/react-chartjs-2.png "Pretty slick")




