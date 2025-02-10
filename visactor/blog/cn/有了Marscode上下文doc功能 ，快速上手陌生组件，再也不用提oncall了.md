---
title: 有了Marscode 上下文doc功能 ，快速上手陌生组件，再也不用提oncall了

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
# 什么是Marscode

豆包 MarsCode 编程助手是豆包旗下的 AI 编程助手，提供以智能代码补全为代表的 AI 功能。它支持主流的编程语言和 IDE，在开发过程中提供单行代码或整个函数的编写建议。此外，它还支持代码解释、单测生成和问题修复等功能，提高了开发效率和质量。 更多信息，请参考[豆包 MarsCode 编程助手的文档](https://www.marscode.cn/home?utm_source=developer&utm_medium=oss&utm_campaign=visactor_a)。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Q2e1bbkztofydIxbrWJcl8knnkd.gif' alt='' width='1000' height='auto'>

# 什么上下文Doc 功能

指定上下文可以让 AI 助手提供更精准的回答，但是大模型对一个组件的理解的实时程度是低于组件自己的官方文档的。如果我们在使用一个不熟悉的组件的时候，如果能将该组件的相关文档引入到上下文中，可以大大的提升智能化编程水平。



Marscode 使用 “#” 来指定上下文。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Bp7jbfiupo8oPexQfWQcvVS5n2c.gif' alt='' width='527' height='auto'>

输入“#Doc：”，可以看到Marscode 默认支持的文档。继续输入可以进行文档搜索。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/XREJbWAXDogI2tx43oKcgaC9n6e.gif' alt='' width='551' height='auto'>

这里我们可以看到Marscode 已经内置了 VisActor 开源可视化解决方案中的三个组件（[VChart](https://www.visactor.io/vchart)，[VTable](https://www.visactor.io/vtable)，[VStory](https://www.visactor.io/vstory)）的文档。

下面我们实地测试一下引用文档作为上下文是否会给编程带来实际的益处。

# 效果测试

## 创建测试项目

我们创建一个最简单web 项目

```
npm create vite@latest my-ts-web -- --template typescript
cd my-ts-web

```






# 实践验证

## 新增一个线图

我们要求Marscode 生成一段代码，在当前文件中添加一个VChart 线图。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/RIWabmbm5o981xxoCD3cYvyBnjb.gif' alt='' width='301' height='auto'>

生成的结果还是有错误的。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Pm23biobIocfdAxNpeKcYTnEnEC.gif' alt='' width='1000' height='auto'>

我们下面引入文档，看看是不是会有所改变。

首先我们看到使用了多个文档作为上下文信息：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/SF55bBy84oRAzkxfniUcPt7Wnbc.gif' alt='' width='504' height='auto'>

重新生成的代码修复了之前的api 错误调用。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/IPf5bH7XXoAficxl6yfc44n4npd.gif' alt='' width='312' height='auto'>

生成了一个正确的线图。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/LTZyb2kuYo95JfxVQo4cUjdpnAg.gif' alt='' width='1000' height='auto'>



## 加大难度

首先我们来修改数据，显示三条线。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/UJvdb88RSojHlrx75dvczjHXnqh.gif' alt='' width='526' height='auto'>

我们看到这次并没有引用文档，同时生成的结果也不是很好。调整一下说法，看看是不是会好一些。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/KSUjbcYlfoQFrwxlzAhcXLUnnzb.gif' alt='' width='454' height='auto'>

主要修改点：

1. 重构了数据结构，使用type字段来区分不同系列

1. 使用seriesField来指定系列的分类字段

1. 每个数据点包含year（x轴）、value（y轴）和type（系列）三个字段

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VGqCbLZHNovw5cxM2GIcaBHunic.gif' alt='' width='1000' height='auto'>

修改入场动画效果：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/BZhUb5Z7PobnASxJnQwc55oyn0b.gif' alt='' width='517' height='auto'>



主要修改：

1. 添加了animation配置

1. appear设置入场动画为fadeIn（淡入淡出）

1. duration设置动画持续时间为1000毫秒

结果如下：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Y34UbtKNwo9efYxAswkcbnvxnGh.gif' alt='' width='1000' height='auto'>



## 更多问答

因为有了官方文档，我们可以让Marscode 回答更多类型的问题，不只是代码本身。

比如基本的图表概念：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/KBYIbB9Fyoh5NbxlxKrcM0CSncd.gif' alt='' width='483' height='auto'>

或者 跨端支持情况：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VC9JbsIMXo3VRKx8XIOcEImWnac.gif' alt='' width='488' height='auto'>

导航到更相关文档：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/T25UbYqrQooLPOxkr5ocuUyrnTf.gif' alt='' width='521' height='auto'>



# 自定义添加文档

如果你想使用的文档没有被Marscode 默认提供，也无需担心，Marscode提供了添加自定义文档的功能。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/BPnhboTMho5udbxfi9EcA9gEnvf.gif' alt='' width='578' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/GiTKbd3caokSPYxg5a0cO7Rwnhf.gif' alt='' width='565' height='auto'>

添加文档入口链接即可。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/QfkhbDZe7oxTHmxrhBOcauxLnre.gif' alt='' width='548' height='auto'>

接下来就可以引用了。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Tsr0buovdol3vCxE5PucoeNTnkd.gif' alt='' width='518' height='auto'>



# 简单总结

Marscode 结合文档有如下好处：

1. 提升开发效率

- 快速访问：通过Marscode直接查阅VisActor API文档

- 智能提示：基于VisActor文档的精准代码补全

2. 增强开发体验

- 无缝集成：在开发环境中直接获取VTable使用指导

- 实时反馈：快速验证代码效果

3. 降低学习成本

- 文档辅助：随时查阅VisActor官方示例

- 代码生成：自动生成符合VisActor规范的代码

4. 提高代码质量

- 规范检查：确保代码符合VisActor最佳实践

- 性能优化：自动生成高性能表格配置

# 联系我们

github ：[github.com/VisActor](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FVisActor)

VisActor 微信订阅号留言（可以通过订阅号菜单加入微信群）：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/N4TXbfbH1o8Lu2xwSNfcTA5qnnf.gif' alt='' width='258' height='auto'>

VisActor 官网：[www.visactor.io/](https://link.juejin.cn/?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)  ； [www.visactor.](https://link.juejin.cn/?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)com

飞书群：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/TTiRbhzCYocjQVxg6LPc2u6in74.gif' alt='' width='264' height='auto'>

discord：https://discord.com/invite/3wPyxVyH6m
