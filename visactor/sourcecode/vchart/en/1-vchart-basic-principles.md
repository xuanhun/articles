---
title: 1 Basic Principles of VChart    

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
# 1.1 Chart Composition

## Terminology

1. Mark: Basic graphical elements (basic primitives), such as lines, points, rectangles, etc.    

1. Series: Responsible for the visual representation of a specific type of data, containing a set of primitives and their corresponding chart logic, such as a series of lines in a line chart    

1. Region: Defines the spatial area of the chart, associates one or more series, handles interactions and animations, and provides coordinate systems    

1. Component: Components that assist in chart reading and interaction, such as legends, axes, tooltips, etc.    

1. Layout: Manages the layout of the chart, including the position and size of regions and components    

1. Chart: The abstract concept of the entire chart, containing elements on the view such as layout, components, and regions, as well as data and all elements required to form a chart    

## Structural Relationships


<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/HOM9w48WrhyL5hbEByTcG0BanGf.gif' alt='' width='534' height='auto' />

## Simple Chart Example

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/S64ebNauBobOOJxwIalcq8E9ncf.gif' alt='' width='1000' height='auto' />

## Combined Chart Example

### Same Region

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/GAGOb6NrBoGdMrxHbt9cvDXNndb.gif' alt='' width='1000' height='auto' />

The above image is a composite chart, simply put, it has multiple series groups, with bar and line series groups above.

1. If we do not configure specifically, all series will be associated with one region, so they will overlap and share certain coordinates.

1. Each series can have its own data source, or the data source can be configured directly on the chart. In the series, it is associated through `fromDataId` or `fromDataIndex`. In the current example, we choose to configure it on the chart.

### Different Regions

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/FdZebrNhWo3MqTxoqOfcAyYJnh6.gif' alt='' width='1000' height='auto' />

In this example, it is also a combined chart, but its two series appear in different regions. As mentioned above, we use layout to manage the layout of regions. In this example, we use the following code:    


```Typescript
  layout: {
    type: 'grid',
    col: 4,
    row: 3,
    elements: [
      {
        modelId: 'legend',
        col: 0,
        colSpan: 4,
        row: 0
      },
      {
        modelId: 'pie-region',
        col: 0,
        colSpan: 2,
        row: 1
      },
      {
        modelId: 'axis-left',
        col: 2,
        row: 1
      },
      {
        modelId: 'bar-region',
        col: 3,
        row: 1
      },
      {
        modelId: 'axis-bottom',
        col: 3,
        row: 2
      }
    ]
  },    

```
Above, the layout of regions and components was managed in a manner similar to a grid. We use these `modelId`s to associate the corresponding configurations of regions and components:


```Typescript
  region: [
    {
      id: 'pie-region'
    },
    {
      id: 'bar-region'
    }
  ],
  axes: [
    {
      id: 'axis-left',
      regionId: 'bar-region',
      orient: 'left'
    },
    {
      id: 'axis-bottom',
      regionId: 'bar-region',
      orient: 'bottom'
    }
  ]    

```
# 1.2 VChart Architecture and Source Code Structure

## 1.2.1 The Relationship Among VChart, VGrammar, and VRender

These three are the core components in the VisActor visualization system, and their relationship is hierarchical, from the bottom layer to the top layer as follows:    

### VRender (Bottom Layer)

VRender is a low-level visualization rendering engine responsible for the most basic graphic drawing and rendering tasks:    

*  It provides rich visualization rendering capabilities, including custom animations, element composition, and narrative arrangement    

*  It is the foundation of the VisActor visualization system, providing rendering capabilities for upper-level libraries    

*  VRender offers a plugin system for flexible extension    

*  It can seamlessly switch between 2D/3D effects    

*  It handles low-level Canvas operations, graphic drawing, scene management, etc.    

### VGrammar (Middle Layer)

VGrammar is a visualization grammar library based on VRender:    

*  It uses declarative syntax to describe data visualization    

*  VGrammar maps data to visual elements, handling data transformation, marks, scales, etc.    

*  It provides more advanced APIs to simplify the process of creating complex visualizations    

*  VGrammar is responsible for chart grammar definition, data mapping, automatic layout, etc.    

*  It is essentially a further encapsulation of VRender, adding more visualization grammar concepts    

### VChart (Top Layer)

VChart is the top-level chart component library:    

*  It is built on VGrammar and encapsulates common chart types (bar charts, pie charts, line charts, etc.)    

*  VChart provides ready-to-use chart components, so users do not need to understand the underlying graphic grammar    

*  It features cross-platform capabilities, automatically adapting to desktop, H5, and various mini-program environments    

*  VChart offers comprehensive data narrative capabilities, including extensive annotations, animations, flow control, and narrative templates    

*  It is aimed at end users, providing the most user-friendly visualization interface and APIs    

### Summary of the Relationship

The architectural relationship among these three can be understood as:    

```Typescript
VChart (图表组件库)
   ↓
VGrammar (可视化语法)
   ↓
VRender (渲染引擎)
   ↓
浏览器/Canvas/WebGL    

```
From the code implementation perspective:    

*  VChart uses VGrammar to define and construct charts    

*  VGrammar uses VRender for actual drawing and rendering    

*  Ultimately, VRender controls the underlying Canvas/WebGL to draw graphics    

This layered architecture allows developers to choose tools at different levels according to their needs: if highly customized visualizations are required, VRender or VGrammar can be used directly; if quick creation of standard charts is needed, VChart can be used.    

## 1.2.2 Relationships Between Internal Components of VChart and Source Code Structure

The overall architecture adopts a modular design, with the core divided into the following main parts:    

1. Core Engine (Core): The central controller of VChart, responsible for organizing the collaboration of various modules    

1. Chart: Specific implementations of various chart types    

1. Series: Responsible for mapping data to graphics in charts    

1. Mark: Basic graphic elements    

1. Region: Defines the rendering area of the chart    

1. Component: Additional components such as axes, legends, etc.    

1. Layout: Handles the position calculation of chart elements    

1. Event: Handles user interactions    

1. Scale: Related to data mapping and scales    

1. Data: Data transformation and processing    

### Core Module

#### VChart Core Class

The VChart class is the entry point of the entire chart library, responsible for instantiating and managing the lifecycle of the entire chart.    


```Typescript
// packages/vchart/src/core/vchart.ts
export class VChart implements IVChart {
  readonly id = createID();
  
  // 用于注册图表、组件、系列等
  static useRegisters(comps: (() => void)[]) { ... }
  static useChart(charts: IChartConstructor[]) { ... }
  static useSeries(series: ISeriesConstructor[]) { ... }
  
  // 核心渲染流程
  renderSync(morphConfig?: IMorphConfig) { ... }
  async renderAsync(morphConfig?: IMorphConfig) { ... }
  
  // 数据更新方法
  updateData(id: StringOrNumber, data: DataView | Datum[] | string, ...) { ... }
  updateSpec(spec: ISpec, forceMerge: boolean = false, ...) { ... }
  
  // 状态管理
  setSelected(datum: MaybeArray<any> | null, ...) { ... }
  setHovered(datum: MaybeArray<Datum> | null, ...) { ... }
}    

```
The lifecycle of VChart mainly includes:    

1. Initialization of configuration and data    

1. Creation of chart instance    

1. Layout calculation    

1. Rendering    

1. Interaction event handling    

1. Updating and destruction    

#### Modular Chart Class (Chart)

The chart module implements various types of charts, such as bar charts, line charts, pie charts, etc., all inheriting from BaseChart.    

```Typescript
// packages/vchart/src/chart/base/base-chart.ts
export class BaseChart<T extends IChartSpec> extends CompilableBase implements IChart {
  readonly type: string = 'chart';
  readonly seriesType: string;
  
  protected _regions: IRegion[] = [];
  protected _series: ISeries[] = [];
  protected _components: IComponent[] = [];
  
  protected _layoutFunc: LayoutCallBack;
  protected _layoutRect: IRect = { ... };
  
  layout(params: ILayoutParams): void { ... }
  compile() { ... }
}    

```
For example, BarChart inherits from BaseChart    


```Typescript
export class BarChart<T extends IBarChartSpec = IBarChartSpec> extends BaseChart<T> {
  static readonly type: string = ChartTypeEnum.bar;
  static readonly seriesType: string = SeriesTypeEnum.bar;
  static readonly transformerConstructor = BarChartSpecTransformer;
  readonly transformerConstructor = BarChartSpecTransformer;
  readonly type: string = ChartTypeEnum.bar;
  readonly seriesType: string = SeriesTypeEnum.bar;
}    

```
The chart module is responsible for:    

*  Determining chart types and layouts    

*  Managing included areas, series, and components    

*  Handling the overall mapping from data to visual elements    

#### Series Module (Series)

The series module is the core mapping from data to visual representation, with different series types corresponding to different graphical forms.    


```Typescript
// packages/vchart/src/series/base/base-series.ts
export abstract class BaseSeries<T extends ISeriesSpec> extends BaseModel<T> implements ISeries {
  readonly type: string = 'series';
  readonly coordinate: CoordinateType = 'none';
  
  protected _region: IRegion;
  protected _rootMark: IGroupMark = null;
  protected _seriesMark: Maybe<IMark> = null;
  
  protected _rawData!: DataView;
  protected _data: SeriesData = null;
  
  abstract initMark(): void;
  abstract initMarkStyle(): void;
  abstract dataToPosition(data: Datum, checkInViewData?: boolean): IPoint;
}    

```
Series modules are responsible for:    

*  Converting data to graphical marks    

*  Handling data mapping for specific chart types    

*  Managing the style and state of marks    

#### Mark Module (Mark)

Marks are the most basic visual elements, such as lines, rectangles, points, etc., which are the fundamental units that make up charts. Corresponding code implementations can be found in the `packages/vchart/src/mark` directory.    

Marks are responsible for:    

*  Implementing specific graphic rendering    

*  Handling interaction states (such as highlight, selection)    

*  Binding with data    

#### Region Module (Region)

Regions define the rendering positions of charts on the canvas and can contain multiple series. Corresponding code is in `packages/vchart/src/region`    

The region module is responsible for:    

*  Determining the position and size of each sub-region in the chart    

*  Managing the series contained within    

*  Handling layout relationships between regions    

#### Component Module (Component)

Components are auxiliary elements in charts besides data graphics, such as axes, legends, titles, etc. Various component implementations are in the `packages/vchart/src/component` directory    

The component module is responsible for:    

*  Rendering various auxiliary elements    

*  Interacting with users (such as legend clicks)    

*  Collaborating with the main chart    

#### Layout Module (Layout)

The layout module is responsible for calculating the position and size of each chart element. Corresponding code is in `packages/vchart/src/layout`. Specifically, it includes:    

*  Calculating element positions    

*  Adjusting container size adaptively    

*  Handling layering relationships between elements    

#### Event Module (Event)

The event module handles user interactions and internal events. Specifically, it includes:    

*  Handling user interaction events (such as clicks, hover)    

*  Dispatching internal events    

*  Triggering data updates and rendering updates    

#### Scale Module (Scale)

The scale module is responsible for mapping data to visual attributes. Specifically, it includes:    

*  Handling the mapping between data and visual space    

*  Managing various scales (linear, discrete, color, etc.)    

*  Calculating axis ranges and ticks    

#### Data Module (Data)

The data module handles the transformation and processing of raw data. Specifically, it includes:    

*  Parsing and transforming data    

*  Statistical calculations    

*  Handling missing and abnormal values    

## Rendering Process

The rendering process of VChart mainly includes the following steps:    

1. Initialization: Create a VChart instance through the spec configuration    

1. Compilation: Parse the configuration and create various components and series    

1. Layout: Calculate the position and size of each element    

1. Data processing: Process and transform data    

1. Rendering preparation: Bind data to marks    

1. Actual rendering: Draw marks onto the canvas    

1. Interaction binding: Bind various interaction events    

## Data Update Process

When data or configuration changes:    

1. Call updateData or updateSpec methods    

1. Reprocess affected data    

1. Update related scales    

1. Re-layout (if needed)    

1. Update affected marks    

1. Trigger re-rendering    


 # This document was revised and organized by  
 [Xuanhun](https://github.com/xuanhun)