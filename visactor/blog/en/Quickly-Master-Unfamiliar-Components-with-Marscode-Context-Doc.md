---
title: Quickly Master Unfamiliar Components with Marscode Context Doc Feature

keywords: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---

# What is Marscode

Doudou Marscode Programming Assistant is an AI programming assistant under the Doudou brand, providing AI features represented by intelligent code completion. It supports mainstream programming languages and IDEs, offering suggestions for single-line code or entire functions during development. Additionally, it supports code interpretation, unit test generation, and problem-fixing capabilities, improving development efficiency and quality. For more information, please refer to [Doudou Marscode Programming Assistant Documentation](https://www.marscode.cn/home?utm_source=developer&utm_medium=oss&utm_campaign=visactor_a).

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Q2e1bbkztofydIxbrWJcl8knnkd.gif' alt='' width='1000' height='auto'>

# What is Context Doc Feature

Specifying context allows AI assistants to provide more accurate answers, but large models' real-time understanding of a component is lower than the component's official documentation. When using an unfamiliar component, incorporating relevant documentation into the context can greatly enhance intelligent programming capabilities.

Marscode uses "#" to specify context.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Bp7jbfiupo8oPexQfWQcvVS5n2c.gif' alt='' width='527' height='auto'>

Enter "#Doc:", and you can see the documentation that Marscode supports by default. Continue typing to search for documentation.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/XREJbWAXDogI2tx43oKcgaC9n6e.gif' alt='' width='551' height='auto'>

Here we can see that Marscode has already integrated documentation for three components ([VChart](https://www.visactor.io/vchart), [VTable](https://www.visactor.io/vtable), [VStory](https://www.visactor.io/vstory)) from the VisActor open-source visualization solution.

Let's test whether referencing documentation as context brings actual benefits to programming.

# Effect Testing

## Create Test Project

Let's create a simple web project

```bash
npm create vite@latest my-ts-web -- --template typescript
cd my-ts-web
```

# Practical Verification

## Add a Line Chart

We'll ask Marscode to generate code to add a VChart line chart to the current file.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/RIWabmbm5o981xxoCD3cYvyBnjb.gif' alt='' width='301' height='auto'>

The generated result still has errors.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Pm23biobIocfdAxNpeKcYTnEnEC.gif' alt='' width='1000' height='auto'>

Let's introduce documentation and see if it makes a difference.

First, we see multiple documents used as context information:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/SF55bBy84oRAzkxfniUcPt7Wnbc.gif' alt='' width='504' height='auto'>

The regenerated code fixed the previous API call errors.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/IPf5bH7XXoAficxl6yfc44n4npd.gif' alt='' width='312' height='auto'>

Generated a correct line chart.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/LTZyb2kuYo95JfxVQo4cUjdpnAg.gif' alt='' width='1000' height='auto'>

## Increasing Difficulty

First, let's modify the data to display three lines.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/UJvdb88RSojHlrx75dvczjHXnqh.gif' alt='' width='526' height='auto'>

We see that this time documentation wasn't referenced, and the result wasn't very good. Let's adjust the approach and see if it improves.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/KSUjbcYlfoQFrwxlzAhcXLUnnzb.gif' alt='' width='454' height='auto'>

Main modifications:

1. Restructured data using type field to distinguish different series
2. Used seriesField to specify series classification field
3. Each data point contains year (x-axis), value (y-axis), and type (series) fields

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VGqCbLZHNovw5cxM2GIcaBHunic.gif' alt='' width='1000' height='auto'>

Modify entrance animation effect:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/BZhUb5Z7PobnASxJnQwc55oyn0b.gif' alt='' width='517' height='auto'>

Main modifications:

1. Added animation configuration
2. Set appear animation to fadeIn
3. Set animation duration to 1000 milliseconds

Result:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Y34UbtKNwo9efYxAswkcbnvxnGh.gif' alt='' width='1000' height='auto'>

## More Q&A

With official documentation, we can have Marscode answer more types of questions, not just about code itself.

For example, basic chart concepts:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/KBYIbB9Fyoh5NbxlxKrcM0CSncd.gif' alt='' width='483' height='auto'>

Or cross-platform support:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VC9JbsIMXo3VRKx8XIOcEImWnac.gif' alt='' width='488' height='auto'>

Navigate to more relevant documentation:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/T25UbYqrQooLPOxkr5ocuUyrnTf.gif' alt='' width='521' height='auto'>

# Custom Documentation Addition

If the documentation you want to use isn't provided by Marscode by default, don't worry. Marscode provides the ability to add custom documentation.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/BPnhboTMho5udbxfi9EcA9gEnvf.gif' alt='' width='578' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/GiTKbd3caokSPYxg5a0cO7Rwnhf.gif' alt='' width='565' height='auto'>

Just add the documentation entry link.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/QfkhbDZe7oxTHmxrhBOcauxLnre.gif' alt='' width='548' height='auto'>

Then you can reference it.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/Tsr0buovdol3vCxE5PucoeNTnkd.gif' alt='' width='518' height='auto'>

# Brief Summary

Marscode combined with documentation has the following benefits:

1. Improved Development Efficiency
- Quick access: Directly consult VisActor API documentation through Marscode
- Intelligent prompts: Precise code completion based on VisActor documentation

2. Enhanced Development Experience
- Seamless integration: Get VTable usage guidance directly in the development environment
- Real-time feedback: Quick validation of code effects

3. Reduced Learning Costs
- Documentation assistance: Access VisActor official examples anytime
- Code generation: Automatically generate code compliant with VisActor standards

4. Improved Code Quality
- Standard checking: Ensure code complies with VisActor best practices
- Performance optimization: Automatically generate high-performance table configurations

# Contact Us

GitHub: [github.com/VisActor](https://github.com/VisActor)

VisActor WeChat Official Account (join WeChat group through official account menu):

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/N4TXbfbH1o8Lu2xwSNfcTA5qnnf.gif' alt='' width='258' height='auto'>

VisActor Official Website: [www.visactor.io/](https://www.visactor.io/vtable); [www.visactor.com](https://www.visactor.com)

Feishu Group:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/TTiRbhzCYocjQVxg6LPc2u6in74.gif' alt='' width='264' height='auto'>

Discord: https://discord.com/invite/3wPyxVyH6m 