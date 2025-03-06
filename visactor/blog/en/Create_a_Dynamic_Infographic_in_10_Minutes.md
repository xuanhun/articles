---
title: [Practical] Create a Dynamic Infographic in 10 Minutes

key words: VisActor, VChart, VTable, VStory, VMind, VGrammar, VRender, Visualization, Chart, Data, Table, Graph, Gis, LLM
---
# Background
Dynamic infographics are a powerful form of visual expression that can transform complex data and information into intuitive, vivid graphical presentations. Through dynamic infographics, we can:

1. Better tell data stories, making dry data lively and interesting
2. Improve information transmission efficiency, helping audiences quickly understand core information
3. Enhance expressiveness and interactivity, providing immersive user experiences
4. Achieve real-time data updates and dynamic display

This article will detail how to create a dynamic infographic using VStory, helping everyone quickly master this powerful tool.

# Preparation

## Understanding VStory

First, visit the VStory official website to understand VStory and its basic concepts: https://www.visactor.com/vstory/guide/tutorial_docs/Getting_Started

## Clone Repository and Local Environment

**First, globally install [**@microsoft/rush**](https://rushjs.io/pages/intro/get_started/)**:

```
npm i --global @microsoft/rush
```

**Then clone the code to your local machine:**

```
git clone git@github.com:VisActor/VStory.git
cd VStory
```

You can also choose [Github Desktop](https://desktop.github.com/download/)

**Install Dependencies**

```
// Note: Execute in the project root directory
rush update
```

**Start VStory Local Development**

```
rush story
```

After success, open http://localhost:6789/ in your browser to see the following screen:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/CwyYbqV89oXNqGxurdjcnqUfnjd.gif' alt='' width='1000' height='auto'>

You can check completed infographics under **infographic**

# Start Creating

## Decide What to Create

You can choose topics based on your interests or current news events. Infographics are charts that tell data stories through various visualizations containing data.

Here, I plan to use charts to introduce the impact of DeepSeek's open-source model release on the AI market and explain why it had such an impact.

## Collect Data

Based on the purpose, I need to collect relevant data: what changes occurred in US stocks after DeepSeek released its open-source model.

Ask AI for relevant information:

* DeepSeek officially released its latest open-source model **DeepSeek R1** on **January 20, 2025**. The model achieved performance comparable to OpenAI's o1 model at an extremely low training cost (only about $6 million) and caused a sensation globally.

* After the open-source model became increasingly popular, one week later on **January 27** (US local time), the US tech sector plummeted due to DeepSeek's impact. NVIDIA's stock price fell nearly 17% in a single day, setting a record for the largest single-day market value drop in US stock history. Microsoft, AMD, and other companies' stock prices also suffered significantly.

Collect accurate data for January 27 from financial websites:

| Company Name | Stock Code | Jan 24 Closing Price | Jan 27 Closing Price |
| ------------ | ---------- | ------------------- | ------------------- |
| NVIDIA | NVDA | 142.62 | 118.42 |
| Broadcom | AVGO | 244.70 | 202.13 |
| AMD | AMD | 122.84 | 115.01 |

## Prepare Graphic Content

### Data Display

I choose to use charts to display this data. Ask AI what chart type would be better for display. Got the following recommendation:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/TyfIbPoswoHOybxiM1KcjSgVnGc.gif' alt='' width='1000' height='auto'>

Now I go to the [VChart official website](https://www.visactor.com/vchart) to draw this chart using VChart. No need to download VChart; you can debug directly using the online editing capability provided on the official website.

First, find the [grouped column chart example](https://www.visactor.com/vchart/demo/bar-chart/group-column)

Then adjust the chart's display effect:

1. Modify the chart's data to what I want to display
2. Narrow the data range to make data changes clearer
3. Remove the value axis as individual stock prices aren't very meaningful compared to decline percentages
4. Add annotations to mark the decline percentage for each stock

Here's the final chart effect. Record this configuration spec.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/ZbYGbbrIDoVIbpxeqPWcq7nEnid.gif' alt='' width='1000' height='auto'>

### Title and Content Introduction

Use AI to generate titles and text that match my ideas:

```
**English Title:**

DeepSeek's Shockwaves: AI Giants Lose $170B in 72 Hours

(Subtitle: When Algorithms Crack the Silicon Fortress)

**Analysis Text (English):**

DeepSeek's breakthrough exposes a seismic shift in computing economics. The synchronized collapse of chip leaders signals that AI's next phase may bypass brute-force hardware scaling. NVIDIA's historic plunge—its worst performance since the crypto-mining crash—mirrors Wall Street's awakening: when algorithms can achieve 10x training efficiency, the $3 trillion semiconductor industry must reinvent its value proposition. This isn't a correction, but a Copernican revolution in AI infrastructure.
```

### Add a Background Image

Create a background image related to the content using AI:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/TSS6bLjk6o0bJ9xcM17cwM03nMe.gif' alt='' width='1000' height='auto'>

### Design Content Layout and Display Effect

Ask AI for a basic layout suggestion:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/MOVrbiMUlooHRlxWQTYcRcMznuc.gif' alt='' width='1000' height='auto'>

Summary of layout effects:

1. A large title at the top
2. Subtitle right above the chart
3. Description on the right side of the chart

Given there's no more content, pay attention to the chart's dimensions and text content size to avoid too much blank space at the bottom.

## Start Creating

### Step 1: Create New Demo

Create your own demo file in this directory of the project:

```
packages/vstory/demo/src/demos/infographic
```

Here's the file I created:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VhxrbhEKooj40OxJduMcVg2Zn0b.gif' alt='' width='888' height='auto'>

Then you can first copy content from another infographic to your file, delete the DSL content, it looks something like this:

```typescript
import React, { useEffect } from 'react';
import { IStoryDSL, Player, Story } from '../../../../../vstory-core/src';
import { registerAll } from '../../../../src';

registerAll();
export const DeepseekShockwave = () => {
  const id = 'DeepseekShockwave';
  useEffect(() => {
    const container = document.getElementById(id);
    const canvas = document.createElement('canvas');
    canvas.width = 1024;
    canvas.height = 1024;
    container?.appendChild(canvas);

    const dsl: IStoryDSL = {
      acts: [],
      characters: []
    };

    const story = new Story(dsl, {
      canvas,
      width: 1024, // Planning to make a 1024 X 700 rectangular infographic
      height: 700,
      scaleX: 'auto',
      scaleY: 'auto'
    });
    const player = new Player(story);
    story.init(player);
    player.play(-1);

    return () => {
      story.release();
    };
  }, []);

  return <div style={{ width: '100%', height: '100%' }} id={id}></div>;
};
```

Add your demo to the app directory. app.tsx is located at:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/MwIwb9XDeopC3SxVumHc7KLjnVh.gif' alt='' width='1000' height='auto'>

Then add your content under Infographic in menus, remember to import your component in app.tsx first:

```typescript
import { DeepseekShockwave } from './demos/infographic/deepseek-shockwave';
```

Here's what I added:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/UvH1bMQ8po3cg5xDSbEcA2S6nLe.gif' alt='' width='916' height='auto'>

Now you can see your demo in the right menu after starting debug.

### Add Elements

Let's first add all elements we need to display, giving them random positions. Elements are added to characters:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/VLMAbJeNhobbZbxQRNdcpH15nF9.gif' alt='' width='766' height='auto'>

**Background**

First add image resources:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/IX0Ybk8OrolrULxTe36c7hksnBG.gif' alt='' width='954' height='auto'>

Note the rule: Image resources must be placed in this directory:

```
packages/vstory/demo/src/assest/infographic/${yourDemoName}
```

Then add an image type element:

```typescript
// Import image element
import bg from '../../assets/infographic/deepseek-shockwave/bg.png';

characters: [
        {
          id: 'background', // Element ID will be used later
          type: 'Image', // Type
          zIndex: 0,  // Layer level
          position: { // Element position and size
            x: 0,
            y: 0,
            width: 1024,
            height: 700
          },
          options: {
            graphic: {
              image: bg, 
              fillOpacity: 0.4 // Opacity
            }
          }
        },
]
```

**Chart**

```typescript
characters: [
    // ... others
    {
      id: 'chart',
      type: 'VChart',
      zIndex: 1,
      position: { // Give it a random position first
        x: 50,
        y: 50,
        width: 400, // Note to give width and height
        height: 400
      },
      options: {
        spec: spec // Chart configuration from content preparation
      }
    }
]
```

**Text**

```typescript
characters: [
    // ... others
    {
      id: 'title',
      type: 'Text',
      zIndex: 1,
      position: { x: 512, y: 0, width: 1024, height: 100 },
      options: {
        graphic: {
          text: `DeepSeek's shockwave: AI's technological revolution`,
          fill: 'black',
          fontSize: 48,
          textAlign: 'center',
          fontWeight: 'bolder'
        }
      }
    },
]
```

After adding all elements.

Configure a display action for them. (We'll configure animations last)

```typescript
const dsl: IStoryDSL = {
  acts: [
    {
      id: 'defaultAct',
      scenes: [
        {
          id: 'defaultScene',
          actions: [
            {
            // Give all elements a display action first, without specific animation properties, they will display directly
              characterId: ['background', 'title', 'subTitle', 'desc1', 'desc2', 'chart'],
              characterActions: [
                {
                  action: 'appear'
                }
              ]
            }
          ]
        }
      ]
    }
    ...character
}
```

You should see a messy infographic:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/SluCbT2cpo5zxcxxxZpcy3KWn5f.gif' alt='' width='1000' height='auto'>

### Layout and Fine-tuning

Now we need to adjust elements to their corresponding positions and adjust properties to make it look more coordinated:

* You can adjust element position and size through the character's position property
* Text's text property can be an array, so you can manually line break like this:

```typescript
 text: [`When Algorithms Crack`, '\n', `the Silicon Fortress`], 
```

* Specific property configurations are here: https://www.visactor.com/vstory/guide/tutorial_docs/VStory_Concepts/character

After simple adjustment of positions, text colors, and sizes, this infographic is almost complete:

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/WQ9EbBFpSo4cnOx0kSrcFVSenpb.gif' alt='' width='1000' height='auto'>

### Artistic Enhancement

This part is subjective, such as adding shapes, watermarks, adding backgrounds to text, etc.

### Add Animations

Just now when adding elements, we added a unified `appear` action in the action. Now let's configure different animation effects for elements individually.

Taking the right text as an example:

```typescript
const dsl: IStoryDSL = {
  acts: [
    {
      id: 'defaultAct',
      scenes: [
        {
          id: 'defaultScene',
          actions: [
            {
              characterId: ['desc1'],
              characterActions: [
                {
                  action: 'appear',
                  startTime: 1000, // Animation start time
                  payload: [
                    {
                      animation: {
                        duration: 800, // Duration
                        easing: 'linear', // Interpolation method
                        effect: 'wipe' // Effect
                      }
                    }
                  ]
                }
              ]
            }
           // ... other actions
           ]
         }
       }
     ]
   }
   // ... characters 
```

Specific animation configuration documentation is here: https://www.visactor.com/vstory/guide/tutorial_docs/VStory_Concepts/action

### Final Effect

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/F9uubwszKoVdaExeBHWcn9F2nsg.gif' alt='' width='1000' height='auto'>

# Upload

1. Pull new branch from develop, PR target is also develop.
2. When submitting PR, select demo update and invite Reviewers: [xuefei1313](https://github.com/xuefei1313) or [neuqzxy](https://github.com/neuqzxy) or [xiaoluoHe](https://github.com/xiaoluoHe)

[Reference PR](https://github.com/VisActor/VStory/pull/243)
[How to submit PR](https://www.visactor.io/vstory/contributing/)

# Welcome to Communicate

* VisActor Github: https://github.com/VisActor (Welcome to star)
* VStory Github: https://github.com/VisActor/VStory (Welcome to star)
* VisActor Feishu Group: https://applink.larkoffice.com/client/chat/chatter/add_by_link?link_token=43bu05e4-4cf6-41f0-82de-f20d83c2535e

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/img/V3BRb5MRTookttxZl3VcdDk8nDj.gif' alt='' width='264' height='auto'>

Discord: [discord.gg/3wPyxVyH6m](https://discord.gg/3wPyxVyH6m)

WeChat Official Account: [Open link to scan code](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f28519302ee94940a8159fc52d375aaa~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=258&h=258&s=8552&e=webp&b=fefefe)

Twitter: [twitter.com/xuanhun1](https://twitter.com/xuanhun1) 