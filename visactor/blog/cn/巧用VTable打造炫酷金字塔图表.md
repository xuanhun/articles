---
title: 巧用VTable打造炫酷金字塔图表

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
在数据分析和可视化领域，表格是展示数据直观、有效的方式之一。今天，就让我们来探索如何利用VTable这个强大的表格组件，制作出既美观又富有信息量的金字塔图表，以及深入了解VTable中各种单元格类型的使用方法，让你的表格也能“绘”出精彩图表！

## 用VTable实现人口金字塔



人口金字塔是一种经典的图表形式，能够清晰地展示不同年龄段人口的性别分布情况。在VTable中，我们可以通过巧妙地设置单元格类型和数据，来实现这一图表。

### 数据准备

以2023年中国人口金字塔为例，我们首先需要准备人口数据，包括不同年龄段的男性和女性人口数量。以下是一份二维数组的数据中第一列是代表年龄，第二列代表男性人口数量，第三列代表女性人口数量。

```
const records = [
  [0, 5634674, 5063545],
  [1, 5705095, 5104886],
  [2, 6428332, 5727205],
  [3, 7693489, 6821534],
  [4, 8161021, 7203100],
  [5, 9715173, 8532301],
  [6, 9650591, 8437180],
  [7, 9219192, 8023112],
  [8, 9895275, 8576677],
  [9, 9661108, 8338776],
  [10, 10181676, 8760074],
  [11, 9435376, 8092996],
  [12, 9489747, 8122793],
  [13, 9556154, 8163689],
  [14, 9381839, 8005802],
  [15, 9099192, 7758048],
  [16, 8890454, 7576040],
  [17, 8720216, 7432716],
  [18, 8594120, 7328355],
  [19, 8416000, 7183082],
  [20, 8430209, 7205565],
  [21, 8466902, 7251819],
  [22, 8894890, 7638348],
  [23, 8486376, 7308669],
  [24, 8587529, 7420310],
  [25, 8796105, 7629613],
  [26, 9049822, 7885903],
  [27, 9367029, 8203736],
  [28, 9588509, 8447239],
  [29, 9880743, 8760562],
  [30, 10273285, 9171734],
  [31, 11010587, 9897533],
  [32, 13601860, 12314052],
  [33, 13043658, 11883209],
  [34, 12594941, 11546787],
  [35, 13131786, 12117412],
  [36, 12360084, 11473978],
  [37, 11386597, 10628424],
  [38, 10761644, 10094575],
  [39, 10163338, 9571765],
  [40, 11422792, 10797854],
  [41, 10508636, 9964478],
  [42, 10017988, 9526554],
  [43, 9619763, 9173632],
  [44, 9056756, 8659112],
  [45, 8997215, 8626101],
  [46, 9652077, 9281127],
  [47, 10015409, 9662890],
  [48, 11069594, 10715950],
  [49, 11768373, 11433423],
  [50, 12018464, 11708473],
  [51, 12355492, 12091272],
  [52, 12906951, 12693324],
  [53, 12432644, 12298286],
  [54, 12529023, 12460625],
  [55, 10718431, 10707166],
  [56, 11251041, 11290592],
  [57, 11415079, 11504019],
  [58, 11196086, 11338591],
  [59, 12228142, 12453712],
  [60, 9600080, 9836193],
  [61, 5711938, 5898205],
  [62, 5933189, 6167949],
  [63, 5948957, 6221196],
  [64, 7167743, 7565716],
  [65, 8064981, 8622421],
  [66, 7202344, 7810706],
  [67, 7411934, 8156809],
  [68, 7100337, 7937336],
  [69, 6511011, 7403640],
  [70, 6628801, 7655587],
  [71, 5539974, 6496927],
  [72, 5340662, 6361109],
  [73, 4486039, 5334561],
  [74, 4095771, 4855813],
  [75, 3672686, 4282433],
  [76, 3284023, 3938476],
  [77, 2840609, 3499661],
  [78, 2557354, 3174779],
  [79, 2259373, 2870194],
  [80, 2076623, 2715500],
  [81, 1907136, 2559103],
  [82, 1682448, 2318997],
  [83, 1409855, 2009200],
  [84, 1286674, 1875365],
  [85, 1103399, 1686589],
  [86, 933191, 1521358],
  [87, 769638, 1350282],
  [88, 626185, 1151337],
  [89, 503993, 973886],
  [90, 370193, 768844],
  [91, 268569, 598536],
  [92, 202514, 492130],
  [93, 136717, 366776],
  [94, 93980, 278374],
  [95, 58550, 192884],
  [96, 34165, 129966],
  [97, 20236, 92475],
  [98, 10692, 60687],
  [99, 4848, 35695],
  [100, 3722, 45558]
];

```
### 配置列信息

然后，在VTable中创建一个表格，将年龄段及男女性别分别作为列标签。接下来，关键的一步就是设置男性及女性人口数量列对应的单元格类型为progressbar，这样每个单元格就可以展示一个进度条，其长度代表相应年龄段和相应性别人口的数量。

在具体的实现中，我们可以通过以下配置来配置表格的列信息：

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
在上面配置中，第一列和最后一列都是年龄段，第二列是男性人口数量，第三列是女性人口数量，我们拿到的数据是个从`0 - 100`每一个年龄都对应一条数据，数据格式是二维数组，所以设置`column`的`filed`时直接将数组index设置到`filed`上，第二三列的`cellType`设置为`'progressbar'`让其显示为数据条，数据条需要同时配置`{min: number, max: number}`也就是指明数据范围，max我这里选取了records中的最大值，为了整体上好看让其和边缘有个padding这里加了`buffer： 1000000`。

目前显示效果如下：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Zs10bEE1ToAKTgxo8HRcUALynBK.gif' alt='' width='496' height='auto'>

### 优化显示效果

先就图中文字拥挤进行优化，因为设置的行高比较小，`defaultRowHeight: 6`只有6px，优化左右侧年龄数字显示不全问题，这里开启`mergeCell： true`，配置会将上下单元格内容一致的进行合并，但是每一行的数据并不相同怎么样，我们通过fieldFormat强制让他们上下相邻五行的内容一样：

```
    fieldFormat(args) {
      if (args[0] >= 100) {
        return 100;
      }
      return Math.round(args[0] / 5) * 5;
    },

```
第二三列不希望显示数据，同样利用fieldFormat：

```
    fieldFormat(args) {
      return '';
    }

```
优化后目前效果如下：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/FAZKbucAboQSfXxmYJLcPwYSnFg.gif' alt='' width='499' height='auto'>

继续优化数据条的样式，首先需要让第二列的数据条右对齐，设置style样式：

```
    style: {
      barRightToLeft: true
    }

```
优化后：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/AXwwbb76Yo3crpxotCXctGvMnLd.gif' alt='' width='495' height='auto'>

优化左右两侧数据条的颜色后，最终这俩列配置为：

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
其他的边框及表头单元格背景色及文本样式配置到了theme中：

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

通过上述代码，我们可以看到，每个年龄段的男性和女性人口数量分别用不同颜色的进度条展示出来，形成了一个直观的人口金字塔图表。

### 图表标题

一个完整的图表若想清晰准确地传达其核心信息，必然需要有一个简洁明了且能精准表达图表含义的标题。对于功能强大的 VTable 而言，这简直是小菜一碟。我们只需在现有的配置中轻松地增加 title 配置，就能为图表赋予一个恰当且具有概括性的标题，使图表的意义一目了然，让使用者能够迅速捕捉到关键信息，大大提升图表的可读性和实用性。

```
title: {
    text: 'China Population 2023 Pyramid: 1,425,849,286',
    padding: 10,
    orient: 'top',
    textStyle: { fill: '#16cdcd' }
  },

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/GyQfbJqXKoaN4mxiL6ScsLInn8f.gif' alt='' width='497' height='auto'>

完整实例代码可移步至官网：https://visactor.com/vtable/demo/business/population-pyramid

## VTable中progressbar类型的使用



进度条在各种应用程序中都扮演着重要的角色，它能够直观地向用户展示任务的进展情况。VTable 所提供的 `progressbar` 类型，更是为开发者提供了丰富的选项，配合表格的其他能力能使其能够根据具体的需求和设计风格，打造出独具特色的进度条。

下面我们具体介绍一下相关的关键配置：

*  **min**：进度条展示范围的最小数据，默认值为0。

*  **max**：进度条展示范围的最大数据，默认值为100。

*  **barType**：进度条类型，默认值为'default'。可选值包括：

*  'default'：普通进度条；

*  'negative'：考虑负值的进度条，进度条会以0为分割显示正负两个方向的进度条；

*  'negative_no_axis'：与'negative'类似，但无0值坐标轴。

*  **dependField**：如果需要单元格展示的文字和进度条使用的数据字段不同，在`dependField`中配置进度条使用的数据字段。

此外，progressbar类型在样式`style`方面也有很多专属配置项，如`barColor`、`barHeight`、`barAxisColor`、`barMarkXXX`等，通过这些配置项，我们可以进一步美化进度条的外观。



## VTable其他单元格类型的介绍



除了progressbar类型，VTable还提供了多种丰富的单元格类型，这些不同的单元格类型具有各自独特的特点和功能，让我们能够根据不同的数据展示需求进行灵活选择。

下面进行简单介绍：

*  **text**：最基本的单元格类型，用于显示文本信息。在表格中，我们可以使用text类型来展示行标签、列标签以及一些辅助说明文字，帮助观众更好地理解数据的含义。

*  **link**：用于显示链接类型的数据。通过设置不同的格式化函数、样式以及点击时的跳转地址、是否检测链接的合法性等，我们可以对链接进行灵活的展示和处理。

*  **image**：允许我们在单元格中插入图片。如果我们的数据与图片相关，如产品图片、地理位置图片等，使用image类型可以直观地展示这些信息，增强表格的视觉效果。

*  **video**：与image类型类似，用于显示视频类型的数据，所有配置项和image一样。

*  **sparkline**：用于显示迷你图类型的数据。通过设置迷你图具体的`sparklineSpec`配置项，我们可以展示各种趋势、分布等迷你图表，为数据可视化提供更多可能性。

*  **chart**：用于显示图表类型的数据。在使用该类型之前需要先通过`VTable.chart.register`注册一种图表类型，注册之后可以通过`chartModule`和`chartSpec`等配置项来展示丰富的图表内容。

*  **checkbox**：复选框类型单元格在许多应用中被广泛使用，包括任务管理、数据筛选、权限设置等。checkbox单元格是否处于选中状态，默认值为 false，支持配置函数，不同单元格配置不同。

*  **radio**： 单选框类型

下面为大家截一张官网展示各个类型的 demo 列表的图片以供参考！是不是感觉其功能相当强大呢？从这些 demo 中，我们能够清晰地感受到其强大的功能所带来的便捷和高效。无论是在数据展示，还是在数据分析场景中，它都能发挥出显著的作用，为我们提供优质的体验和解决方案。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/SuKNbbpxDofcJkxz1oUczYAOnYg.gif' alt='' width='897' height='auto'>

## 小结

通过合理地运用VTable中的各种单元格类型，我们能够打造出功能丰富、形式多样的表格图表。无论是直观的人口金字塔，还是其他复杂的数据可视化需求，VTable都能为我们提供强大的支持。赶快去尝试一下吧，让你的表格也能“绘”出精彩纷呈的图表世界！


# 联系我们

github ：[github.com/VisActor](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FVisActor)

VisActor 微信订阅号留言（可以通过订阅号菜单加入微信群）：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VyPsbaIz8offShxlv0ZcqqD8nfd.gif' alt='' width='258' height='auto'>

VisActor 官网：[www.visactor.io/](https://link.juejin.cn/?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)  ； [www.visactor.](https://link.juejin.cn/?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)com

飞书群：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/NAQhbtdelofTIyxk9pgcE3hqnQG.gif' alt='' width='264' height='auto'>

discord：https://discord.com/invite/3wPyxVyH6m