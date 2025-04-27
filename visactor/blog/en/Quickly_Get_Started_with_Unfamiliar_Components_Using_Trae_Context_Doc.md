---
title: With Trae Context Doc Feature, Quickly Get Started with Unfamiliar Components—No More Oncall Needed

key words: VisActor, VChart, VTable, VStory, VMind, VGrammar, VRender, Visualization, Chart, Data, Table, Graph, Gis, LLM
---
# What is Trae

Trae (China: https://www.trae.com.cn/, International: https://www.trae.ai/) is committed to becoming The Real AI Engineer. Trae's AI IDE product focuses on intelligent productivity, seamlessly integrating into your development workflow, working in perfect harmony with you to complete every task with higher quality and efficiency.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/QO6tbhEcWoH2wUxkv9ocbHTDnih.gif' alt='' width='1000' height='auto'>

# What is the Context Doc Feature

Specifying context allows the AI assistant to provide more accurate answers. However, a large model's real-time understanding of a component is often less up-to-date than the component's official documentation. When using an unfamiliar component, if we can introduce the relevant documentation into the context, it can greatly improve the level of intelligent programming.

Trae uses "#" to specify context.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/DQbKbLavToRSlAxmjDwcZekwngd.gif' alt='' width='527' height='auto'>

Enter "#Doc:" to index the document set. Here, we add VisActor's documentation for testing. Currently, Trae does not have any built-in document sets, so you need to add them manually.

First, add a document set:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/DmbAbZxPboS7ZrxfgN9cyhFinpe.gif' alt='' width='563' height='auto'>

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/GELxbtnzHoPwM8xKvGGcCd2hnDq.gif' alt='' width='388' height='auto'>

Here, we add the entry URLs for each component via URL:

*  VChart: https://www.visactor.io/vchart/
*  VTable: https://www.visactor.io/vtable
*  VMind: https://www.visactor.io/vmind
*  ...

The whole process is a bit slow, but the indexing effect is clearly better than Cursor.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/XyVQbiHvio2MVWxdRhvctj4sndb.gif' alt='' width='878' height='auto'>

Let's test whether referencing documentation as context brings practical benefits to programming.

# Effect Test

## Create a Test Project

We create the simplest web project: 