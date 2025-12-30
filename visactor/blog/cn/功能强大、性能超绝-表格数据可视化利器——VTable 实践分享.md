---
title: 功能强大、性能超绝：表格数据可视化利器——VTable 实践分享    

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
本文来自VTable 用户 LLmoskk（ https://github.com/LLmoskk）投稿。    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/AG51bUcp1otOWnx23U1lJCJKgZ1.gif' alt='' width='397' height='auto' />

---
# VTable简介

引用自官网:     

VTable: 不只是高性能的多维数据分析表格，更是行列间创作的方格艺术家！    

在现代应用程序中，表格组件是不可或缺的一部分，它们能够快速展示大量数据，并提供良好的可视化效果和交互体验。VTable是一款基于可视化渲染引擎VRender的高性能表格组件库，为用户提供卓越的性能和强大的多维分析能力，以及灵活强大的图形能力。    

官网链接: https://visactor.com/vtable    

github地址: https://github.com/VisActor/VTable    

# 创建项目

准备分享几个简单的实践案例，我目前主要使用的是基础表格ListTable在react项目中，所以案例基本也都是ListTable的，后续在项目中使用到了其他表格的过程中遇到值得分享的内容也会继续分享的。    

全部代码示例: https://github.com/LLmoskk/vtable-demo     

在线预览: https://llmoskk.github.io/vtable-demo/    

先起一个react项目，经典vite起手，注: react-vtable React 19 还不被支持 我们需要使用18版本的react。    

# 指挥 AI + MCP 快速准确的使用 vtable

我目前使用的AI IDE是 AWS 的 krio，我们先添加context7 mcp， 这个mcp的作用是让ai获取最新的文档，避免使用过时的api。现在主流的IDE基本都支持添加MCP了，不在此一一举例。    

```json
{
  "mcpServers": {
    "context7": {
      "args": [
        "-y",
        "@upstash/context7-mcp@latest"
      ],
      "command": "npx",
      "disabled": false,
      "autoApprove": [
        "resolve-library-id"
      ]
    },
  }
}    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/Sm8nbekaQoAv2BxLCoZl4tMKg4f.gif' alt='' width='484' height='auto' />

可以看到已经成功调用mcp工具去获取文档信息了。我要求他实现一个树形表格。    

很快啊！ ai就写完了。效果还不错，这里就不贴代码了，没有太多参考价值，只是用来演示一下使用ai + mcp结合快速的实现vtable的功能，无需自己去翻阅文档了。    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/UHhzbGnWYoZ10hxKcvrlg1HxgWe.gif' alt='' width='1000' height='auto' />

# 实现计算数据并持久化记忆修改拖拽列宽度

**项目场景概述：**    

在处理数据量较大的表格时，若将所有列的宽度固定为120px，可能会导致某些数据较少的列占据过多的空间。因此，提出了一个解决方案：动态调整列宽，根据数据值自动测量并设置每一列的最终宽度。    

1. **列宽自适应**：通过分析表格前20行的数据，我们将动态计算各列的最佳宽度，以确保表格布局更加合理。    

1. **用户自定义拖拽**：允许用户手动拖动列边界调整列宽。拖拽后的宽度将在本地存储（localStorage）中保存，有效提升用户体验。    

1. **加载优先级机制**：表格加载时，将优先考虑以下顺序来设置列宽：    

*  本地存储中的宽度    

*  自动计算得出的宽度    

*  默认宽度设置    

将编写一个名为 `use-column-width.ts` 的文件，封装一个自定义Hook。该Hook的功能是按照上述逻辑计算列宽，并与用户拖拽的宽度进行记忆。我们将利用ahook中的 `useLocalStorageState` 来保持状态信息同步存储到 localStorage 中。    

~~测量文本宽度 ~~~~*参考文章 *~~~~*https://juejin.cn/post/7091990279565082655*~~~~*， *~~~~完整代码不在这里贴出了可访问仓库自取 ~~~~https://github.com/LLmoskk/vtable-demo/blob/main/src/pages/demo1/utils/calculate-column-width.ts~~    

(不需要自己写了，可以用vtable写好的measureText 😅)    

如果没有定制要求的话可以直接使用表格的 `自动列宽模式（autoWidth）` 已经帮忙计算过一遍了。    

vtable文字测量方法: https://github.com/VisActor/VUtil/blob/main/packages/vutils/src/graphics/text/measure/textMeasure.ts    

https://www.npmjs.com/package/@visactor/vutils    

```xml
import { useLocalStorageState } from 'ahooks';
import { useCallback, useMemo } from 'react';
import { type ColumnDefine } from '@visactor/vtable';
import { calculateColumnsWidthMap } from '../utils/calculate-column-width';
import type { Sort } from '../type';

type UseColumnWidthParams<T extends ColumnDefine> = {
  */** 列配置数组 */*
  columns?: T[];
  */** localStorage的key */*
  storageKey: string;
  */** 表格数据，用于计算列宽 */*
  data?: any[];
  */** 默认列宽 */*
  defaultWidth?: number;
  */** 视图ID */*
  viewId?: string | number;
  */** 是否已排序的信息 */*
  sorts?: Sort[];
};

type UseColumnWidthReturn<T extends ColumnDefine> = {
  */** 应用了列宽的列配置（优先级：本地存储 > 计算值 > 默认值） */*
  columnsWithWidth: T[];
  */** 保存列宽的函数 */*
  saveColumnWidths: (colWidths: number[]) => void;
  */** 列宽映射对象 */*
  columnWidths: Record<string, number>;
};

*/***
* * 管理表格列宽持久化的hook*
* * 当columns的field顺序发生变化时，会自动清理不存在的列宽设置*
* **
* * 列宽优先级：本地存储 > 计算值 > 默认值*
* */*
const useColumnWidth = <T extends ColumnDefine>({
  columns,
  storageKey,
  data = [],
  defaultWidth = 120,
  sorts,
}: UseColumnWidthParams<T>): UseColumnWidthReturn<T> => {
  const [storedValue, setStoredValue] = useLocalStorageState<any>(storageKey, {
    defaultValue: {},
  });
  const columnWidths = storedValue;

  const calculatedWidthMap = useMemo(
    () =>
      calculateColumnsWidthMap(
        columns,
        data,
        sorts,
      ),
    [columns, data, sorts],
  );

  *// 保存列宽到 localStorage*
  const saveColumnWidths = useCallback(
    (colWidths: number[]) => {
      const widthMap: Record<string, number> = {};
      columns?.forEach((col, index) => {
        if (colWidths[index]) {
          widthMap[String(col.field)] = colWidths[index];
        }
      });

      setStoredValue(widthMap);
    },
    [columns, setStoredValue],
  );

  *// 应用列宽到列配置*
  *// 优先级：本地存储 > 计算值 > 默认值*
  const columnsWithWidth = useMemo(() => {
    return columns?.map((col) => {
      *// checkbox 固定 40 cellType 可能为函数*
      if (col.cellType === 'checkbox' || col.headerType === 'checkbox') {
        return {
          ...col,
          width: 40,
        };
      }

      *// 优先使用本地存储的宽度*
      if (columnWidths?.[String(col.field)]) {
        return {
          ...col,
          width: columnWidths[String(col.field)],
        };
      }

      *// 其次使用计算的宽度*
      const calculatedWidth = calculatedWidthMap.get(String(col.field));
      if (calculatedWidth) {
        return {
          ...col,
          width: calculatedWidth,
        };
      }

      *// 最后使用默认宽度*
      return {
        ...col,
        width: defaultWidth,
      };
    });
  }, [columns, columnWidths, calculatedWidthMap, defaultWidth]);

  return {
    columnsWithWidth: columnsWithWidth || [],
    saveColumnWidths,
    columnWidths: columnWidths || {},
  };
};

export default useColumnWidth;
    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/INtCbr51MogF5rxvfjelaqxVgNc.gif' alt='' width='1000' height='auto' />

# 主题切换

vtable提供了`Arco design`的主题与 `Dark Mode`的主题，我的项目是由`Arco design`的table切换到vtable的，所以使用自官方提供的主题包，并且我的项目需要适配暗色模式，于是也用到了 `Dark Mode` 来定制修改了下，基本配置信息都可以在文档中查看 https://visactor.com/vtable/option/ListTable#theme    

内置主题有五个    

```gherkin
const builtinThemes = [
    { key: 'DEFAULT', name: '默认主题', theme: themes.DEFAULT },
    { key: 'DARK', name: '暗色主题', theme: themes.DARK },
    { key: 'BRIGHT', name: '明亮主题', theme: themes.BRIGHT },
    { key: 'ARCO', name: 'Arco主题', theme: themes.ARCO },
    { key: 'SIMPLIFY', name: '简约主题', theme: themes.SIMPLIFY }
  ];    

```
我们还可以extends主题进行扩展定制，我们系统中是由arco design的Table组件迁移至vtable的，切换到ARCO主题后，发现还是和组件库的表格有些不同的，因此我做了些样式覆写。    

```xml
import { themes } from "@visactor/vtable";

*/***
* * 获取通用的 VTable 主题配置*
* */*
export const getCommonVTableTheme = () => {
    return themes.ARCO.extends({
            frameStyle: {
                borderLineWidth: 0,
            },
            headerStyle: {
                bgColor: '#F0F1F5',
                fontSize: 12,
                fontWeight: 400,
                autoWrapText: true,
                lineClamp: 3,
            },
            bodyStyle: {
                *// 使用函数动态设置背景色，如果是 aggregation 行则使用 headerStyle 的样式*
                bgColor: (args: any) => {
                    *// 检查是否是 aggregation 行*
                    if (args.table && typeof args.table.isAggregation === 'function') {
                        const isAggregationCell = args.table.isAggregation(
                            args.col,
                            args.row,
                        );
                        if (isAggregationCell) {
                            return '#F0F1F5';
                        }
                    }
                    return '#FFFFFF';
                },
            },
            bottomFrozenStyle: {
                bgColor: '#F0F1F5',
                fontWeight: 600,
                fontSize: 14,
            },
            tooltipStyle: {
                bgColor: 'black',
                color: 'white',
                fontSize: 12,
                padding: [8, 12, 8, 12],
            },
            scrollStyle: {
                visible: 'always', *// 滚动条始终显示*
                hoverOn: false, *// 滚动条不悬浮在内容上，而是独立显示*
            },
            selectionStyle: {
                cellBgColor: 'rgba(133,165,242,0.2)',
            },
        });
    }
};
    

```
对单元格的条件定制，遍历列配置后可以单独根据条件设置单元格样式    

```xml
const salesColumn = baseColumns.find(col => col.field === 'sales');
  if (salesColumn) {
    (salesColumn as any).style = {
      color: (args: any) => {
        const value = args.dataValue;
        if (value >= 150000) return '#059669'; *// 绿色：高销售额*
        if (value >= 100000) return '#0891b2'; *// 蓝色：中等销售额*
        return '#dc2626'; *// 红色：低销售额*
      },
      fontWeight: (args: any) => {
        return args.dataValue >= 150000 ? 'bold' : 'normal';
      }
    };
  }    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/XUiibbCGDos9uqxcwQHlq8Mgg3e.gif' alt='' width='1000' height='auto' />

# 表格转置与冻结表格

冻结列功能配置: https://visactor.com/vtable/guide/basic_function/frozen_column_row    

在后台系统看表的时候一般需要固定左侧的几列。支持顶部、底部、左侧、右侧的冻结设置。    

我在项目中遇到的问题就是切换主题到暗色的时候，表格固定按钮的图标没有跟随主题变化，通过查询文档得知可以注册 icon 自定义图标的一些配置。然后我从源码中获取到原始的 svg 传入跟随主题变化的 `frozen_color`    

注册icon: https://visactor.com/vtable/guide/custom_define/custom_icon    

```cplusplus
export const registerVtableIcon = (isDark?: boolean) => {
  const frozen_size = 22;
  const frozen_size_2 = 22;
  const frozen_color = isDark ? '#FFFFFF' : '#282F38';
  const frozen_color_opacity = '0.35';
  const freeze_color_opacity = '0.2';

  register.icon('frozen', {
    type: 'svg',
    svg:
      '<svg width="22" height="22" viewBox="0 0 22 22" fill="none" xmlns="http://www.w3.org/2000/svg">' +
      `<path d="M8.49975 3.66663C8.32294 3.66663 8.15337 3.73686 8.02835 3.86189C7.90332 3.98691 7.83309 4.15648 7.83309 4.33329V9.63246C6.76475 10.2533 6.07942 11.1795 6.00625 12.2308C5.99892 12.2786 5.99692 12.3268 6.00009 12.3741L5.99975 12.4166C5.99975 12.5934 6.06999 12.763 6.19501 12.888C6.32004 13.0131 6.48961 13.0833 6.66642 13.0833H10.3333L10.3331 17.5L10.8611 18.292C10.8763 18.3148 10.8969 18.3335 10.9211 18.3464C10.9453 18.3594 10.9723 18.3662 10.9998 18.3662C11.0272 18.3662 11.0542 18.3594 11.0784 18.3464C11.1026 18.3335 11.1232 18.3148 11.1384 18.292L11.6664 17.5L11.6666 13.0833H15.3331C15.5099 13.0833 15.6795 13.0131 15.8045 12.888C15.9295 12.763 15.9998 12.5934 15.9998 12.4166C15.9998 12.4025 15.9998 12.3883 15.9994 12.3741C16.0028 12.3263 16.0008 12.2776 15.9933 12.2295C15.9196 11.1786 15.2343 10.2528 14.1664 9.63229V4.33329C14.1664 4.15648 14.0962 3.98691 13.9712 3.86189C13.8461 3.73686 13.6766 3.66663 13.4998 3.66663H8.49975Z" fill="${frozen_color}" fill-opacity="${frozen_color_opacity}"/>` +
      '</svg>',
    width: frozen_size,
    height: frozen_size,
    name: 'frozen',
    funcType: IconFuncTypeEnum.frozen,
    positionType: IconPosition.right,
    marginRight: 0,
    hover: {
      width: frozen_size_2,
      height: frozen_size_2,
      bgColor: 'rgba(101, 117, 168, 0.1)',
    },
    cursor: 'pointer',
  });

  register.icon('freeze', {
    type: 'svg',
    svg:
      '<svg width="22" height="22" viewBox="0 0 22 22" fill="none" xmlns="http://www.w3.org/2000/svg">' +
      '<g clip-path="url(#clip0)">' +
      `<path d="M17.1313 8.42047C17.1932 8.48238 17.2423 8.55587 17.2759 8.63676C17.3094 8.71764 17.3266 8.80434 17.3266 8.89189C17.3266 8.97944 17.3094 9.06613 17.2759 9.14702C17.2423 9.2279 17.1932 9.3014 17.1313 9.3633L13.3843 13.1103C13.7007 14.3048 13.5305 15.4443 12.8388 16.2395C12.8104 16.2781 12.7778 16.3136 12.7417 16.3451L12.712 16.3755C12.6501 16.4374 12.5766 16.4865 12.4957 16.52C12.4148 16.5535 12.3281 16.5707 12.2406 16.5707C12.153 16.5707 12.0663 16.5535 11.9854 16.52C11.9046 16.4865 11.8311 16.4374 11.7692 16.3755L9.17633 13.7826L6.05316 16.9058L5.11983 17.0925C5.09291 17.0979 5.06508 17.0965 5.03881 17.0886C5.01254 17.0806 4.98863 17.0663 4.96923 17.0469C4.94982 17.0275 4.9355 17.0036 4.92755 16.9773C4.9196 16.951 4.91827 16.9232 4.92366 16.8963L5.11033 15.963L8.23333 12.8396L5.64066 10.2471C5.57875 10.1852 5.52964 10.1117 5.49614 10.0309C5.46263 9.94997 5.44539 9.86327 5.44539 9.77572C5.44539 9.68817 5.46263 9.60148 5.49614 9.52059C5.52964 9.43971 5.57875 9.36621 5.64066 9.3043C5.65066 9.2943 5.66066 9.2843 5.67099 9.27464C5.70266 9.2383 5.73833 9.20547 5.77766 9.17664C6.57283 8.48564 7.71199 8.31564 8.90599 8.63197L12.6528 4.88497C12.7147 4.82306 12.7882 4.77395 12.8691 4.74045C12.95 4.70694 13.0367 4.6897 13.1242 4.6897C13.2118 4.6897 13.2985 4.70694 13.3794 4.74045C13.4603 4.77395 13.5338 4.82306 13.5957 4.88497L17.1312 8.42047H17.1313ZM15.7172 8.8918L13.1243 6.29914L9.56483 9.8588C9.47574 9.94788 9.36323 10.0099 9.24034 10.0376C9.11746 10.0654 8.98922 10.0578 8.87049 10.0156C8.22783 9.78764 7.63899 9.7553 7.17749 9.89814L12.1182 14.8388C12.261 14.3771 12.2287 13.7885 12.0007 13.146C11.9585 13.0272 11.9509 12.899 11.9787 12.7761C12.0064 12.6532 12.0684 12.5407 12.1575 12.4516L15.7172 8.89164V8.8918Z" fill="${frozen_color}" fill-opacity="${freeze_color_opacity}"/>` +
      '</g>' +
      '<defs>' +
      '<clipPath id="clip0">' +
      '<rect width="22" height="22" fill="white"/>' +
      '</clipPath>' +
      '</defs>' +
      '</svg>',
    width: frozen_size,
    height: frozen_size,
    name: 'freeze',
    funcType: IconFuncTypeEnum.frozen,
    positionType: IconPosition.right,
    marginRight: 0,
    hover: {
      width: frozen_size_2,
      height: frozen_size_2,
      bgColor: 'rgba(101, 117, 168, 0.1)',
    },
    cursor: 'pointer',
  });
};    

```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/IMOrb1hRVohOI5xpledlUJxUgad.gif' alt='' width='1000' height='auto' />

表格转置设置下`transpose` 即可行转列，转置表格特别适合数据列很多但行数较少的场景，虽然目前我的项目中还未使用到。    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/Br1YbvnjWolKNvxRDm3lcUXogqg.gif' alt='' width='1000' height='auto' />

# 表格尺寸配置

行高列宽文档: https://visactor.com/vtable/guide/basic_function/row_height_column_width    

widthMode有三种模式 `'standard' | 'adaptive' | 'autoWidth'`    

如果没有业务特别定制的列宽要求的话，设置 `自动列宽模式（autoWidth）`最佳，可以根据列头和 body 单元格中的内容自动计算列宽度，忽略设置的 `width` 属性和 `defaultColWidth`。但计算会浪费一些性能，就看自己的取舍了。    

还有值得注意的一点是最好设置表格 `maxWidth+minWidth`因为我们允许用户自由拖拽列宽了，但又不希望无限制，所以一般我都会加上边界限制。    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/WfgcbGH5tol1FxxK7zplP8lBgSb.gif' alt='' width='1000' height='auto' />

# 表头固定悬浮

传统的dom表格可以设置表头粘性定位，但我使用vtable的时候想要实现这个功能，只能让整体vtable的dom粘性了，效果不是很好，最后想到的办法是让vtable的高度尽量保持一屏。就可以实现类似表头粘性定位的交互了。    

```xml
  const [tableHeight, setTableHeight] = useState(500); // 默认撑开的一个高度
  
  useEffect(() => {
    const calculateHeight = () => {
      *// 计算表格高度：视口高度 - 顶部导航栏(60) - 底部间距(16) - Tabs高度(36) - 分页器高度(32)*
      *// 目标是让表格高度占满一屏，这样当滚动到底部时，表格正好铺满屏幕*
      const height = window.innerHeight - 60 - 16 - 36 - 32 - 80;
      setTableHeight(*Math*.max(height, 400)); *// 设置最小高度*
    };

    calculateHeight();
    window.addEventListener('resize', calculateHeight);

    return () => {
      window.removeEventListener('resize', calculateHeight);
    };
  }, []);
  
  <ListTable
    // ...
    height={tableHeight}
  />
    

```
# 复制表格内容

vtable支持添加 keyboardOptions copySelected 即可开启表格ctrl + c 复制到能力。    

```xml
keyboardOptions={{
    copySelected: true,
}}    

```
但是项目中的表格有些单元格我使用了 customRender 自定义渲染的能力，我将一个 json的数据转换为icon + 文本的呈现形式，这时候复制到话就会出问题。因此需要`formatCopyValue` https://visactor.com/vtable/option/ListTable#formatCopyValue((value:%20string)%20=%3E%20string)    

```xml
{
  "icon": "xxxx",
  "text": "Demo"
}    

```
```xml
export const formatCopyValueForVTable = (value: unknown): string => {
  const toStr = (v: unknown) => (v == null ? '' : String(v));
  if (typeof value !== 'string') return toStr(value);

  const parseCell = (cell: string) => {
    const trimmed = cell.trim();
    if (trimmed.startsWith('{') && trimmed.endsWith('}')) {
      try {
        const obj = *JSON*.parse(trimmed);
        if (obj && typeof obj === 'object') {
          *// 优先检查 describe 字段（asset 对象）*
          if ('describe' in obj) {
            return toStr((obj as any).describe ?? '');
          }
          *// 其次检查 text 字段（icon-text 对象）*
          if ('text' in obj) {
            return toStr((obj as any).text ?? '');
          }
        }
      } catch (_) {
        *// 非合法 JSON，保持原样*
      }
    }
    return cell;
  };

  return value
    .split('\n')
    .map((line) => line.split('\t').map(parseCell).join('\t'))
    .join('\n');
};    

```
这样复制出来的内容就正常了。    

# 总结

VTable 提供了高性能的表格使用体验，帮助开发者简化操作。其开放的可配置 API 选项丰富，基本满足各种业务开发需求。此外，Vtable 在响应问题和产品迭代方面也展现出快速高效的特点，为用户提供了优质的支持和服务。    



感恩 Vtable 开源项目为我们带来了如此好用的工具，它的高性能和灵活配置极大地方便了我们的开发工作。开源的精神让更多的开发者受益，希望能够有更多此类优秀的开源项目继续涌现！开源万岁！    



最后，我们诚挚的欢迎所有对数据可视化感兴趣的朋友参与进来，参与 VisActor 的开源建设：    

**VTable**：[VTable 官网](https://www.visactor.io/vtable)、[VTable Github（欢迎 Star）](https://github.com/VisActor/VTable)    

VisActor 官方网站：[www.visactor.io/](http://www.visactor.io/) 或 www.viactor.com    

Discord：[discord.gg/3wPyxVyH6m](http://discord.gg/3wPyxVyH6m)    

飞书群（外网）：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F40dcf4e6722d4925804361a2269991d8~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D264%26h%3D277%26s%3D35808%26e%3Dpng%26b%3Dfdfdfd)    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/CF8abTiTCoPAvRxMtOVlwEHFgUg.gif' alt='' width='264' height='auto' />

微信公众号：[打开链接扫码](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2Ff28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D258%26h%3D258%26s%3D8552%26e%3Dwebp%26b%3Dfefefe)    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/QfWAbVN3booT09xYSfXloPnogbe.gif' alt='' width='258' height='auto' />

github：[github.com/VisActor](https://github.com/VisActor/)    
