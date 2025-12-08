---
title: VChart Extension New Feature: Unlock Data Regression and Trend Analysis with One Line of Code

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
## New Member of VChart Extension Package: Unlock the Secrets of Data Trends with One Line of Code

In the field of data analysis and visualization, we often want more than just displaying discrete data points—we crave insights into the underlying trends and patterns. Whether tracking the growth momentum of core product metrics, analyzing conversion effects of different strategies in A/B tests, or searching for potential relationships between key variables during business reviews, a smooth "trend line" always makes data stories clearer and more insightful.

To make trend analysis unprecedentedly simple, the VisActor VChart team has officially launched the **Regression Line** feature in the `vchart-extension` package. Now, with just one simple line of configuration, you can add powerful data regression and trend visualization capabilities to your charts.

<div style="padding:5px;background-color: rgb(245, 246, 247);border-color: rgb(242, 243, 245);">**Core Value**: The VChart regression line extension aims to help users quickly and accurately overlay statistical regression lines on existing charts (such as scatter plots, line charts, bar charts, etc.), easily revealing potential relationships and development trends between data variables, providing intuitive references for data-driven decisions.
</div>
## Core Capabilities Overview

VChart's regression line extension is powerful and flexible, designed to meet various needs from quick exploration to in-depth analysis.

*  **Rich Regression Types**: Built-in multiple commonly used regression algorithms to meet analysis needs in different scenarios.

*  **Linear Regression (linear)**: Explore linear relationships between variables.

*  **Polynomial Regression (polynomial)**: Fit nonlinear complex trends with support for custom degrees.

*  **Logarithmic Regression (logarithmic)**: Suitable for scenarios where growth rates are fast initially and slow later.

*  **Exponential Regression (exponential)**: Model trends where data grows exponentially.

*  **Loess Regression (loess)**: Locally weighted regression, excellent at capturing local data change trends.

*  **Kernel Density Estimation (KDE)**: Overlay smooth probability density curves on histograms.

*  **Empirical Cumulative Distribution (ECDF)**: Display the cumulative distribution of data.

*  **Seamless Integration with Multiple Chart Types**: Can be easily combined with the most commonly used chart types in VChart.

*  **Scatter Plot**: The classic scenario for regression analysis, intuitively displaying the fitting trend of two continuous variables.

*  **Bar Chart**: Add overall trend references for discrete categorical data.

*  **Histogram**: Combined with KDE or ECDF, gain deeper understanding of data distribution characteristics.

*  **Support for Grouped/Categorical Regression**: When data contains multiple categories, regression lines can be calculated and drawn independently for each group, facilitating detailed comparative analysis.

*  **Highly Customizable Styles**: Styles of regression lines, formula labels, confidence intervals, and other elements all support fine-grained configuration, ensuring visual effects perfectly blend with the overall design style.

## Quick Start: Three Steps to Add Regression Lines to Scatter Plots

Adding regression lines to charts is very simple. Follow these three steps to enable this feature in your VChart project.

### Step 1: Install and Import the Extension Package

First, make sure you have installed VChart and its extension package in your project.

```bash
# Using npm
npm install @visactor/vchart @visactor/vchart-extension

# Using yarn
yarn add @visactor/vchart @visactor/vchart-extension
    

```

### Step 2: Register the Regression Line Component

Import and register the regression line component at your code entry point. This is the key to enabling all related features.

```TypeScript
import VChart from '@visactor/vchart';
import { registerRegressionLine } from '@visactor/vchart-extension';

// Only need to call once
registerRegressionLine();
    

```

### Step 3: Add Regression Line to Chart Configuration

After registration, you can add regression lines in the chart spec through the `append*RegressionLineConfig` series of helper functions. Taking the most common scatter plot as an example:

```TypeScript
import { appendScatterRegressionLineConfig } from '@visactor/vchart-extension';

// Assuming you already have a basic scatter plot spec
const spec = {
  type: 'scatter',
  data: {
    values: [
      { name: 'chevrolet chevelle malibu', milesPerGallon: 18, cylinders: 8, horsepower: 130 },
  { name: 'buick skylark 320', milesPerGallon: 15, cylinders: 8, horsepower: 165 },
  { name: 'plymouth satellite', milesPerGallon: 18, cylinders: 8, horsepower: 150 },
      // ... more data
    ]
  },
  xField: 'milesPerGallon',
  yField: 'horsepower',
};

// Add regression line configuration to spec
appendScatterRegressionLineConfig(spec, {
  type: 'linear' // Specify regression type as linear regression
});

// Now the spec contains regression line configuration and can be used for VChart rendering
const vchart = new VChart(spec, { dom: 'chart-container' });
vchart.renderSync();
    

```
It's that simple! The rendered chart will automatically draw a linear regression line on top of the original scatter plot.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/SvoHbWXtNo2kgYxz890clUO4nme.gif' alt='' width='988' height='auto' />

## Advanced Configuration and Examples

The VChart regression line extension provides rich configuration options, allowing you to finely control the behavior and appearance of regression lines.

### Key Configuration Options Explained

When using the `append*RegressionLineConfig` function, the second parameter `config` object supports the following key properties:

*  `type`: Specify the regression type. For example, in scatter plots, you can choose `'linear'`, `'polynomial'`, `'logarithmic'`, `'exponential'`, `'loess'`. In histograms, you can choose `'kde'`, `'ecdf'`.

*  `degree` (or `polynomialDegree`): When `type` is `'polynomial'`, used to specify the degree of the polynomial, default is 2. Higher degrees allow the curve to better fit data fluctuations, but risk overfitting.

*  `line`: Used to configure the style of the regression line, such as `style` (line type, color, thickness, etc.).

*  `label`: Configure the formula label at the end or top of the regression line, can customize `text` and `style`.

*  `confidenceInterval`: Configure the confidence interval, control its visibility through `visible`, and customize its fill style through `style`.

### Example: Style Customization

The following example will modify the `type` to implement a 3rd-degree polynomial regression line based on the previous scatter plot and customize the regression line style.

```TypeScript
import { appendScatterRegressionLineConfig } from '@visactor/vchart-extension';

// Assuming you already have a basic scatter plot spec
const spec = {
  type: 'scatter',
  data: {
    values: [
      { name: 'chevrolet chevelle malibu', milesPerGallon: 18, cylinders: 8, horsepower: 130 },
  { name: 'buick skylark 320', milesPerGallon: 15, cylinders: 8, horsepower: 165 },
  { name: 'plymouth satellite', milesPerGallon: 18, cylinders: 8, horsepower: 150 },
      // ... more data
    ]
  },
  xField: 'milesPerGallon',
  yField: 'horsepower',
};

// Add regression line configuration to spec
appendScatterRegressionLineConfig(spec, {
  type: 'polynomial', // Supports 4 types: 'linear' | 'logistic' | 'lowess' | 'polynomial'
    polynomialDegree: 3,
    color: 'red',
    line: {
      style: {
        lineWidth: 2
      }
    },
    confidenceInterval: {
      style: {
        fillOpacity: 0.2
      }
    },
    label: {
      text: '3rd Degree Polynomial Regression'
    }
});

// Now the spec contains regression line configuration and can be used for VChart rendering
const vchart = new VChart(spec, { dom: 'chart-container' });
vchart.renderSync();
    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/XS9xbbaS3ocOBPxK7gSc36FenNh.gif' alt='' width='956' height='auto' />

### Example: Histogram and Kernel Density Estimation (KDE)

The regression line extension can also enhance the expressiveness of histograms. By overlaying KDE curves, you can more smoothly observe the "shape" of data distribution.

```TypeScript
// This is a histogram spec with bin transformation
const spec = {
  type: 'histogram',
  data: { /* ... */ },
  type: 'histogram',
  xField: 'x0',
  x2Field: 'x1',
  yField: 'frequency',
};

// Attach KDE curves to it
appendHistogramRegressionLineConfig(spec, [
  {
    type: 'kde', // Supports 'kde' and 'ecdf'
    line: {
      style: {
        stroke: 'red',
        lineWidth: 2
      }
    },
    label: {
      text: 'KDE Kernel Density Estimation'
    }
  },
  {
    type: 'ecdf', // Supports 'kde' and 'ecdf'
    line: {
      style: {
        stroke: 'green',
        lineWidth: 2
      }
    },
    label: {
      text: 'Empirical Cumulative Distribution Function (ECDF)'
    }
  }
]);
    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/LTErbdPNVoWNUVxweNacM8m2nTe.gif' alt='' width='1000' height='auto' />

### Online Demo and Tutorial

Demo: https://visactor.com/vchart/demo/scatter-chart/scatter-regression-line

Tutorial: https://visactor.com/vchart/guide/tutorial_docs/Chart_Extensions/regression_line

### Welcome to Connect

Finally, we sincerely welcome all friends interested in data visualization to join us and participate in the open-source development of VisActor:

**VTable**: [VTable Official Website](https://link.juejin.cn?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)、[VTable Github (Welcome to Star)](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FVisActor%2FVTable)

VisActor Official Website: [www.visactor.io/](https://link.juejin.cn?target=http%3A%2F%2Fwww.visactor.io%2F) or [www.viactor.com](https://link.juejin.cn?target=http%3A%2F%2Fwww.viactor.com)

Discord: [discord.gg/3wPyxVyH6m](https://link.juejin.cn?target=http%3A%2F%2Fdiscord.gg%2F3wPyxVyH6m)

Feishu Group (External Network): [Open Link to Scan QR Code](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F40dcf4e6722d4925804361a2269991d8~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D264%26h%3D277%26s%3D35808%26e%3Dpng%26b%3Dfdfdfd)

WeChat Official Account: [Open Link to Scan QR Code](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2Ff28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D258%26h%3D258%26s%3D8552%26e%3Dwebp%26b%3Dfefefe)

GitHub: [github.com/VisActor](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FVisActor%2F)

