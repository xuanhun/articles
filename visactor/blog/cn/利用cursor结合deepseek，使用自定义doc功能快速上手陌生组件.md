---
title: 利用cursor 结合 deepseek ，使用 自定义doc 功能快速上手陌生组件

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---
在当今的软件开发领域，开发者们面临着不断提升开发效率与降低上手成本的挑战。本文档的核心目的，便是助力开发者们实现这一目标，特别是通过巧妙运用**组件官网文档**，并结合 **Cursor** 与 **DeepSeek** 等工具，让 AI 自动生成所需代码，快速降低入门门槛。接下来，我们将以比较新的开源项目， VisActor  （https://www.visactor.com ； https://www.visactor.io/）开源可视化解决方案中的几个项目为例进行实验，看看效果如何。



# 前期准备

## 创建测试项目

例如我有一个通过`npx create-react-app my-app --template typescript `初始化的一个简单项目，启动后如下所示：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/C4cabsu8goKMv4xiaBtcDqvmnuf.gif' alt='' width='1000' height='auto'>

## 获取DeepSeek api key

注册deepSeek，在[deepSeek api](https://platform.deepseek.com/api_keys)官网上新建一个属于你的api key。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/JNQPbFzIOoYknHxr3aDcg60onCg.gif' alt='' width='1000' height='auto'>

## 配置 Cursor

官网：https://www.cursor.com/

下载并注册，用cursor打开你的vchart项目，我们对cursor进行配置



以deepSeek-V3为例，其api模型名称为`deepseek-chat`，api地址为https://api.deepseek.com/v1，详见[api使用官网](https://api-docs.deepseek.com/zh-cn/)

我们在model页面进行新建，设置对应的api地址和model名称

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/WBBCb55WwoF3HUxnhGtc6P5Un5c.gif' alt='' width='1000' height='auto'>

当然你也可以使用其他任何AI模型，这里我们使用deepSeek



# 注入官网教程到 @Docs



# 实践验证

## VTable 测试

VTable （https://www.visactor.io/vtable/； https://www.visactor.com/vtable/）是 VisActor 可视化库中一款强大的表格组件。它专为满足多样化的数据展示需求而设计，具备高度的灵活性与可定制性。无论是简单的数据罗列，还是复杂的数据分析展示场景，VTable 都能提供出色的解决方案。

VTable具备以下核心特性：

- 支持多种表格类型：基础表格、透视表、转置表、透视图等

- 强大的交互功能：排序、筛选、行列拖拽、单元格编辑等

- 丰富的单元格类型：文本、图表、进度条、复选框、迷你图等

- 高性能渲染：支持百万级数据流畅展示

- 多端适配：完美支持Vue、React等主流框架

### 在Cursor中唤起 AI 交互面板并生成代码

在项目环境搭建完成后，使用 `cmd + i` 唤起 AI 交互面板。我们直接让 AI 生成一段插入 VTable 的基本表格代码。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/AIWSbqgyhog1DZxaYGCcl9kinsb.gif' alt='' width='1000' height='auto'>

然而，初次生成结果显示，AI 并不识别 VTable 的 `ListTable`，可能是无法准确识别VTable的ListTable配置。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/IfZ4bjODTocB2oxPfDScNdvnnEg.gif' alt='' width='1000' height='auto'>

### 注入官网教程到 @Docs

为了解决上述问题，我们将官网教程注入到 @Docs 中。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/DOapbrW3gowfgDxw6v9cagArnPe.gif' alt='' width='1000' height='auto'>

在 `promt` 中明确指定 @Docs 中的 `VisActor VTable`，经过这一步操作后，我们惊喜地发现，AI 能够正确按照 VTable 中的 `option` 来编写实现逻辑。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/HOHPbR1gho68fAxnA0XcWR3Xnuc.gif' alt='' width='1000' height='auto'>

### 代码应用与效果展示

将生成的代码复制到相应文件中，运行项目，即可看到初步的效果，已经正确生成表格。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Vbf8bECXsoJngRxMxdzcbZgunfb.gif' alt='' width='1000' height='auto'>

之后，我们继续让 AI 修改表格样式。AI 给出了合理的修改建议，

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/CM5lbR4tIokooxxxNotcvtakngd.gif' alt='' width='1000' height='auto'>

再次运行项目后，我们得到了更符合需求的展示效果：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Co0mb5MdhoTuE1xGpwtcqC5Vngu.gif' alt='' width='406' height='auto'>

## VChart 测试

### 新增柱图

通过`cmd+i`命令唤起ai交互，直接让ai帮我们先生成一个简单的柱图代码

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/WnzubAlKKo70ePxjlVbclc6ynmg.gif' alt='' width='970' height='auto'>

直接应用这个spec，我们查看结果，一个简单的柱图就渲染完成了；可以发现deepSeek对vchart有着一定的认知，简单的图表可以直接添加，我们尝试下更复杂的场景。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/HYjbb8u8ToXkbBxJ8o6cGqPgn8f.gif' alt='' width='1000' height='auto'>

### 复杂场景，注入docs

我们希望添加一条y轴的均值辅助线，查看结果，然而结果并不正确，仔细查看可以发现，markLine虽然写的像是这么回事，但是spec并不符合规范，而且均值线经过了计算，我们通过注入docs来解决这个问题。

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/UJdkbdp0poJRSzxy94kcOHqZnsd.gif' alt='' width='980' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/RQgwbW3WGopiNAxoEAXcJhornqc.gif' alt='' width='1000' height='auto'>

####  设置docs

进入cursor设置页面，选择`Features`，新增docs，docs地址为https://visactor.com/vchart  (https://visactor.io/vchart)；也可以直接在编辑页面通过@Docs进行新增

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/JQarbKxBEoKc3FxjN4Fc9jydnXb.gif' alt='' width='1000' height='auto'>

### 实验结果

通过将新加入的docs，再次编辑，可以得到正确的结果！

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/PJXKbmAccoeUmjxXButcR45dnZe.gif' alt='' width='1000' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/SIFlbNJ5eo0bbjxRRMCcoruFnWp.gif' alt='' width='1000' height='auto'>

## VStory 测试

VStory （github：https://github.com/VisActor/VStory/； site:https://www.visactor.io/vstory/ , https://www.visactor.com/vstory/ )是一个面向叙事的可视化研发框架，集成了VisActor所有可视化组件的能力，因此使用起来难度更大，我们做简单的测试。



通过`cmd+i`命令唤起ai交互，直接让ai帮我们先生成一个最简单的仪表盘demo

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/WNZAbOsbVoBd46xTGChcqbm3nbh.gif' alt='' width='766' height='auto'>

可以发现完全不对，因为deepseek使用的数据是2023年的，那时候VStory还没有发布，所以并不知道如何使用，这时我们就需要让它去读文档学习

### 注入docs

我们通过注入docs来解决这个问题。

进入cursor设置页面，选择`Features`，新增docs，docs地址为https://visactor.com/vstory/guide/tutorial_docs/VStory_Website_Guide；也可以直接在编辑页面通过@Docs进行新增。



<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/OtoTbG06lodqIaxBCkscQztNnoh.gif' alt='' width='1000' height='auto'>

### 实验结果

通过将新加入的docs，再次编辑，可以得到正确的结果！

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/HqKdbVHgYoM9a6xmsZqcuZ4QnDq.gif' alt='' width='754' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/X1UZb9HXsoIdiRxqLCucJbqPnZc.gif' alt='' width='1000' height='auto'>



**因为VStory 使用了VChart，VTable 和VRender，如果想达到更好的效果，应该同时添加VChart，VTable 和VRender 的文档到上下文里。**

# 简单总结

1. 提升开发效率

- 快速访问：通过Cursor直接查阅VisActor API文档

- 智能提示：基于VisActor文档的精准代码补全

2. 增强开发体验

- 无缝集成：在开发环境中直接获取VTable使用指导

- 实时反馈：快速验证代码效果

3. 降低学习成本

- 文档辅助：随时查阅VisActor官方示例

- 代码生成：自动生成符合VisActor规范的代码

4. 提高代码质量

- 规范检查：确保代码符合VTable最佳实践

- 性能优化：自动生成高性能表格配置

# 联系我们

github ：[github.com/VisActor](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FVisActor)

VisActor 微信订阅号留言（可以通过订阅号菜单加入微信群）：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VyPsbaIz8offShxlv0ZcqqD8nfd.gif' alt='' width='258' height='auto'>

VisActor 官网：[www.visactor.io/](https://link.juejin.cn/?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)  ； [www.visactor.](https://link.juejin.cn/?target=https%3A%2F%2Fwww.visactor.io%2Fvtable)com

飞书群：

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/NAQhbtdelofTIyxk9pgcE3hqnQG.gif' alt='' width='264' height='auto'>

discord：https://discord.com/invite/3wPyxVyH6m


