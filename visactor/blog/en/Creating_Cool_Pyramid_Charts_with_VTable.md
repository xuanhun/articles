---
title: Creating Cool Pyramid Charts with VTable

key words: VisActor, VChart, VTable, VStory, VMind, VGrammar, VRender, Visualization, Chart, Data, Table, Graph, Gis, LLM
---
In the field of data analysis and visualization, tables are one of the most intuitive and effective ways to display data. Today, let's explore how to use VTable, a powerful table component, to create beautiful and informative pyramid charts, and delve into the usage of various cell types in VTable, enabling your tables to "draw" amazing charts!

## Implementing Population Pyramid with VTable

The population pyramid is a classic chart form that clearly shows the gender distribution of different age groups. In VTable, we can create this chart by cleverly setting cell types and data.

### Data Preparation

Taking China's 2023 population pyramid as an example, we first need to prepare population data, including male and female population numbers for different age groups. The following is a two-dimensional array where the first column represents age, the second column represents male population, and the third column represents female population.

```
const records = [
  [0, 5634674, 5063545],
  [1, 5705095, 5104886],
  [2, 6428332, 5727205],
  // ... (same data as original)
  [100, 3722, 45558]
];
```

### Configure Column Information

Then, create a table in VTable, using age groups and gender as column labels. The key step is to set the cell type of male and female population columns to progressbar, so each cell can display a progress bar whose length represents the population of the corresponding age group and gender.

In the specific implementation, we can configure the table's column information as follows:

```
const columns = [
  {
    field: '0',
    title: 'Age',
    width: 'auto'
  },
  {
    field: '1',
    title: 'Male (Population)',
    width: 'auto',
    cellType: 'progressbar',
    min: 0,
    max: 13601860 + 1000000,
  },
  {
    field: '2',
    title: 'Female (Population)',
    width: 'auto',
    cellType: 'progressbar',
    min: 0,
    max: 13601860 + 1000000,
  },
  {
    field: '0',
    title: 'Age',
    width: 'auto',
  }
];
```

In the above configuration, both the first and last columns are age groups, the second column is male population, and the third column is female population. Our data is a two-dimensional array with each age from `0-100` corresponding to one row of data, so we set the array index directly to the `field` in the column configuration. The `cellType` of the second and third columns is set to `'progressbar'` to display as data bars. The data bars need to be configured with `{min: number, max: number}` to specify the data range. For max, I chose the maximum value in records, and added a `buffer: 1000000` to make it look better overall with padding from the edges.

Current display effect:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Zs10bEE1ToAKTgxo8HRcUALynBK.gif' alt='' width='496' height='auto'>

### Optimize Display Effect

First, optimize the crowded text issue due to the small row height (`defaultRowHeight: 6` is only 6px). To optimize the incomplete display of age numbers on both sides, enable `mergeCell: true`. This configuration will merge cells with the same content above and below. But how about when each row's data is different? We force adjacent five rows to have the same content through fieldFormat:

```
    fieldFormat(args) {
      if (args[0] >= 100) {
        return 100;
      }
      return Math.round(args[0] / 5) * 5;
    },
```

For the second and third columns, we don't want to display data, also using fieldFormat:

```
    fieldFormat(args) {
      return '';
    }
```

Current effect after optimization:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/FAZKbucAboQSfXxmYJLcPwYSnFg.gif' alt='' width='499' height='auto'>

Continue to optimize the data bar style. First, make the second column's data bars right-aligned by setting style:

```
    style: {
      barRightToLeft: true
    }
```

After optimization:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/AXwwbb76Yo3crpxotCXctGvMnLd.gif' alt='' width='495' height='auto'>

After optimizing the colors of data bars on both sides, the final configuration for these two columns is:

```
{
    field: '1',
    title: 'Male (Population)',
    width: 'auto',
    fieldFormat(args) {
      return '';
    },
    cellType: 'progressbar',
    min: 0,
    max: 13601860 + 1000000,
    style: {
      barHeight: 5,
      barColor: '#4cba72',
      barRightToLeft: true
    }
  },
  {
    field: '2',
    title: 'Female (Population)',
    width: 'auto',
    cellType: 'progressbar',
    fieldFormat(args) {
      return '';
    },
    min: 0,
    max: 13601860 + 1000000,
    style: {
      barColor:'#f66d6c',
      barHeight: 5,
    }
  },
```

Other border and header cell background color and text style configurations are in the theme:

```
  theme: VTable.themes.DEFAULT.extends({
    headerStyle: {
      color: '#dfeef7',
      bgColor: '#038dc8'
    },
    frameStyle: {
      borderColor: '#1d80b0',
      borderLineWidth: 2
    }
  })
```

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/ScAnbIDmzoRXIqxBMB4cE2KdnIf.gif' alt='' width='500' height='auto'>

Through the above code, we can see that the male and female population numbers for each age group are displayed with progress bars in different colors, forming an intuitive population pyramid chart.

### Chart Title

A complete chart needs a concise and accurate title that precisely conveys its core information to clearly communicate its meaning. For the powerful VTable, this is a piece of cake. We only need to easily add title configuration to the existing configuration to give the chart an appropriate and comprehensive title, making the chart's meaning clear at a glance, allowing users to quickly capture key information, greatly improving the chart's readability and practicality.

```
title: {
    text: 'China Population 2023 Pyramid: 1,425,849,286',
    padding: 10,
    orient: 'top',
    textStyle: { fill: '#16cdcd' }
  },
```

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/GyQfbJqXKoaN4mxiL6ScsLInn8f.gif' alt='' width='497' height='auto'>

The complete example code can be found on the official website: https://visactor.com/vtable/demo/business/population-pyramid

## Using Progressbar Type in VTable

Progress bars play an important role in various applications, intuitively showing users task progress. The `progressbar` type provided by VTable offers rich options that, combined with other table capabilities, allow developers to create unique progress bars according to specific needs and design styles.

Let's look at the key configurations in detail:

* **min**: Minimum data value for progress bar display, default is 0.

* **max**: Maximum data value for progress bar display, default is 100.

* **barType**: Progress bar type, default is 'default'. Available values include:
  * 'default': Normal progress bar
  * 'negative': Progress bar considering negative values, showing progress bars in both positive and negative directions with 0 as the divider
  * 'negative_no_axis': Similar to 'negative' but without 0 value axis

* **dependField**: If the text displayed in the cell and the data field used by the progress bar need to be different, configure the data field used by the progress bar in `dependField`.

Additionally, the progressbar type has many exclusive style configuration items, such as `barColor`, `barHeight`, `barAxisColor`, `barMarkXXX`, etc. Through these configuration items, we can further beautify the appearance of progress bars.

## Introduction to Other Cell Types in VTable

Besides the progressbar type, VTable provides various rich cell types, each with unique characteristics and functions, allowing us to flexibly choose according to different data display needs.

Here's a brief introduction:

* **text**: The most basic cell type, used to display text information. In tables, we can use the text type to display row labels, column labels, and some auxiliary explanatory text to help audiences better understand the meaning of data.

* **link**: Used to display link-type data. Through setting different formatting functions, styles, and click jump addresses, whether to check link validity, etc., we can flexibly display and handle links.

* **image**: Allows us to insert images in cells. If our data is related to images, such as product images, geographic location images, etc., using the image type can intuitively display this information, enhancing the table's visual effect.

* **video**: Similar to the image type, used to display video-type data, with all configuration items the same as image.

* **sparkline**: Used to display sparkline-type data. By setting specific `sparklineSpec` configuration items for sparklines, we can display various trends, distributions, and other mini charts, providing more possibilities for data visualization.

* **chart**: Used to display chart-type data. Before using this type, you need to first register a chart type through `VTable.chart.register`. After registration, you can display rich chart content through configuration items like `chartModule` and `chartSpec`.

* **checkbox**: Checkbox type cells are widely used in many applications, including task management, data filtering, permission settings, etc. Whether the checkbox cell is in a checked state defaults to false and supports configuring functions with different configurations for different cells.

* **radio**: Radio button type

Below is a screenshot of the demo list showing various types from the official website for reference! Don't you feel its functionality is quite powerful? From these demos, we can clearly feel the convenience and efficiency brought by its powerful functions. Whether in data display or data analysis scenarios, it can play a significant role, providing us with quality experience and solutions.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/SuKNbbpxDofcJkxz1oUczYAOnYg.gif' alt='' width='897' height='auto'>

## Summary

By reasonably using various cell types in VTable, we can create feature-rich and diverse table charts. Whether it's an intuitive population pyramid or other complex data visualization needs, VTable can provide powerful support. Go ahead and try it out, let your tables also "draw" a wonderful world of charts! 