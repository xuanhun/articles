---
title: Powerful and High-Performance: VTable Practice Sharing — The Ultimate Tool for Table Data Visualization    

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
This article is contributed by VTable user LLmoskk (https://github.com/LLmoskk).    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/AG51bUcp1otOWnx23U1lJCJKgZ1.gif' alt='' width='397' height='auto' />

---
# VTable Overview

Quoted from the official website:     

VTable: Not just a high-performance multidimensional analysis table — it’s a grid artist creating between rows and columns!    

In modern applications, table components are indispensable. They can quickly present large volumes of data and provide solid visualization and interactive experiences. VTable is a high-performance table component library based on the visualization rendering engine VRender. It offers excellent performance, powerful multidimensional analysis capabilities, and flexible, robust graphics abilities.    

Official site: https://visactor.com/vtable    

GitHub: https://github.com/VisActor/VTable    

# Create a Project

I’ll share a few simple practical cases. I currently mainly use the basic ListTable in a React project, so most examples are for ListTable. I’ll continue sharing additional content as I encounter valuable tips while using other VTable types.    

Full example code: https://github.com/LLmoskk/vtable-demo     

Online preview: https://llmoskk.github.io/vtable-demo/    

Start a React project with Vite. Note: react-vtable does not yet support React 19 — use React 18.    

# Use AI + MCP to Drive VTable Quickly and Accurately

I currently use AWS’s krio as my AI IDE. First, add the context7 MCP. This MCP lets the AI fetch the latest documentation to avoid outdated APIs. Most mainstream IDEs support adding MCP; no need to list them here.    

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
    }
  }
}
```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/Sm8nbekaQoAv2BxLCoZl4tMKg4f.gif' alt='' width='484' height='auto' />

You can see MCP was successfully invoked to fetch documentation. I asked it to implement a tree table.    

Quickly, the AI finished. The effect is pretty good. I won’t paste the code here since it’s mainly to demonstrate using AI + MCP to implement VTable features fast, without manually paging through docs.    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/UHhzbGnWYoZ10hxKcvrlg1HxgWe.gif' alt='' width='1000' height='auto' />

# Auto-Calculated Column Widths with Persistent Drag-Resize Memory

Project context:    

With large datasets, fixing all column widths to 120px can waste space for columns with short content. The solution: dynamically adjust column widths by measuring values to set a final width per column.    

- Column auto-fit: Analyze the first 20 rows to dynamically compute each column’s optimal width for a more reasonable layout.    
- User drag-resize: Allow users to drag column borders to adjust widths. Store the result in localStorage to improve UX.    
- Load priority: On table load, set widths by this order:    
  - Local storage value    
  - Auto-calculated value    
  - Default width    

We’ll create a file named `use-column-width.ts` to encapsulate a custom Hook. It computes widths per the above logic and remembers user drag-resized widths. We use ahooks’ `useLocalStorageState` to keep state synchronized with localStorage.    

~~Measuring text width — reference: https://juejin.cn/post/7091990279565082655. Full code is in the repo: https://github.com/LLmoskk/vtable-demo/blob/main/src/pages/demo1/utils/calculate-column-width.ts~~    

(No need to write it yourself — VTable provides measureText 😅)    

If you don’t have customization needs, you can directly use VTable’s autoWidth mode — it already performs one pass of width calculation.    

Text measure in VTable: https://github.com/VisActor/VUtil/blob/main/packages/vutils/src/graphics/text/measure/textMeasure.ts    

https://www.npmjs.com/package/@visactor/vutils    

```ts
import { useLocalStorageState } from 'ahooks';
import { useCallback, useMemo } from 'react';
import { type ColumnDefine } from '@visactor/vtable';
import { calculateColumnsWidthMap } from '../utils/calculate-column-width';
import type { Sort } from '../type';

type UseColumnWidthParams<T extends ColumnDefine> = {
  /** Column config list */
  columns?: T[];
  /** Key for localStorage */
  storageKey: string;
  /** Table data used to calculate column widths */
  data?: any[];
  /** Default column width */
  defaultWidth?: number;
  /** View ID */
  viewId?: string | number;
  /** Sort info applied */
  sorts?: Sort[];
};

type UseColumnWidthReturn<T extends ColumnDefine> = {
  /** Column configs with width applied (priority: localStorage > calculated > default) */
  columnsWithWidth: T[];
  /** Function to save column widths */
  saveColumnWidths: (colWidths: number[]) => void;
  /** Column width map */
  columnWidths: Record<string, number>;
};

/**
 * Manage persistent column widths for a table
 * Automatically cleans widths for fields no longer present when columns change
 * Priority: localStorage > calculated > default
 */
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

  // Save column widths to localStorage
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

  // Apply widths to column config
  // Priority: localStorage > calculated > default
  const columnsWithWidth = useMemo(() => {
    return columns?.map((col) => {
      // Fixed width 40 for checkbox; cellType may be a function
      if (col.cellType === 'checkbox' || col.headerType === 'checkbox') {
        return {
          ...col,
          width: 40,
        };
      }

      // Prefer localStorage width
      if (columnWidths?.[String(col.field)]) {
        return {
          ...col,
          width: columnWidths[String(col.field)],
        };
      }

      // Otherwise use calculated width
      const calculatedWidth = calculatedWidthMap.get(String(col.field));
      if (calculatedWidth) {
        return {
          ...col,
          width: calculatedWidth,
        };
      }

      // Fallback to default width
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

# Theme Switching

VTable provides themes for Arco Design and Dark Mode. My project migrated from Arco Design’s Table to VTable, so I used the official theme package, and since we also needed dark mode, I customized the Dark Mode settings. See docs for base theme config: https://visactor.com/vtable/option/ListTable#theme    

There are five built-in themes:    

```ts
const builtinThemes = [
  { key: 'DEFAULT', name: 'Default', theme: themes.DEFAULT },
  { key: 'DARK', name: 'Dark', theme: themes.DARK },
  { key: 'BRIGHT', name: 'Bright', theme: themes.BRIGHT },
  { key: 'ARCO', name: 'Arco', theme: themes.ARCO },
  { key: 'SIMPLIFY', name: 'Simplify', theme: themes.SIMPLIFY }
];
```
You can extend themes for customization. After switching to the ARCO theme, I still found differences from the component library’s Table. So I implemented some style overrides.    

```ts
import { themes } from '@visactor/vtable';

/** Get a common VTable theme config */
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
      // Use a function to set background color dynamically. Use headerStyle for aggregation rows.
      bgColor: (args: any) => {
        // Check whether this is an aggregation row
        if (args.table && typeof args.table.isAggregation === 'function') {
          const isAggregationCell = args.table.isAggregation(args.col, args.row);
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
      visible: 'always', // Scrollbar always visible
      hoverOn: false, // Scrollbar does not overlay content; shows independently
    },
    selectionStyle: {
      cellBgColor: 'rgba(133,165,242,0.2)',
    },
  });
};
```
For conditional cell styling, traverse column config and set styles per condition.    

```ts
const salesColumn = baseColumns.find(col => col.field === 'sales');
if (salesColumn) {
  (salesColumn as any).style = {
    color: (args: any) => {
      const value = args.dataValue;
      if (value >= 150000) return '#059669'; // Green: high sales
      if (value >= 100000) return '#0891b2'; // Blue: medium sales
      return '#dc2626'; // Red: low sales
    },
    fontWeight: (args: any) => {
      return args.dataValue >= 150000 ? 'bold' : 'normal';
    }
  };
}
```
<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/XUiibbCGDos9uqxcwQHlq8Mgg3e.gif' alt='' width='1000' height='auto' />

# Transpose and Frozen Columns/Rows

Frozen columns/rows config: https://visactor.com/vtable/guide/basic_function/frozen_column_row    

Admin tables often need to fix a few columns on the left. VTable supports freezing at top, bottom, left, and right.    

Issue I encountered: when switching to dark mode, the freeze button icon color didn’t follow the theme. Docs show you can register custom icons. I grabbed the original SVG from source and injected a theme-following `frozen_color`.    

Register icon: https://visactor.com/vtable/guide/custom_define/custom_icon    

```ts
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

To transpose the table, set `transpose` — it converts rows to columns. Transposed tables are ideal when there are many data columns but few rows. I haven’t used it in my current project yet.    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/Br1YbvnjWolKNvxRDm3lcUXogqg.gif' alt='' width='1000' height='auto' />

# Table Size Configuration

Row height & column width docs: https://visactor.com/vtable/guide/basic_function/row_height_column_width    

widthMode supports three modes: `'standard' | 'adaptive' | 'autoWidth'`.    

If you don’t have special business requirements for column widths, `autoWidth` is best. It automatically computes widths based on header and body content, ignoring `width` and `defaultColWidth`. The calculation does cost some performance — tradeoffs apply.    

Also, it’s best to set table `maxWidth + minWidth`. Since we allow users to drag-resize freely, we don’t want unlimited width — add boundaries.    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/WfgcbGH5tol1FxxK7zplP8lBgSb.gif' alt='' width='1000' height='auto' />

# Sticky Header-like Behavior

Traditional DOM tables can use sticky positioning for headers. With VTable, I initially tried making the entire VTable DOM sticky, but the effect wasn’t great. The final approach: keep VTable’s height within one viewport so the header behaves similarly to sticky when scrolling.    

```ts
const [tableHeight, setTableHeight] = useState(500); // Default expanded height

useEffect(() => {
  const calculateHeight = () => {
    // Table height: viewport height - top nav (60) - bottom spacing (16) - Tabs (36) - pagination (32) - extra (80)
    // Goal: occupy one full screen so when scrolled to bottom, the table fills the viewport
    const height = window.innerHeight - 60 - 16 - 36 - 32 - 80;
    setTableHeight(Math.max(height, 400)); // Minimum height
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

# Copy Table Content

Enable keyboard copy with `keyboardOptions.copySelected` to support Ctrl + C copying.    

```ts
keyboardOptions={{
  copySelected: true,
}}
```
However, in my project, some cells use `customRender` to show JSON as icon + text. Copying raw content would be incorrect. Use `formatCopyValue`: https://visactor.com/vtable/option/ListTable#formatCopyValue((value:%20string)%20=%3E%20string)    

```json
{
  "icon": "xxxx",
  "text": "Demo"
}
```
```ts
export const formatCopyValueForVTable = (value: unknown): string => {
  const toStr = (v: unknown) => (v == null ? '' : String(v));
  if (typeof value !== 'string') return toStr(value);

  const parseCell = (cell: string) => {
    const trimmed = cell.trim();
    if (trimmed.startsWith('{') && trimmed.endsWith('}')) {
      try {
        const obj = JSON.parse(trimmed);
        if (obj && typeof obj === 'object') {
          // Prefer the describe field (asset object)
          if ('describe' in obj) {
            return toStr((obj as any).describe ?? '');
          }
          // Then check text field (icon-text object)
          if ('text' in obj) {
            return toStr((obj as any).text ?? '');
          }
        }
      } catch (_) {
        // Not valid JSON — keep original
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
Now copied content is correct.    

# Conclusion

VTable delivers high-performance table experiences that simplify development. Its open and rich configurable APIs meet most business needs. VTable also responds quickly to issues and iterates efficiently, providing quality support and service.    

We’re grateful to the VTable open-source project for such a useful tool. Its performance and flexible configuration greatly facilitate our development work. The spirit of open source benefits more developers — may more excellent open-source projects continue to emerge!    

Finally, we warmly welcome everyone interested in data visualization to join VisActor’s open-source efforts:    

**VTable**: [VTable Official Site](https://www.visactor.io/vtable), [VTable GitHub (please star)](https://github.com/VisActor/VTable)    

VisActor official website: [www.visactor.io/](http://www.visactor.io/) or www.viactor.com    

Discord: [discord.gg/3wPyxVyH6m](http://discord.gg/3wPyxVyH6m)    

Feishu group (external): [Open link to scan](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F40dcf4e6722d4925804361a2269991d8~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D264%26h%3D277%26s%3D35808%26e%3Dpng%26b%3Dfdfdfd)    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/CF8abTiTCoPAvRxMtOVlwEHFgUg.gif' alt='' width='264' height='auto' />

WeChat official account: [Open link to scan](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2Ff28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark%3A0%3A0%3A0%3A0%3Aq75.image%23%3Fw%3D258%26h%3D258%26s%3D8552%26e%3Dwebp%26b%3Dfefefe)    

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/QfWAbVN3booT09xYSfXloPnogbe.gif' alt='' width='258' height='auto' />

GitHub: [github.com/VisActor](https://github.com/VisActor/)
