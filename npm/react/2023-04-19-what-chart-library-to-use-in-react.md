---
layout: post
author: Onur Bugdayci
title: "What chart library to use in React?"
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
  A showcase and comparison of some chart libraries in React.
extras:
  - githubproject: https://github.com/itenium-be/React-Charting
    githubtext: "Project with all the examples for the React chart libraries"
toc:
  icon: fa-react
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
{% include github-stars.html url="airbnb/visx" desc="Visx is a collection of reusable low-level visualization components. Visx combines the power of d3 to generate your visualization with the benefits of React for updating the DOM." %}
{% include github-stars.html url="plouc/nivo" desc="Nivo provides supercharged React components to easily build dataviz apps, it's built on top of d3." %}
{% include github-stars.html url="FormidableLabs/victory" desc="A collection of composable React components for building interactive data visualizations. Built on D3" %}
{% include github-stars.html url="uber/react-vis" desc="A collection of React components to render common data visualization charts" %}
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

In the next few sections I will go over each chart library installation and try and detail my line chart creations.
I chose to create a line chart because it is the relevant type for the project I'm working on. My goal was to see how long
and difficult it would be to create and display a line chart given a particular dataset. 
The setup times to create and display these charts can be found in the overview table above.

**I used the following dataset structure**:
```json
{
  persons: [
    {
      id: 1,
      name: "Bob",
      age: 11,
    },
    {
      id: 2,
      name: "Tom",
      age: 13,
    },
    {
      id: 3,
      name: "Bruno",
      age: 56,
    },
    {
      id: 4,
      name: "Kyoe",
      age: 33,
    },
    {
      id: 5,
      name: "Borat",
      age: 333,
    },
    {
      id: 6,
      name: "Sasha",
      age: 68,
    },
    {
      id: 7,
      name: "Onur",
      age: 27
    }
  ],
}
```

## Recharts

Installation: `npm install --save recharts`

**Line chart**:
![Rechart line chart](/assets/blog-images/rechart.png "Pretty slick")

**Code**:
```ts
import { shallowEqual, useSelector } from "react-redux";
import { CartesianGrid, Legend, Line, LineChart, Tooltip, XAxis, YAxis } from "recharts";

export function Recharts() {
  const persons: IPerson[] = useSelector(
    (state: PersonState) => state.persons,
    shallowEqual // checks that two different variables reference the same object
  );

  return (
    <>
    <LineChart width={500}
          height={300}
          data={persons}
          margin={{
            top: 5,
            right: 30,
            left: 20,
            bottom: 5,
          }}> 
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="name" />
        <YAxis />
        <Tooltip />
        <Legend />
        <Line type="monotone" dataKey="age" stroke="#8884d8" />
    </LineChart>
    </>
  );
}
```

Usage of Recharts was a smooth experience. The setup time could probably have been shorter had I worked with charts in a
React TypeScript environment before. It already does a lot of stuff out-of-the-box without a lot of configuration or any configuration like the tooltip and legend. 


## Visx

Installation: `npm install --save @visx/xychart`

**Line chart**:
![Visx line chart](/assets/blog-images/visx.png "Pretty slick")

**Code**:
```ts
import { Axis, Grid, LineSeries, Tooltip, XYChart } from "@visx/xychart";
import { shallowEqual, useSelector } from "react-redux";

export function Visx() {
  const persons: IPerson[] = useSelector(
    (state: PersonState) => state.persons,
    shallowEqual
  );

  const accessors = {
    xAccessor: (d ?: IPerson) => d && d.name,
    yAccessor: (d ?: IPerson) => d && d.age,
  };

  return (
    <>
      <XYChart
        width={500}
        height={300}
        xScale={{ type: "band" }}
        yScale={{ type: "linear" }}
      >
        <Axis orientation="bottom" />
        <Axis orientation="left" />
        <Grid columns={false} numTicks={4} />
        <LineSeries dataKey="Line 1" data={persons} {...accessors} />
        <Tooltip<IPerson>
          snapTooltipToDatumX
          snapTooltipToDatumY
          showVerticalCrosshair
          showSeriesGlyphs
          renderTooltip={({ tooltipData, colorScale }) => (
            <div>
              <div style={{ color: colorScale?.(tooltipData?.nearestDatum?.key!) }}>
                {tooltipData?.nearestDatum?.key}
              </div>
              {accessors.xAccessor(tooltipData?.nearestDatum?.datum)}
              {", "}
              {accessors.yAccessor(tooltipData?.nearestDatum?.datum)}
            </div>
          )}
        />
      </XYChart>
    </>
  );
}
```

The x and y axis configuration was done in a bit of a roundabout way, but the main problem and what took the longest time to set correctly was the tooltip. 
For some reason the library makes the tooltip data object use properties that have no relevance to the data being inserted in the chart.
It assumes all data inserted are datum objects whereas in this case they are person objects. The documentation also neglects to mention this in their example.

## Nivo

Installation: `npm install --save @nivo/core @nivo/line`

**Line chart**:
![Nivo line chart](/assets/blog-images/nivo.png "Pretty slick")

**Code**:
```ts
import { Line } from "@nivo/line";
import { shallowEqual, useSelector } from "react-redux";

export function Nivo() {
  const persons: IPerson[] = useSelector(
    (state: PersonState) => state.persons,
    shallowEqual
  );
  const data: any[] = persons.map((person) => {
    return { x: person.name, y: person.age };
  });

  return (
    <>
      <Line
        width={500}
        height={300}
        curve="monotoneX"
        margin={{ top: 20, right: 20, bottom: 60, left: 80 }}
        enableSlices="x"
        data={[{ id: "persons", data: data }]}
        xScale={{
          type: "band",
        }}
        yScale={{ type: "linear" }}
        axisLeft={{
          legend: "linear scale",
          legendOffset: 12,
        }}
        axisBottom={{
          legend: "linear scale",
          legendOffset: -12,
        }}
      />
    </>
  );
}
```
My main gripe and time sink with this package was the issue that the data properties needed to be called x and y respectively,
so I needed to map my dataset to fit this mold. A simple setting for setting what properties you want to use on which axis would have been nice. Big visual issue however as you can probably tell is that the values don't exactly show correctly on the x axis.

## Victory

Installation: `npm install --save victory`

**Line chart**:
![Victory line chart](/assets/blog-images/victory.png "Pretty slick")

**Code**:
```ts
import { shallowEqual, useSelector } from "react-redux";
import { VictoryChart, VictoryContainer, VictoryLine } from "victory";

export function Victory() {
  const persons: IPerson[] = useSelector(
    (state: PersonState) => state.persons,
    shallowEqual
  );
  const data: any[] = persons.map((person) => {
    return { x: person.name, y: person.age };
  });

  return (
    <>
      <VictoryChart
        height={300}
        width={500}
        containerComponent={<VictoryContainer responsive={false} />}
      >
        <VictoryLine data={data} />
      </VictoryChart>
    </>
  );
}
```

Usage was relatively easy, but it has the same limitation as the Nivo library because you also need to map your data properties to x and y. Also visually it doesn't look as pleasing as some of the other charts in this list.

## React-vis

Installation: `npm install --save react-vis`

**Line chart**:
![React-vis line chart](/assets/blog-images/react-vis.png "Pretty slick")

**Code**:
```ts
import { shallowEqual, useSelector } from "react-redux";
import {
  XYPlot,
  XAxis,
  YAxis,
  HorizontalGridLines,
  LineSeries,
} from "react-vis";

export function ReactVis() {
  const persons: IPerson[] = useSelector(
    (state: PersonState) => state.persons,
    shallowEqual
  );
  const data: any[] = persons.map((person) => {
    return { x: person.name, y: person.age };
  });

  return (
    <>
      <XYPlot width={500} height={300} xType="ordinal">
        <HorizontalGridLines />
        <XAxis />
        <YAxis />
        <LineSeries
          color='red'
          data={data}
        />
      </XYPlot>
    </>
  );
}
```
I could not get this chart to render remotely well. The documentation did not really help either in this case. Also I needed to map the data properties to x and y as well again. 

## React-chartjs-2

Installation: `npm install --save react-chartjs-2 chart.js`

**Line chart**:
![React-chartjs-2 line chart](/assets/blog-images/react-chartjs-2.png "Pretty slick")

**Code**:
```ts
import { shallowEqual, useSelector } from "react-redux";
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend,
} from "chart.js";
import { Line } from "react-chartjs-2";

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
);

export const options = {
  responsive: false,
  plugins: {
    legend: {
      position: "top" as const,
    },
    title: {
      display: true,
      text: "Chart.js Line Chart",
    },
  },
};

export function ReactChartJS2() {
  const persons: IPerson[] = useSelector(
    (state: PersonState) => state.persons,
    shallowEqual
  );
  const labels: string[] = persons.map((person) => person.name);
  const ages: number[] = persons.map((person) => person.age);
  const data = {
    labels,
    datasets: [
      {
        label: "Dataset 1",
        data: ages,
        borderColor: "rgb(255, 99, 132)",
        backgroundColor: "rgba(255, 99, 132, 0.5)",
      },
    ],
  };

  return (
    <>
      <Line width={500} height={300} options={options} data={data} />
    </>
  );
}

```

Documentation for this library was kind of limited, especially when it came to creating a line chart.
An annoying trait is that you have to split your dataset up in into a labels array for the x axis and data array for the y axis.
The end result does look nice however.

# Conclusion

First time was a charm, the first library Recharts did everything I wanted it to in a clear and convenient manner. 
It's the only library where I didn't really have a complaint. It's easy to use and has very good documentation. 
