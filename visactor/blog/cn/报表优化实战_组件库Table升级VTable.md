---
title: 报表优化实战：组件库Table升级VTable

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
# **一、价值聚焦**

**渲染性能与动态能力全面升级**

*  **🚀 性能瓶颈突破**

*  **用户体验**：低配老旧机型使用流畅度提升，无卡顿现象，FPS 接近满帧

*  **关键指标**：页面浏览体感耗时降低 500ms ，INP 降低 50% 

*  **内存占用**：canvas 渲染模型替代 DOM 方案，内存使用、回收效率显著优化

*  **💡 动态功能开箱即用**

*  **交互能力**：自动列宽、列拖拽调整、多级表头、右键菜单等表格需求开发效率提升

*  **扩展潜力**：基于VRender 统一渲染引擎，与 VChart 同源技术栈，天然支持单元格内嵌图表、数据上下钻等 BI 场景

*  **🛠️ 架构解耦**

*  **技术债务清理**：通过适配层剥离旧组件库耦合，代码维护成本降低**，**代码量降低

*  **核心逻辑**： 基于 VTable API 设计，实现业务逻辑与 UI 渲染的解耦；将事件、主题、数据加载等逻辑拆分为独立模块，分批注入 



# **二、背景阐述**

**重构的必然性**

1. **关键指标劣化**

*  页面呈现体感耗时 5s （标准值需 < 2s），用户等待时间过久，影响用户体验，导致用户流失；

*   INP 700ms （web vitals 约定 < 200ms good， >500 ms poor ），交互卡顿导致操作中断率激增，操作运维效率降低

*  卡顿显著： 在 Intel i7 macbookpro 浏览、操作报表，FPS 平均 48 帧，且频繁掉帧

2. **技术债制约**

*  组件库版本：当前使用组件库0.x版 Table 不支持动态调整列宽、右键菜单、总计等功能，且0.x版本已不再维护，无法平滑升级 1.x 版本，此外升级后无法保证功能与性能问题得到解决

*  维护成本高：业务逻辑与UI渲染深度耦合，因 Table 组件缺失的能力，定制了多处散乱的 hack 逻辑，导致小需求大改动，开发耗时久，测试难以覆盖全

3. **业务需求**

*  专项治理：团队对报表模块开展易用性提升的专项梳理，专项包含了近20个需求点，旨在提升用户看数体验和效率，发挥与挖掘报表的价值。

*  长期规划：未来可能会支持更大数量级数据的可视化，支持更丰富的 BI 分析能力  

# **三、调研对比**

**Canvas vs DOM，VTable的优势**

1. **渲染引擎技术选型**  

**维度**

**对比项**

**DOM方案的组件库Table**

**Canvas方案的VTable**

**性能**



大数据渲染

超过千行可能出现明显卡顿（rerender开销）

可流畅处理万级行数据



内存占用

每个单元格都是独立DOM节点

只维护一个画布元素



首次渲染速度

取决数据量级，少块，多慢

需一次性计算绘制画布内的内容



滚动性能

可能触发频繁的重排重绘

增量渲染，渲染快速



响应速度

交互响应快，但大数据量下延迟明显

一致的响应速度，不受数据量影响



动态更新

单元格级别更新高效，整表更新较慢

双缓存，区域更新高效，频繁刷新性能好

**功能**



表头固定/列冻结

支持，大数据量下浏览会掉帧

支持，性能稳定不受数据量影响



自定义样式

受CSS限制，特殊效果实现复杂

高度自由，可实现任意效果



单元格合并

支持，配置简单

支持，自定义程度更高



行/列拖拽

支持，但需额外组件

原生支持，性能好



自定义渲染

灵活，可渲染任意 React 组件

有限，react18 以上版本支持自定义组件渲染，以下版本依赖提供的组件



数据导出

不支持，需自行实现

支持，且可自定义导出



透视表

不支持，需自行实现

支持，有一定学习成本



BI 分析能力

搜索、排序与过滤

基础分析能力外，还有聚合、总计及透视表分析能力

**开发**

学习曲线

平缓，符合React开发习惯

较陡，需要学习特定API



文档与搜索资源

文档完善，示例丰富。

文档、教程以及示例完善。搜索资源较少



社区活跃度

内部组件库，活跃一般

活跃度一般，持续增长中



TypeScript支持

完善

完善



调试便捷性

便捷

一般，依赖断点debugger，渲染层黑盒



集成成本

与 React 生态无缝集成

基于 react 封装 React-VTable，可 React 声明的方式来集成开发

1. **VTable 简介**

VisActor 是字节对外开源的，面向叙事的智能可视化解决方案。由 VChart(可视化组件)，VTable(数据组件)，VGrammar(语法组件)，VMind(图表智能生成组件)，VRender(渲染组件) 等多个组件组成。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/ACN6b8J6ZoPQDrxKePtcwgrunD3.gif' alt='' width='1000' height='auto'>



VTable 是 VisActor 可视化体系中的表格组件库，基于可视化渲染引擎 VRender 进行封装。核心能力如下

性能极致：支持百万级数据快速运算与渲染。



<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/SbQzbEyXWodNiZxgE0UcBSq8n2L.gif' alt='' width='516' height='auto'>

多维分析：多维数据自动分析与呈现

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/X5PsboPqQoztpCxuyfVcrICKnXd.gif' alt='' width='516' height='auto'>



表现力强：提供灵活强大的图形能力，无缝融合VChart

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/TELZbOgLIoeyUtxJxpJcbeSanOd.gif' alt='' width='516' height='auto'>

**选择VTable的核心理由**  

*  **场景**：业务场景适配，能解决核心痛点

*  **性能**：性能表现优异，内存占用低，滚动和动态更新体验流畅 

*  **功能**：丰富的开箱即用基础功能，透视表及 BI 分析能力为后产品迭代有更好想象空间

*  **生态**：作为VisActor家族一员，与其他生态内产品深度联动，拓展性更高



# **四、接入设计**

**架构设计与平滑迁移**

1. **适配接入**：切割业务与 vtable 的之间的耦合，提升各模块的拓展性与可维护性。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/PZSxbxrbhouyyjxSoZYc2SGCnje.gif' alt='' width='1000' height='auto'>



实现一个列构造器，定义通用的列配置项，不同的 Table Column 生成逻辑抽离到 adapter 中，通过 build 方法生成 column 配置项。

```
// 通用列构造器
export class TableColumn {
  private column: TableColumn;
  private adapter: ColumnAdapter;

  constructor(key: string, options?: { adapter?: ColumnAdapter }) {
    this.column = { key };
    this.adapter = options?.adapter;
  }

  setTitle(text: string, tooltip?: string) {
    this.column.title = {
      text,
      tooltip,
    };
  }
  
  // ...
  
  build(args?: unknow) {
    const column = this.adapter(this.column, args);
    return column;
  }

  setAdapter(adapter: TColumnAdapter) {
    this.adapter = adapter;
    return this;
  }
}

// VTable adapter
export function deliveryColumnAdapter(column: TableColumn) {
  const columnOfVTable = commonColumnAdapterOfVTable(column);
  columnOfVTable.icon = cell => {
    // ...
  };
  columnOfVTable.fieldFormat = record => {
    // ...
  };
  return columnOfVTable;
}

// 生产 VTable 的列配置
const getColumnOfVTable = (column:TableColumn, adapter: ColumnAdapter, args?: unknow) => {
  column.setAdapter(adapter);
  return column.build(args);
}

```
1. **主题定制**：基于 VTable 主题拓展能力，复刻原组件库 Table UI 设计，使页面风格保持一致。

1. **交互模块化：**右键菜单，表格事件，表格 Tooltip，复选框操作，表单后端排序等功能模块化，在 VTable 实例创建时注册使用。

1. **Icon 巧用：**Icon 在 VTable 中的使用灵活，也支持自定义注册、重置。将报表中使用的 Icon 收敛到一个模块下管理，注册后直接使用。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/KqJQbQZBQon5uUxqPptctN6XnCg.gif' alt='' width='1000' height='auto'>







# **五、实战演练**

1. ** VTable 高性能揭秘** ，渲染百万行数据

VRender 作为 VTable 的渲染引擎，提供的按需重绘及合并异步渲染能能力，支持了VTable的高性能渲染需求。如下图所示，在表格的滚动场景，滚动后的渲染是分为渲染缓存能覆盖的区域和渲染需要更新的区域，二者相比重新渲染所有内容速度提升显著。点击查看关于 VTable 更多[原理剖析](https://juejin.cn/post/7304538199144562724#heading-11)

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/NJkSbjxQEoTcSMxfzxIcKB7SnYg.gif' alt='' width='954' height='auto'>



1. **基于 VTable 加载，更新机制，封装一个 Table 组件**

VTable 内部的加载流程中经历了模块初始化、数据处理以及场景渲染。VTable 支持全量、单项以及批量三种更新方式

*  全量更新适用于需要对整个配置进行更改的情况

*  单项更新适用于修改少数几个配置项的场景

*  批量更新适用于批量更新多个配置项的情况，需要调用接口重新布局和渲染

下述代码，包含 VTable 实例初始化，模块注册，更新渲染以及实例卸载，所有逻辑封装成 React 组件，对外暴露部分 VTable Options。采用全量个更新方式，对多个配置项进行更改，避免多次布局和渲染，提高页面性能。

```
// 注册Icon
registerIcon();
const REPORT_CONTAINER_ID = 'basic_report_of_adspark';
const COMMON_STYLE_OPTION: ListTableConstructorOptions = {
  heightMode: 'standard',
  defaultRowHeight: DEFAULT_ROW_HEIGHT,
  defaultHeaderRowHeight: DEFAULT_HEADER_ROW_HEIGHT,
  autoFillWidth: true,
};
export const ReportBaseVTable = ({
  option,
  defaultSort,
}: {
  option: Omit<ListTableConstructorOptions, 'container'>;
  defaultSort: SortState;
}) => {
  // ...
  const tableInstance = useRef<ListTable>(null);
  
  const init = () => {
    registerCellTooltip(tableInstance.current);
    registerCellClick(tableInstance.current);
    registerCheckboxStateChangeEvent(tableInstance.current);
    registerCellDropdownMenuClick(tableInstance.current);
    registerTableSort(tableInstance.current);
    // ...
  };

  useEffect(() => {
    if (!option?.columns?.length) {
      return;
    }
    if (!tableInstance.current) {
      tableInstance.current = new ListTable({
        container: document.getElementById(REPORT_CONTAINER_ID),
      });
      init();
    }
    // 批量更新
    tableInstance.current?.updateOption({
      widthMode: 'autoWidth',
      heightMode: 'autoHeight',
      ...COMMON_STYLE_OPTION,
      ...option,
      theme: CustomTheme,
    });
    // 单独更新 sort
    tableInstance.current?.updateSortState(defaultSort, false);
  }, [defaultSort, option]);
  
  
  useUnmount(() => {
    tableInstance.current?.release();
    tableInstance.current = null;
  });

  return (
    <div
      style={{
        height: (tableInstance.current?.records?.length ?? 0) * DEFAULT_ROW_HEIGHT + DEFAULT_HEADER_ROW_HEIGHT,
        width: '100%',
      }}
      id={REPORT_CONTAINER_ID}
    ></div>
  );
};

```
1. **定制主题**

VTable 支持自定义主题，结合项目 UI 风格，基于默认的主题扩展出了一套定制主题。

```
/**
* 定义主题
*/
export const CustomTheme: ITableThemeDefine = VTable.themes.DEFAULT.extends({
  // 表头样式
  headerStyle: {
    // ...
    hover: {
      cellBgColor: '#f0f0f0',
    },
  },
  bodyStyle: { fontSize: 12, color: '#333', borderLineWidth: [1, 0, 1, 0], bgColor: '#fff' },
  frameStyle: { borderLineWidth: null, borderColor: '#fff' },
  // 选中太样式
  selectionStyle: {
    // ...
  },
  // 冻结列样式
  frozenColumnLine: {
    border: {
     // ...
    },
    shadow: {
    },
  },
  // 滚动条样式
  scrollStyle: {
    // ...
  },
  // tooltip 样式
  tooltipStyle: {
  //...
  },
});

// 替换主题
const option = {
 // ...
 theme: CustomTheme
 // ...
}

```
1. **自定义Tooltip**

业务场景中，部分单元格的 hover 需要  tooltip 的交互，实现思路监听单元格 mouseenter 事件，获取当前单元格内容和位置信息，判断是否需要 tooltip, 如果需要则使用 showTooltip API 来实现 tooltip 效果，API 支持 tooltip 内容、样式及动画的多个维度的配置。

```
/**
* 自定义单元格的Tooltip
*/
export function registerCellTooltip(tableInstance: ListTable) {
  tableInstance.on('mouseenter_cell', args => {
    const { col, row } = args;
    // 获取单元格内容
    const { dataValue, field } = tableInstance.getCellInfo(col, row);
    // ... 
    // 获取单元格位置信息
    const rect = tableInstance.getVisibleCellRangeRelativeRect({ col, row });
    // 展示 tooltip
    tableInstance.showTooltip(col, row, {
      content: getTooltipContent(dataValue, field),
      referencePosition: { rect, placement: VTable.TYPES.Placement.top },
      className: 'defineTooltip',
      disappearDelay: 60,
      style: {
        bgColor: 'black',
        color: 'white',
        arrowMark: true,
      },
    });
  });
}

```
1. **右键菜单**

浏览报表时，右键菜单能够提升用户操作报表的效率。VTable 支持自定义右键菜单，实现分为两步

*  定义 menu，配置菜单项的 key 与 text

*  注册菜单事件 dropdown_menu_click， 结合选择菜单项实现业务功能

```
export enum EContextMenuKey {
  // ...
  CopyId = 'copyId',
}
export const ContextToMenuMap = {
 // ...
  [EContextMenuKey.CopyId]: {
    key: EContextMenuKey.CopyId,
    text: '复制 ID',
  },
};

const option = {
// ...
 /**
  * 菜单配置
 */
 menu: {
        contextMenuItems: (field: string, row: number, col: number, table?: BaseTableAPI) =>  [
         {
            menuKey: EContextMenuKey.CopyId,
            text: ContextToMenuMap[EContextMenuKey.CopyId].text,
         }
        ]

//...
}

/**
* 菜单事件注册
*/
export function registerCellDropdownMenuClick(tableInstance: ListTable) {
  tableInstance.on('dropdown_menu_click', (args: DropDownMenuEventArgs) => {
    // ...
    let copy = tableInstance.getCellRawValue(args.col, args.row);
    if (args.menuKey === EContextMenuKey.CopyId) {
      copy = copy.split(REPORT_DATA_ID_SEPARATOR)?.[0];
    }
    navigator?.clipboard?.writeText?.(copy);
    // ... 
  });
}


```
1. **自定义icon**

VTable 中 Icon 应用场景广泛，我们可以通过 icon 和 headerIcon 来配置表头及 body 显示的单元格图标，icon 配置项丰富，涉及 Icon 内容，样式以及 tooltip 等，此外，我们可以自定义 icon 注册到表格中使用, 使用时只需配置 icon name 即可。

```
export enum TableIcon {
  // ...
  GoToAnchor = 'goToAnchor',
}
/**
* icon 定义
*/
export const ICON_MAP: Record<TableIcon, ColumnIconOption> = {
  [TableIcon.GoToAnchor]: {
    type: 'svg',
    name: TableIcon.GoToAnchor,
    svg:'https://xxxx',
    width: ICON_WIDHT,
    height: ICON_HEIGHT,
    // 所处位置
    positionType: VTable.TYPES.IconPosition.left,
    // tootip 配置
    tooltip: {
      style: {
        arrowMark: true,
      },
      title: '',
      placement: VTable.TYPES.Placement.right,
    },
    // hover 样式
    cursor: 'pointer',
  },
};
/**
* 注册 Icon
*/
export function registerIcon() {
  Object.values(ICON_MAP).forEach(icon => {
    VTable.register.icon(icon.name, icon);
  });
}
// 使用 icon 
const tableInstance = new VTable.PivotTable({
  columns: [
    {
      field: '',
      // ..
      headerIcon: TableIcon.GoToAnchor,
      icon: TableIcon.GoToAnchor
    }
  ]
});
/**
* icon 点击事件
*/
export function registerIconClick(tableInstance: ListTable) {
  tableInstance.on('click_cell', args => {
    // 获取Icon
    const { targetIcon, originData } = args;
    // 点击 Icon 的动作
    if (targetIcon?.name === TableIcon.GoToAnchor) {
      // ....
  });
}

```
# **六、效果验证**

**从技术指标到业务影响的全景呈现**  

1. **浏览流畅**

卡顿问题不复存在，对比效果在低配电脑感知更为明显。下图中新报表每页渲染50条数据，原报表渲染30条数据的渲染帧率对比。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/S79QbLmKMooE6qxx4pwcG3wlnUf.gif' alt='' width='974' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/KwgAbX6O4or8ioxJOTTcXRh1nfd.gif' alt='' width='1000' height='auto'>

1. **交互丰富**

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/XCNHbtQm0oTn8mxhTcrcDJPDn1g.gif' alt='' width='666' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/TSapbqDwXo1X7mxqM8EceYGfn9e.gif' alt='' width='666' height='auto'>

1. **性能指标量化对比**

**场景**

**原方案**

**VTable方案**

**提升**

交互浏览

平均48帧左右，帧率不稳定

平均58帧

+20%，且稳定性提升

INP

740ms

334ms

+54.8%

体感耗时

5s

2.6s

+48%(结合结合其他策略共同优化效果)

1. **业务价值**

*  **高频需求标准化**：右键菜单、单击事件等交互功能配置化、模块化的设计复用度高，需求迭代效率提升

*  **技术债务清理**：旧组件库强耦合代码移除，彻底解决升级困境 

1. **经验沉淀与未来规划**

*  **核心经验**：  

*  渲染引擎选型需匹配业务场景，结合项目历史迭代与技术栈

*  适配层设计是平滑迁移与技术可控性的关键

*  **未来演进**：  

*  **可视化融合**：VTable + VChart实现图表内嵌表格 

*  **智能化探索**：基于表格数据的二次分析与AI辅助分析

# 欢迎交流

最后，我们诚挚的欢迎所有对数据可视化感兴趣的朋友参与进来，参与 VisActor 的开源建设：

**VChart**：[VChart 官网](https://www.visactor.io/vchart)、[VChart Github（欢迎 Star）](https://github.com/VisActor/VChart)

**VTable**：[VTable 官网](https://www.visactor.io/vtable)、[VTable Github（欢迎 Star）](https://github.com/VisActor/VTable)

**VMind**：[VMind 官网](https://www.visactor.io/vmind)、[VMind Github（欢迎 Star）](https://github.com/VisActor/VMind)

官方网站：[www.visactor.io/](http://www.visactor.io/) 或 www.viactor.com

Discord：[discord.gg/3wPyxVyH6m](http://discord.gg/3wPyxVyH6m)

飞书群（外网）：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F40dcf4e6722d4925804361a2269991d8~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D264%26h%3D277%26s%3D35808%26e%3Dpng%26b%3Dfdfdfd)

微信公众号：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2Ff28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D258%26h%3D258%26s%3D8552%26e%3Dwebp%26b%3Dfefefe)

github：[github.com/VisActor](https://github.com/VisActor/)


