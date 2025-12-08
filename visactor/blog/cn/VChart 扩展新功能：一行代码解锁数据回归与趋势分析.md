---
title: VChart 扩展新功能：一行代码解锁数据回归与趋势分析    

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
## VChart 扩展包新成员：一行代码解锁数据趋势的秘密

在数据分析和可视化领域，我们常常不仅满足于展示离散的数据点，更渴望洞察其背后的趋势与规律。无论是追踪产品核心指标的增长势头，分析 A/B 实验中不同策略的转化效果，还是在业务复盘时寻找关键变量间的潜在关联，一条平滑的“趋势线”总能让数据故事更加清晰和富有洞察力。    

为了让趋势分析变得前所未有的简单，VisActor VChart 团队在扩展包 `vchart-extension` 中正式推出了**回归线（Regression Line）**功能。现在，你只需一行简单的配置，即可为你的图表增添强大的数据回归与趋势展示能力。    

<div style="padding:5px;background-color: rgb(245, 246, 247);border-color: rgb(242, 243, 245);">**核心价值**：VChart 回归线扩展旨在帮助用户快速、准确地在现有图表（如散点图、折线图、柱状图等）上叠加统计回归线，轻松揭示数据变量间的潜在关系和发展趋势，为数据驱动的决策提供直观参考。    
</div>
## 核心能力一览

VChart 的回归线扩展功能强大且灵活，旨在满足从快速探索到深度分析的各类需求。    

*  **丰富的回归类型**：内置多种常用回归算法，满足不同场景的分析需求。    

*  **线性回归 (linear)**：探索变量间的线性关系。    

*  **多项式回归 (polynomial)**：拟合非线性的复杂趋势，支持自定义阶数。    

*  **对数回归 (logarithmic)**：适用于增长率先快后慢的场景。    

*  **指数回归 (exponential)**：模拟数据呈指数级增长的趋势。    

*  **Loess 回归 (loess)**：局部加权回归，擅长捕捉局部的数据变化趋势。    

*  **核密度估计 (KDE)**：在直方图上叠加平滑的概率密度曲线。    

*  **经验累积分布 (ECDF)**：展示数据的累积分布情况。    

*  **无缝集成多种图表**：可轻松与 VChart 中最常用的图表类型结合。    

*  **散点图**：回归分析的经典场景，直观展示两个连续变量的拟合趋势。    

*  **柱状图**：为离散的分类数据添加整体趋势参考。    

*  **直方图**：结合 KDE 或 ECDF，深入理解数据分布特征。    

*  **支持分组/分类回归**：当数据包含多个类别时，可以为每个分组独立计算并绘制回归线，便于进行精细化的对比分析。    

*  **高度可定制的样式**：回归线、公式标签、置信区间等元素的样式均支持精细化配置，确保视觉效果与整体设计风格完美融合。    

## 快速上手：三步为散点图添加回归线

为图表添加回归线非常简单。遵循以下三步，即可在你的 VChart 项目中启用该功能。    

### 第一步：安装并引入扩展包

首先，请确保你的项目中已安装 VChart 及其扩展包。    

```bash
# 使用 npm
npm install @visactor/vchart @visactor/vchart-extension

# 使用 yarn
yarn add @visactor/vchart @visactor/vchart-extension
    

```
### 第二步：注册回归线组件

在你的代码入口处，引入并注册回归线组件。这是启用所有相关功能的关键。    

```TypeScript
import VChart from '@visactor/vchart';
import { registerRegressionLine } from '@visactor/vchart-extension';

// 只需调用一次
registerRegressionLine();
    

```
### 第三步：在图表配置中添加回归线

完成注册后，即可在图表 spec 中通过 `append*RegressionLineConfig` 系列辅助函数来添加回归线。以最常见的散点图为例：    

```TypeScript
import { appendScatterRegressionLineConfig } from '@visactor/vchart-extension';

// 假设你已有一个基础的散点图 spec
const spec = {
  type: 'scatter',
  data: {
    values: [
      { name: 'chevrolet chevelle malibu', milesPerGallon: 18, cylinders: 8, horsepower: 130 },
  { name: 'buick skylark 320', milesPerGallon: 15, cylinders: 8, horsepower: 165 },
  { name: 'plymouth satellite', milesPerGallon: 18, cylinders: 8, horsepower: 150 },
      // ... 更多数据
    ]
  },
  xField: 'milesPerGallon',
  yField: 'horsepower',
};

// 为 spec 添加回归线配置
appendScatterRegressionLineConfig(spec, {
  type: 'linear' // 指定回归类型为线性回归
});

// 现在，spec 就包含了回归线配置，可以用于 VChart 渲染了
const vchart = new VChart(spec, { dom: 'chart-container' });
vchart.renderSync();
    

```
就是这么简单！渲染后的图表将在原始散点图的基础上，自动绘制出一条线性回归线。    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/SvoHbWXtNo2kgYxz890clUO4nme.gif' alt='' width='988' height='auto' />

## 进阶配置与示例

VChart 回归线扩展提供了丰富的配置项，让你能够精细控制回归线的行为和外观。    

### 关键配置项解析

在使用 `append*RegressionLineConfig` 函数时，第二个参数 `config` 对象支持以下关键属性：    

*  `type`: 指定回归类型。例如，在散点图中可选 `'linear'`, `'polynomial'`, `'logarithmic'`, `'exponential'`, `'loess'`。在直方图中可选 `'kde'`, `'ecdf'`。    

*  `degree` (或 `polynomialDegree`): 当 `type` 为 `'polynomial'` 时，用于指定多项式的阶数，默认为 2。阶数越高，曲线越能拟合数据的波动，但有过拟合的风险。    

*  `line`: 用于配置回归线的样式，如 `style`（线型、颜色、粗细等）。    

*  `label`: 配置回归线末端或顶部的公式标签，可以自定义 `text` 和 `style`。    

*  `confidenceInterval`: 配置置信区间，通过 `visible` 控制其显隐，并通过 `style` 自定义其填充样式。    

### 示例：样式定制

下面的例子将在前述散点图的基础上，修改 `type` 实现3次多项式回归线，并自定义回归线样式。    

```TypeScript
import { appendScatterRegressionLineConfig } from '@visactor/vchart-extension';

// 假设你已有一个基础的散点图 spec
const spec = {
  type: 'scatter',
  data: {
    values: [
      { name: 'chevrolet chevelle malibu', milesPerGallon: 18, cylinders: 8, horsepower: 130 },
  { name: 'buick skylark 320', milesPerGallon: 15, cylinders: 8, horsepower: 165 },
  { name: 'plymouth satellite', milesPerGallon: 18, cylinders: 8, horsepower: 150 },
      // ... 更多数据
    ]
  },
  xField: 'milesPerGallon',
  yField: 'horsepower',
};

// 为 spec 添加回归线配置
appendScatterRegressionLineConfig(spec, {
  type: 'polynomial', // 支持4中类型 'linear' | 'logisitc' | 'lowess' | 'polynomial'
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
      text: '3次多项式回归'
    }
});

// 现在，spec 就包含了回归线配置，可以用于 VChart 渲染了
const vchart = new VChart(spec, { dom: 'chart-container' });
vchart.renderSync();
    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/XS9xbbaS3ocOBPxK7gSc36FenNh.gif' alt='' width='956' height='auto' />

### 示例：直方图与核密度估计（KDE）

回归线扩展同样能增强直方图的表现力。通过叠加 KDE 曲线，可以更平滑地观察数据分布的“形状”。    

```TypeScript
// 这是一个包含 bin 转换的直方图 spec
const spec = {
  type: 'histogram',
  data: { /* ... */ },
  type: 'histogram',
  xField: 'x0',
  x2Field: 'x1',
  yField: 'frequency',
};

// 为其附加 KDE 曲线
appendHistogramRegressionLineConfig(spec, [
  {
    type: 'kde', // 支持 'kde' 和 'ecdf'
    line: {
      style: {
        stroke: 'red',
        lineWidth: 2
      }
    },
    label: {
      text: 'KDE核密度估计'
    }
  },
  {
    type: 'ecdf', // 支持 'kde' 和 'ecdf'
    line: {
      style: {
        stroke: 'green',
        lineWidth: 2
      }
    },
    label: {
      text: '经验累积分布函数（ECDF）'
    }
  }
]);
    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/LTErbdPNVoWNUVxweNacM8m2nTe.gif' alt='' width='1000' height='auto' />

### 在线demo和教程

demo： https://visactor.com/vchart/demo/scatter-chart/scatter-regression-line    

教程： https://visactor.com/vchart/guide/tutorial_docs/Chart_Extensions/regression_line    

### 欢迎交流

最后，我们诚挚的欢迎所有对数据可视化感兴趣的朋友参与进来，参与 VisActor 的开源建设：    

**VTable**：[VTable 官网](https://link.juejin.cn?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)、[VTable Github（欢迎 Star）](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FVisActor%2FVTable)    

VisActor 官方网站：[www.visactor.io/](https://link.juejin.cn?target=http%3A%2F%2Fwww.visactor.io%2F) 或 [www.viactor.com](https://link.juejin.cn?target=http%3A%2F%2Fwww.viactor.com)    

Discord：[discord.gg/3wPyxVyH6m](https://link.juejin.cn?target=http%3A%2F%2Fdiscord.gg%2F3wPyxVyH6m)    

飞书群（外网）：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F40dcf4e6722d4925804361a2269991d8~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D264%26h%3D277%26s%3D35808%26e%3Dpng%26b%3Dfdfdfd)    

微信公众号：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2Ff28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D258%26h%3D258%26s%3D8552%26e%3Dwebp%26b%3Dfefefe)    

github：[github.com/VisActor](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FVisActor%2F)    
