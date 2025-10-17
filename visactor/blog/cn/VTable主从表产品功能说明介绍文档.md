---
title: 主从表产品功能说明介绍文档    

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
# VTable 主从表插件

---


本文作者：    

薯片（https://github.com/Violet2314） 广东财经大学    



---


## 导读

VTable 主从表插件（MasterDetailPlugin）是基于 VisActor VTable 开发的企业级数据可视化组件，专门解决复杂业务场景中的层次化数据展示需求。该插件突破了传统表格的平面化限制，实现了在主表行内嵌入完整子表格的创新交互模式，为用户提供了直观、高效的数据钻取和详情查看体验    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/Kp1HblnjMo17QjxoAUqcwXUrng2.gif' alt='' width='889' height='auto' />

**核心能力**    

*  支持在主表行内嵌入完整的子表格，让复杂数据结构一目了然    

*  支持静态配置和动态函数配置，满足各种业务场景    

*  支持懒加载机制，优化大数据量场景下的性能表现    

### 典型业务场景

<table><colgroup><col style="width: 200px"><col style="width: 200px"><col style="width: 200px"></colgroup><tbody><tr><td rowspan="1" colspan="1">

业务场景    
</td><td rowspan="1" colspan="1">

主表数据    
</td><td rowspan="1" colspan="1">

子表数据    
</td></tr><tr><td rowspan="1" colspan="1">

订单管理    
</td><td rowspan="1" colspan="1">

订单基本信息    
</td><td rowspan="1" colspan="1">

商品清单、物流详情    
</td></tr><tr><td rowspan="1" colspan="1">

项目管理    
</td><td rowspan="1" colspan="1">

项目概览    
</td><td rowspan="1" colspan="1">

任务列表、成员分工    
</td></tr><tr><td rowspan="1" colspan="1">

财务管理    
</td><td rowspan="1" colspan="1">

汇总数据    
</td><td rowspan="1" colspan="1">

明细账目、凭证信息    
</td></tr><tr><td rowspan="1" colspan="1">

库存管理    
</td><td rowspan="1" colspan="1">

产品类别    
</td><td rowspan="1" colspan="1">

SKU详情、库存动态    
</td></tr><tr><td rowspan="1" colspan="1">

客户管理    
</td><td rowspan="1" colspan="1">

客户基本信息    
</td><td rowspan="1" colspan="1">

联系记录、交易历史    
</td></tr></tbody></table>
## 快速上手

### 获取NPM包

首先，你需要在项目根目录下使用以下命令安装：    

```bash
*# 使用 npm 安装*
npm install @visactor/vtable @visactor/vtable-plugins

*# 使用 yarn 安装  *
yarn add @visactor/vtable @visactor/vtable-plugins    

```
### 引入主从表插件

通过 NPM 包引入    

```xml
import * as VTable from '@visactor/vtable';
import { MasterDetailPlugin } from '@visactor/vtable-plugins';

function generateData(*count*) {
  const depts = ['Engineering', 'Marketing', 'Sales', 'HR', 'Finance'];
  return Array.from({ length: count }).map((*_*, *i*) => ({
    id: i + 1,
    rowNo: i + 1,
    name: `姓名 ${i + 1}`,
    department: depts[i % depts.length],
    score: Math.floor(Math.random() * 100),
    amount: Math.floor(Math.random() * 10000) / 100,
    children:
      i % 4 === 0
        ? [
            { task: `子任务 A-${i + 1}`, status: 'open' },
            { task: `子任务 B-${i + 1}`, status: 'done' }
          ]
        : undefined
  }));
}

const records = generateData(11);
 const masterDetailPlugin = new VTablePlugins.MasterDetailPlugin({
id: 'master-detail-static-3',
detailTableOptions: {
  columns: [
    { field: 'task', title: '任务名', width: 220 },
    { field: 'status', title: '状态', width: 120 }
  ],
  defaultRowHeight: 30,
  defaultHeaderRowHeight: 30,
  style: { margin: 12, height: 160 },
  theme: VTable.themes.BRIGHT
}
});

const columns = [
{ field: 'id', title: 'ID', width: 70, sort: true },
{ field: 'rowNo', title: '#', width: 60, headerType: 'text', cellType: 'text' },
{ field: 'name', title: '姓名', width: 140, sort: true },
{ field: 'department', title: '部门', width: 140, sort: true },
{ field: 'score', title: '分数', width: 100, sort: true },
{
  field: 'amount',
  title: '金额',
  width: 120,
  sort: true,
  fieldFormat: (*v*) => {
    if (typeof v === 'number' && !isNaN(v)) {
      return `$${v.toFixed(2)}`;
    }
    return v === undefined || v === null ? '' : String(v);
  }
}
];

const option = {
  container: document.getElementById(CONTAINER_ID),
  columns,
  records,
  autoFillWidth: true,
  hierarchyTextStartAlignment: true,
  plugins: [masterDetailPlugin]
};

const tableInstance = new VTable.ListTable(option);

    

```
运行后效果：    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/IsMmblEO0oypCdxC0r9cQHI7n9c.gif' alt='' width='889' height='auto' />

## 参数配置

<table><colgroup><col style="width: 200px"><col style="width: 200px"><col style="width: 200px"><col style="width: 200px"></colgroup><tbody><tr><td rowspan="1" colspan="1">

参数名称    
</td><td rowspan="1" colspan="1">

类型    
</td><td rowspan="1" colspan="1">

默认值    
</td><td rowspan="1" colspan="1">

功能说明    
</td></tr><tr><td rowspan="1" colspan="1">

`id`    
</td><td rowspan="1" colspan="1">

string    
</td><td rowspan="1" colspan="1">

`master-detail-${timestamp}`    
</td><td rowspan="1" colspan="1">

插件实例的全局唯一标识符，用于区分多个插件实例    
</td></tr><tr><td rowspan="1" colspan="1">

`enableCheckboxCascade`    
</td><td rowspan="1" colspan="1">

boolean    
</td><td rowspan="1" colspan="1">

`true`    
</td><td rowspan="1" colspan="1">

是否启用主从表之间的checkbox级联功能，主表中的复选框选择会自动与相应的子表同步    
</td></tr><tr><td rowspan="1" colspan="1">

`detailTableOptions`    
</td><td rowspan="1" colspan="1">

DetailTableOptions | Function    
</td><td rowspan="1" colspan="1">

-    
</td><td rowspan="1" colspan="1">

子表配置选项，支持静态对象配置或基于数据的动态配置函数    
</td></tr></tbody></table>
动态配置示例    

```xml
const masterDetailPlugin = new MasterDetailPlugin({
  id: 'employee-detail-plugin',
  detailTableOptions: ({ *data*, *bodyRowIndex* }) => {
    if (bodyRowIndex === 0) {
      return {
        columns: [
        //......
        ],
        theme: VTable.themes.BRIGHT,
        style: {
          margin: 20,
          height: 300
        }
      };
    }
    return {
      columns: [
      //......
      ],
      theme: VTable.themes.DARK,
      style: {
        margin: 20,
        height: 300
      }
    };
  }
});    

```
## 主从表插件的主要能力

### 展开行和渲染子表

当用户点击表格行的展开图标时，系统会在该行下方动态创建一个完整的子表格实例。子表格具备独立的配置、数据源和交互能力，与主表形成层次化的数据展示结构。    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/WwBab0wApoR3aKx3JiQcWxm8nxb.gif' alt='' width='1000' height='auto' />

当用户点击展开图标时，会触发以下处理链：    

```markdown
用户点击展开图标
    ↓
EventManager.handleIconClick()
    ↓ 
MasterDetailPlugin.expandRow(rowIndex, colIndex)
    ↓
检查行是否已展开 (isRowExpanded)
    ↓
获取记录数据 (getRecordByRowIndex)
    ↓
MasterDetailPlugin.getChildren()
    ↓
ConfigManager.getDetailConfigForRecord()
    ↓
updateRowHeightForExpand()
    ↓
updateContainerHeight()
    ↓
SubTableManager.renderSubTable()
    ↓
recalculateAllSubTablePositions(*bodyRowIndex* + 1)
    ↓
drawUnderlineForRow()
    ↓
refreshRowIcon()    

```
**技术特性**    

*  **独立实例**: 每个子表都是完整的VTable实例，支持所有表格功能    

*  **动态行高**: 主表行高自动适配子表内容，支持自适应和固定高度    

*  **位置同步**: 主表滚动时子表位置实时跟随，保持视觉连贯性    

*  **内存管理**: 收起时自动销毁子表实例，展开时重新创建    

**生命周期管理**    

创建阶段: 主表行展开 → 解析配置 → 实例化子表    

运行阶段: 位置同步 → 事件处理 → 数据更新    

销毁阶段: 行收起 → 清理事件 → 销毁实例 → 回收内存    

### 滚动同步机制

主表滚动时，所有子表会实时跟随滚动，保持视觉上的一体化效果。同时优化了滚动事件的触发机制，并且还有主子表的滚动分离机制，即当鼠标在子表内滚动时并且没有滚动到上下边界的时候，我滚动的是子表的内容，主表是不会滚动的，主表滚动的时候子表是不会滚动的    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/Dc2ybnrz4oDx7Ix053UcIEj9nYN.gif' alt='' width='1000' height='auto' />

**技术特性**    

*  scrollEventAlwaysTrigger: 自动设置为true，确保边界滚动也能触发事件    

*  批量更新: 一次滚动事件批量更新所有子表位置    

### 主从表checkbox联动功能

主从表checkbox联动实现了主表与子表之间选择状态的智能同步。当主表行被选中时，其对应的子表中所有行自动选中；当子表中部分行被选中/取消选中时，主表行的选择状态会相应更新状态。使用参数`enableCheckboxCascade`来决定是否开启，默认是true    

```xml
主表checkbox点击 → 检测子表存在 → 遍历子表所有行 → 同步选择状态 → 触发联动回调
子表checkbox点击 → 统计子表选中数量 → 计算主表状态 → 更新主表checkbox → 触发联动回调    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/RKTZbwYPPoUVCLxxbUkcJlLfnzf.gif' alt='' width='1000' height='auto' />

**技术实现**    

*  **事件监听**: 监听主表和子表的checkbox变化事件    

*  **状态同步**: 通过内部状态管理器维护选择状态映射关系    

*  **回调通知**: 选择状态变化时触发相应回调函数，便于业务处理    

这种联动机制大大提升了复杂数据结构的操作效率，用户可以通过简单的点击实现批量选择操作。    

### 懒加载功能

当父行记录的 children 字段为 true 时，表示这是一个懒加载节点，需要通过监听 VTable TREE_HIERARCHY_STATE_CHANGE 事件来实现异步数据获取    

当用户点击包含懒加载节点的展开图标时，会触发以下调用链：    

```markdown
用户点击展开图标
    ↓
table.toggleHierarchyState()
    ↓
触发 TREE_HIERARCHY_STATE_CHANGE 事件
    ↓
用户事件处理器接收事件参数
    ↓
tableInstance.setLoadingHierarchyState(col, row) 显示loading图标
    ↓
异步数据获取
    ↓
plugin.setRecordChildren(detailData, col, row) 设置数据并展开
    ↓
渲染子表并完成展开    

```
<table><colgroup><col style="width: 200px"><col style="width: 200px"><col style="width: 200px"></colgroup><tbody><tr><td rowspan="1" colspan="1">

方法    
</td><td rowspan="1" colspan="1">

说明    
</td><td rowspan="1" colspan="1">

参数    
</td></tr><tr><td rowspan="1" colspan="1">

`plugin.setLoadingHierarchyState(col, row)`    
</td><td rowspan="1" colspan="1">

显示loading图标    
</td><td rowspan="1" colspan="1">

col: 列索引, row: 行索引    
</td></tr><tr><td rowspan="1" colspan="1">

`plugin.setRecordChildren(children, col, row)`    
</td><td rowspan="1" colspan="1">

设置子数据并展开    
</td><td rowspan="1" colspan="1">

children: 子数据数组, col: 列索引, row: 行索引    
</td></tr></tbody></table>
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/QXLbbYefJoPDo4xZGTjcar9tn1e.gif' alt='' width='704' height='auto' />

**用户配置示例**    

```xml
const masterData = [
  { id: 1, name: '订单001', children: true },    *// 懒加载标识*
  { id: 2, name: '订单002', children: [...] }   *// 静态数据*
];

const plugin = new MasterDetailPlugin({
  detailTableOptions: {
    *// 子表配置*
  }
});

const tableInstance = new VTable.ListTable(options);

*// 监听主从表层次状态变化事件*
const { MASTER_DETAIL_HIERARCHY_STATE_CHANGE } = VTable.ListTable.EVENT_TYPE;
tableInstance.on(MASTER_DETAIL_HIERARCHY_STATE_CHANGE, async (*args*) => {
  if (args.hierarchyState === VTable.TYPES.HierarchyState.expand && 
      args.originData?.children === true) {
    
    *// 显示loading状态*
    plugin.setLoadingHierarchyState(args.col, args.row);
    
    try {
      *// 异步数据获取*
      const detailData = await fetchDataFromAPI(args.originData.id);
      
      *// 设置子数据并自动展开*
      plugin.setRecordChildren(detailData, args.col, args.row);
    } catch (error) {
      console.error('Failed to load detail data:', error);
    }
  }
});    

```
**智能缓存机制**    

*  **一次加载**: 数据加载成功后，`children: true` 自动转换为实际数据数组    

*  **永久缓存**: 再次点击时识别为静态数据，直接展开无需重新加载    

这种设计实现了透明的按需加载，用户只需配置 `onLazyLoad` 回调，其余状态管理完全由插件自动处理。    

## 小结

VTable 主从表插件是一个正在成长的开源数据展示插件，我们致力于为Web应用提供优秀的层次化数据展示解决方案。    

它将复杂的主从数据关系展示能力带到了Web端，同时充分发挥了现代Web技术的交互优势。无论您是要构建订单管理系统、项目管理平台、财务报表系统，还是任何需要主从表功能的Web应用，VTable主从表插件都能为你提供一个良好的选择    

## 在线demo和教程

demo： [https://visactor.com/vtable/demo/table-type/list-table-master-detail-table](https://visactor.com/vtable/demo/table-type/list-table-master-detail-table)
教程： [https://visactor.com/vtable/guide/plugin/master-detail](https://visactor.com/vtable/guide/plugin/master-detail)    

## 欢迎交流

最后，我们诚挚的欢迎所有对数据可视化感兴趣的朋友参与进来，参与 VisActor 的开源建设：    

**VTable**：[VTable 官网](https://www.visactor.io/vtable)、[VTable Github（欢迎 Star）](https://github.com/VisActor/VTable)    

VisActor 官方网站：[www.visactor.io/](http://www.visactor.io/) 或 www.viactor.com    

Discord：[discord.gg/3wPyxVyH6m](http://discord.gg/3wPyxVyH6m)    

飞书群（外网）：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F40dcf4e6722d4925804361a2269991d8~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D264%26h%3D277%26s%3D35808%26e%3Dpng%26b%3Dfdfdfd)    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/DghNbaPU2oHcYXxSb4Ics97knxd.gif' alt='' width='264' height='auto' />

微信公众号：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2Ff28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D258%26h%3D258%26s%3D8552%26e%3Dwebp%26b%3Dfefefe)    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/V4MNbOXZ4osErVxWToRcZRTUn9c.gif' alt='' width='258' height='auto' />

github：[github.com/VisActor](https://github.com/VisActor/)    






