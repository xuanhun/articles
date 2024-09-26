---
title: VTable表格自定义渲染:技术实践与案例

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
# VTable简介

VTable是一款基于可视化渲染引擎VRender的高性能表格组件库，为用户提供卓越的性能和强大的多维分析能力，以及灵活强大的图形能力。相对于dom表格，VTable 基于canvas 画布进行渲染，性能与可视化能力具有碾压级的优势。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/M4TPbKgdpo2nSaxB4SNcOPycnBh.gif' alt='' width='1000' height='auto'>

介绍文档：[VTable——不只是高性能的多维数据分析表格，开源，免费，百万数据秒级渲染](https%3A%2F%2Fjuejin.cn%2Fpost%2F7287214029449805836)

站点：https://visactor.com/vtable

在用户实际的应用场景中，随着需求的不断迭代，对于 VTable 的自定义能力提出来更高的要求。用户期望能够更加灵活、便捷地根据自身的特定需求来定制 VTable 的各项功能和特性，以满足不同业务场景下的多样化需求。为了更好地满足用户的这些需求，VTable 的自定义能力也在不断地进行优化和改进，进行了多个阶段的功能迭代。接下来，我们将分享这部分自定义能力的演进历程和功能展示。

# 第一阶段 自定义内容和样式

## 属性配置函数

在VTable最初的版本，我们给用户提供了内容和样式的回调函数式的配置方式：

*  文字内容：在列或指标配置中，fieldFormat配置可以对单元格内容显示进行自定义处理，常用于文字内容的格式化

```
type FieldFormat = (record: any, col?: number, row?: number, table?: BaseTableAPI) => any;

```
*  图标：在列或指标配置中，icon配置除了支持固定的图标之外，也支持函数配置，不同单元格显示不同图标

```
type Icons = (string | ColumnIconOption)[] | ((args: CellInfo) => (string | ColumnIconOption)[]);

```
*  单元格样式（文字样式，单元格样式）：与图标相同，单元格相关的样式都支持函数式配置

```
{
    // 斑马线效果
    bgColor: (args: StylePropertyFunctionArg): string {
      const { row, table } = args;
      const index = row - table.frozenRowCount; // 计算该单元格在body区域的行index
      if (!(index & 1)) {
        return '#FAF9FB';
      }
      return '#FDFDFD';
    }
    // ......
}

```
*  ......

生成单元格节点时，会依据函数的不同返回结果，分别设置各个单元格内的内容和样式。

## 相关使用案例

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Am3QbfxK3oaqWbxXGCDcuruvnye.gif' alt='' width='1000' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Y4ODbF6xmozuELxyUojcoaKVnbd.gif' alt='' width='1000' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/BQysbpbS5oFM4TxpuH5cn4dPnxg.gif' alt='' width='1000' height='auto'>

```
const option = {
    // style
    style: {
        bgColor: (arg) => {
            return getBgColor(arg.value)
        },
        color: (arg) => {
            return getColor(arg.value)
        },
        // ......
    },
    // icon
    icon: (arg) => {
        if (arg.value >= 0) {
            return 'up-icon'
        } else if (arg.value < 0) {
            return 'down-icon'
        }
    },
    // ......
}

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/U2yDbaxEpoDp8TxSluXcbwx7nPg.gif' alt='' width='1000' height='auto'>

https://www.visactor.com/vtable/demo/business/project-schedule



# 第二阶段 自定义图形

自定义内容和样式的能力，满足了用户的对单元格内容的调整需求，但是所有的修改都是在单元格原有内容的基础上进行的。部分用户提出了新的需求，希望可以不受单元格类型的限制，自由得绘制内容。基于这部分需求，我们开发了自定义图形（customRender）功能。

## 图形配置

在全局option、列或指标配置中，可以配置customRender属性，来自由定义单元格内的图形

```
type ICustomRenderObj = {
  /** 配置出来的类型集合 */
  elements: ICustomRenderElements;
  /** 期望单元格的高度 */
  expectedHeight: number;
  /** 期望单元格的宽度 */
  expectedWidth: number;
  /** 是否还需要默认渲染内容 只有配置true才绘制 默认 不绘制 */
  renderDefault?: boolean;
};

```
`elements`为图元配置组成的数组，支持下列类型：

*  Text

*  Rect

*  Circle

*  Icon

*  Image

*  Arc

*  Line

详细配置可以参考 https://www.visactor.com/vtable/option/ListTable-columns-text#customRender.elements。

为了方便用户设置位置和尺寸，`x` `y` `width` `height`等属性支持配置百分比，基于单元格的宽度或高度设置；也可以在属性中使用函数，接收单元格数据，计算相应的属性值。

下面是一个气泡效果的配置：

```
{
  customRender: {
    elements: [
      {
        type: 'circle',
        x: '50%', // 定位在单元格中心
        y: '50%',
        // 依据数据计算圆半径
        radius: value => {
          const percent = Math.max(5, (Number(value) / 59645 / 2) * 100);
          return `${percent}%`;
        },
        // 依据数据计算渐变色
        fill: value => {
          const color = getColor(80, 59645, value, 0.5);
          const color1 = getColor(80, 59645, value, 1);
          return {
            gradient: 'linear',
            x0: 0,
            y0: 1,
            x1: 0,
            y1: 0,
            stops: [
              {
                offset: 0,
                color: color
              },
              {
                offset: 1,
                color: color1
              }
            ]
          };
        }
      }
    ],
    renderDefault: false
  }
  // ......
 }

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/DeGUb6IBboo2QlxgfVKcOL0Indc.gif' alt='' width='1000' height='auto'>

https://www.visactor.com/vtable/demo/business/sales-bubble

## 相关使用案例

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/LidPb2klpoSrevxuLTGcAU9SnEf.gif' alt='' width='1000' height='auto'>

自定义图形功能在常常用在在表格中添加简易按钮，使用自定义图形绘制简单按钮，通过事件监听执行相应的功能

```
const columns =[
    // ......
     {
        "field": "Operation",
        "title": "Operation",
        "width": 100,
        customRender: {
            elements: [
                {
                    type: 'text',
                    x: '10%',
                    y: '50%',
                    textBaseline: 'middle',
                    fill: '#00c',
                    text: 'edit',
                    fontSize: 14,
                    underline: true,
                    cursor: 'pointer',
                    pickable: true
                },
                {
                    type: 'text',
                    x: '50%',
                    y: '50%',
                    textBaseline: 'middle',
                    fill: '#00c',
                    text: 'del',
                    fontSize: 14,
                    underline: true,
                    cursor: 'pointer',
                    pickable: true
                }
            ]
        }
    },
];

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/UCzTb6SZsotARbxXKpmcu6Cqnjg.gif' alt='' width='780' height='auto'>

另一种用户经常使用的场景，是在单元格内原有数据的基础上，做一些简单的标注

```
const columns =[
    {
        "field": "Sales",
        "title": "Sales",
        "width": "auto",
        customRender: {
            elements: [
                {
                    type: 'rect',
                    x: '10%',
                    y: '10%',
                    fill: false,
                    stroke: (value) => value > 200 ? '#c00' : false,
                    lineWidth: 20,
                    width: "80%",
                    height: "80%"
                }
            ],
            renderDefault: true
        }
    },
    // ......
];

```


<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/XaMhbIFpMosftbxJewecXLkBnsO.gif' alt='' width='1000' height='auto'>

https://www.visactor.com/vtable/demo/custom-render/custom-render-global



自定义图形详细说明可以参考 https://www.visactor.com/vtable/guide/custom_define/custom_render，自定义图形能力，目前推荐使用在单元格原有内容上补充简单图形，或单元格内自定义简单内容的场景

# 第三阶段 自定义渲染

自定义图形功能可以满足在单元格中自由绘制图元，但是在用户使用过程中，随着自定义内容的越来越复杂，发现了一些功能短板：

*  图元需要组织为一个一维数组，复杂场景强制要求扁平化，相应的代码也会很难维护

*  图元布局需要基于单元格绝对定位，没有相对定位能力，也不能自适应布局（flex）

*  内容只能基础绘图图元，对于复杂模块实现比较复杂

*  没有实时更新能力（交互）

## 自定义场景树节点

针对各类新的复杂功能需求，我们希望提供较为底层的接口，支持用户自行组织VRender场景树节点，来实现单元格内复杂的内容；基于VRender提供的类flex布局能力，用户可以在单元格中进行自适应布局；针对表格中比较常用的一些功能组件（Tag, Chenkbox, Radio），我们也进行了封装，可以很方便的调用。

以一个简单的上下布局的标题标签为例：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/X3GabQIJSorHkpxw9ZKcmyKPnKg.gif' alt='' width='670' height='auto'>

```
import { Group, Text, Tag } from '@visactor/vtable/es/vrender';
// ......

const options = {
  columns: [
    {
      field: 'custom',
      title: 'custom-layout',
      customLayout: (args) => {
        const { table, row, col, rect } = args;
        const { height, width } = rect ?? table.getCellRect(col, row);

        // 单元格容器
        const container = new Group({
          height,
          width,
          display: 'flex',
          flexDirection: 'column',
          flexWrap: 'nowrap'
        });

        // 上部group
        const containerTop = new Group({
          height: height / 2,
          width: width,
          display: 'flex',
          flexDirection: 'row',
          alignItems: 'center',
          fill: 'yellow',
          opacity: 0.1,
        });

        // 上部文字Title
        const title = new Text({
          text: 'custom-layout-title',
          fontSize: 14,
          color: '#333',
          fontWeight: 600,
          boundsPadding: [0, 0, 0, 20]
        });

        containerTop.add(title);

        // 下部group
        const containerBottom = new Group({
          height: height / 2,
          width: width,
          display: 'flex',
          flexDirection: 'row',
          alignItems: 'center',
          fill: 'green',
          opacity: 0.1
        });

        // 下部tags
        const tag1 = new Tag({
          text: 'tag1',
          textStyle: {
            fontSize: 10,
            fill: 'rgb(51, 101, 238)'
          },
          panel: {
            visible: true,
            fill: '#f4f4f2',
            cornerRadius: 5
          },
          space: 5,
          boundsPadding: [0, 0, 0, 20]
        });

        const tag2 = new Tag({
          text: 'tag2',
          textStyle: {
            fontSize: 10,
            fill: 'rgb(51, 101, 238)'
          },
          panel: {
            visible: true,
            fill: '#f4f4f2',
            cornerRadius: 5
          },
          space: 5,
          boundsPadding: [0, 0, 0, 20]
        });

        containerBottom.add(tag1);
        containerBottom.add(tag2);

        container.add(containerTop);
        container.add(containerBottom);

        return {
          rootContainer: container,
          renderDefault: false
        };
      }
    },
    // ......
  ],
  // ......
}

```
为了方便定义节点，我们除了实例化后组装的方式外，也支持直接写jsx标签（需要用户的打包环境支持编译jsx），上面的节点也可以写为

```
const container = (
  <VGroup
    attribute={{
      // ......
    }}
  >
    <VGroup
      attribute={{
        // ......
      }}
    >
      <VText
        attribute={{
          // ......
        }}
      ></VText>
    </VGroup>
    <VGroup
      attribute={{
        // ......
      }}
    >
      <VTag
        attribute={{
          // ......
        }}
      ></VTag>
      <VTag
        attribute={{
          // ......
        }}
      ></VTag>
    </VGroup>
  </VGroup>
)

```
## 交互更新

直接操作VRender场景节点后，交互功能就可以使用相应的事件回调很方便的实现

```
// hover显示icon背景
<VImage
  attribute={{
    id: 'location-icon',
    width: 15,
    height: 15,
    image,
    boundsPadding: [0, 0, 0, 10],
    cursor: 'pointer'
  }}
  stateProxy={stateName => {
    if (stateName === 'hover') {
      // hover状态更新attribute
      return {
        background: {
          fill: '#ccc',
          cornerRadius: 5,
          expandX: 1,
          expandY: 1
        }
      };
    }
  }}
  onMouseEnter={event => {
    event.currentTarget.addState('hover', true, false);
    event.currentTarget.stage.renderNextFrame();
  }}
  onMouseLeave={event => {
    event.currentTarget.removeState('hover', false);
    event.currentTarget.stage.renderNextFrame();
  }}
></VImage>

```
## 相关使用案例

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/AuqFboM16oAF36x2jbTcL5csn3g.gif' alt='' width='1000' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/UA0KbsALLo9pP8xMEcNcdKMYnKf.gif' alt='' width='1000' height='auto'>

在需要展示富文本内容的场景，使用自定义渲染可以分段组织不同样式的文本，并在单元格中实现对应的布局和展示。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Cw2MbZdS5oqCOPxb7hMcGXxsnUd.gif' alt='' width='1000' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/PlJpbrR1Gorm8AxtykbceYOsnUf.gif' alt='' width='1000' height='auto'>

复杂的表格面板也是自定义渲染常用的场景，通过配置不同的图元，可以实现按钮、图标、状态Tag等等的单元格内容。



<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/FzJ8bFGv0ooXdnx7qcwcoyj2nGh.gif' alt='' width='1000' height='auto'>

在表格场景中高度定制展示内容的场景，自定义渲染可以供用户自由实现对应的显示效果。



<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Q2SwbH6FGoKH2vxAQJicWAVunxl.gif' alt='' width='1000' height='auto'>

https://www.visactor.com/vtable/demo/custom-render/custom-cell-layout



自定义渲染详细说明可以参考：https://www.visactor.com/vtable/guide/custom_define/custom_layout，推荐使用在单元格内自定义内容较为复杂的场景中

# 第四阶段 自定义组件

通过函数自定义场景树节点，可以满足大部分用户对于单元格内容的定义的功能，但在用户的开发过程中，一些新的使用问题也开始浮现出来：

*  随着api逐渐底层，自定义功能的上手难度逐渐增加，用户需要对VRender场景树有比较清楚的了解后，才能设计自己的单元格内容场景节点

*  函数式的写法虽然支持jsx标签，但是不是真正的react组件，无法使用props等react组件的基础功能，对于希望可以把单元格内容组件化的用户很不友好

*  一些业务场景，有一些已经完成的高度封装的业务组件，单元格内需要展示react dom组件

针对新的使用问题，我们在这一阶段对react场景进行了专项优化，将单元格内自定义部分进行真正的组件化，并且支持在单元格中展示react dom组件，优化后react开发者可以快速上手自定义组件，也可以支持一部分项目快速迁移。

## 表格上浮层组件

针对在表格上层实现一个自定义浮层组件（例如tooltip、菜单等），不改变单元格内容的需求，我们提供了全局的`CustomComponent`组件，方便快速定位上层组件。

以一个简单的自定义tooltip为例：

```
function Tooltip(props) {
  return (
    <div style={{ width: '100%', height: '100%', border: '1px solid #333', backgroundColor: '#ccc', fontSize: 10 }}>
      {`${props.value}`}
    </div>
  );
}

function App() {
  const [hoverCol, setHoverCol] = useState(-1);
  const [hoverRow, setHoverRow] = useState(-1);
  const [value, setValue] = useState('');
  const visible = useRef(false);
  const tableInstance = useRef(null);

  const updateHoverPos = useCallback(args => {
    if (visible.current) {
      return;
    }
    setHoverCol(args.col);
    setHoverRow(args.row);
    const cellValue = tableInstance.current.getCellValue(args.col, args.row);
    setValue(cellValue);
  }, []);

  return (
    <ListTable
      ref = {tableInstance}
      option={option}
      onMouseEnterCell={updateHoverPos}
    >
      <CustomComponent
        width="80%"
        height="100%"
        displayMode="cell"
        col={hoverCol}
        row={hoverRow}
        anchor="bottom-right"
        dx="-80%"
      >
        <Tooltip value={value} />
      </CustomComponent>
    </ListTable>
  );

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/ZCbab9TaroEEZ8xnDSZc524Znmd.gif' alt='' width='406' height='auto'>

`CustomComponent`组件中，可以依据锚定的单元格的尺寸和位置，自动设置相关样式

*  可以设置相对于单元格的展示位置（anchor）

*  可以依据单元格的尺寸进行百分比设置自身的尺寸

*  可以使用dx dy进行位置微调

详细说明可以参考 https://www.visactor.com/vtable/guide/custom_define/react-custom-component#%E8%87%AA%E5%AE%9A%E4%B9%89%E5%A4%96%E9%83%A8%E7%BB%84%E4%BB%B6

## 表格单元格自定义组件

在jsx标签的基础上，基于`react-reconciler`我们对自定义渲染进行了完全的组件封装，用户可以使用提供的图元组件，封装一个真正的react组件。

以一个单元格展示两个Tag的简单组件为例：

```
function Cell(props) {
  const { table, row, col, rect, prefix } = props;
  if (!table || row === undefined || col === undefined) {
    return null;
  }
  const { height, width } = rect || table.getCellRect(col, row);
  const record = table.getRecordByRowCol(col, row);

  return (
    <Group
      attribute={{
        width,
        height,
        display: 'flex',
        flexWrap: 'wrap',
        flexDirection: 'row',
        alignItems: 'center',
        alignContent: 'center',
        justifyContent: 'space-around'
      }}
    >
      <Tag 
        textStyle={{
          fontSize: 14,
          fontFamily: 'sans-serif',
          fill: 'rgb(51, 101, 238)'
        }}
        padding={[8, 10]}
        panelStyle={{
          visible: true,
          fill: '#e6fffb',
          lineWidth: 1,
          cornerRadius: 4
        }}
      >{`${prefix}-${record.name}-1`}</Tag>
      <Tag 
        textStyle={{
          fontSize: 14,
          fontFamily: 'sans-serif',
          fill: 'rgb(51, 141, 38)'
        }}
        padding={[8, 10]}
        panelStyle={{
          visible: true,
          fill: '#e6fffb',
          lineWidth: 1,
          cornerRadius: 4
        }}
      >{`${prefix}-${record.name}-2`}</Tag>
    </Group>
  );
}

function App() {
  const tableInstance = useRef(null);

  const [prefix, setPrefix] = useState('cus');

  return (
    <ListTable
      ref={tableInstance}
      records={[{name: 'tag'}]}
    >
      <ListColumn field={'name'} title={'Tag Component'} width={200}>
        <Cell role={'custom-layout'} prefix={prefix}/>
      </ListColumn>
    </ListTable>
  );
}

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/DN6UbXJ27oGFpQxxpOJcYAr4nec.gif' alt='' width='472' height='auto'>

这里我们自定义了组件`Cell`，用来展示两个标签，内容由上层组件传递的props中的prefix和单元格数据决定。这里的组件和传统的组件会有一些区别：

*  组件中使用的标签，必须是react-vtable提供的图元和组件

*  每个列或指标只需要设置一个组件，这个组件会被应用在该列的所有单元格上

*  与`customLayout`的回调函数类似，组件会自带`table`, `row`, `col`, `rect`这些props供使用

除了基础的图元组件和Tag Checkbox Radio组件外，react-vtable也内置了Bottom, Link, Avatar和Poptip这些表格中的常用组件

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/N4Q2ba9s2oiIEdx1tr6cmn4snJc.gif' alt='' width='964' height='auto'>

详细说明可以参考 https://www.visactor.com/vtable/guide/custom_define/react-custom-component，自定义组件目前推荐在react环境，需要对单元格内自定义内容进行组件化封装的场景

## 表格单元格中使用react dom组件

如果需要在组件中使用DOM react组件，VRender支持在图元组件的`attribute`属性中，指定`react`属性，并将react组件作为`element`属性传入：

```
<Group
  attribute={{
    // ......
    react: {
      pointerEvents: true,
      container: table.bodyDomContainer, // table.headerDomContainer
      anchorType: 'bottom-right',
      element: <CardInfo record={record} hover={hover} row={row} />
    }
  }}
>
// ...
</Group>

```
相应的react组件会在相对于单元格的位置实例化，覆盖在canvas上

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/MT4gbpeDGoWiIzxvcm8cx5NgnSg.gif' alt='' width='1000' height='auto'>

目前react dom组件有两种使用方式：

*  在单元格内展示的内容，使用react-vtable提供的图元标签，单元格内触发的弹窗、菜单等组件，可以使用DOM react组件，这是我们推荐的方案。

*  在单元格中完全使用react dom组件，react-vtable也提供完整的表格定位，滚动和更新功能



详细说明可以参考：https://www.visactor.com/vtable/guide/custom_define/react-custom-component#%E4%BD%BF%E7%94%A8dom-react%E7%BB%84%E4%BB%B6

## 相关使用实例

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/GllCbvj6Oo5XWbx7Ke1caI8xnJX.gif' alt='' width='816' height='auto'>

使用react dom组件，可以快速将原先react项目中的组件在vtable中展示，并且保留组件的样式和相应的功能。



[自定义外部组件——VisActor/VTable react demo](https%3A%2F%2Fwww.visactor.com%2Fvtable%2Fdemo-react%2Fcomponent%2Fcustom-component)

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/PuiKbUCc7oHsztxZWlKcbVrJn4c.gif' alt='' width='762' height='auto'>

[自定义组件——VisActor/VTable react demo](https%3A%2F%2Fwww.visactor.com%2Fvtable%2Fdemo-react%2Fcomponent%2Fcustom-component)

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/M1MNbzpW5oTpthxFmy7cF8HqnRf.gif' alt='' width='1000' height='auto'>

[单元格自定义组件+dom组件——VisActor/VTable react demo](https%3A%2F%2Fwww.visactor.com%2Fvtable%2Fdemo-react%2Fcustom-layout%2Fcell-custom-layout-dom)



<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/EHi8bYaMro3CrCxo9Icc3EkNn9c.gif' alt='' width='1000' height='auto'>

[单元格内dom组件——VisActor/VTable react demo](https%3A%2F%2Fwww.visactor.com%2Fvtable%2Fdemo-react%2Fcustom-layout%2Fcell-custom-dom)

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/NgFhbZ1l5oygH7xs5LscIwF6nhf.gif' alt='' width='1000' height='auto'>

[模拟飞书人员卡片](https%3A%2F%2Freact-vtable-component-demo.gf-boe.bytedance.net%2Fuser-profile)

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/XhtCbb6BooheKFx5oRmcb9zencc.gif' alt='' width='1000' height='auto'>

# 后续计划

*  组件库的补充，增加表格能常用组件，扩展组件库覆盖范围

*  进一步提升自定义组件的性能

*  增加vue版本的自定义组件能力

# 欢迎交流

欢迎更多使用[VisActor](https%3A%2F%2Fwww.visactor.io%2F)的用户联系我们，给我们投稿，交流业务场景，提建议，贡献代码，谢谢大家！

**VChart**：[VChart 官网](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fvisactor.io%252Fvchart)、[VChart Github（感谢 Star）](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fgithub.com%252FVisActor%252FVChart)

**VTable**：[VTable 官网](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fvisactor.io%252Fvtable)、[VTable Github（感谢 Star）](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fgithub.com%252FVisActor%252FVTable)

**VMind**：[VMind 官网](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fvisactor.io%252Fvmind)、[VMind Github（感谢 Star）](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fgithub.com%252FVisActor%252FVMind)

官方网站：[www.visactor.io/](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fwww.visactor.io%252F)

Discord：[discord.gg/3wPyxVyH6m](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fdiscord.gg%252F3wPyxVyH6m)

飞书群：[打开链接扫码](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fp3-juejin.byteimg.com%252Ftos-cn-i-k3u1fbpfcp%252F40dcf4e6722d4925804361a2269991d8~tplv-k3u1fbpfcp-jj-mark%253A0%253A0%253A0%253A0%253Aq75.image%2523%253Fw%253D264%2526h%253D277%2526s%253D35808%2526e%253Dpng%2526b%253Dfdfdfd)

微信公众号：[打开链接扫码](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fp3-juejin.byteimg.com%252Ftos-cn-i-k3u1fbpfcp%252Ff28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark%253A0%253A0%253A0%253A0%253Aq75.image%2523%253Fw%253D258%2526h%253D258%2526s%253D8552%2526e%253Dwebp%2526b%253Dfefefe)

Twiter：[twitter.com/xuanhun1](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Ftwitter.com%252Fxuanhun1)

github：[github.com/VisActor](https%3A%2F%2Flink.juejin.cn%3Ftarget%3Dhttps%253A%252F%252Fgithub.com%252FVisActor)




