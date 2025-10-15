---
title: VTable Gantt Intelligent Zoom Feature Introduction and Development Practice

key words: VisActor,VChart,VTable,VStrory,VMind,VGrammar,VRender,Visualization,Chart,Data,Table,Graph,Gis,LLM
---

## Abstract

This article provides a detailed introduction to the complete development practice of the VTable Gantt chart intelligent zoom system. The project implements a multi-level intelligent zoom system based on millisecondsPerPixel and deep integration of the VRender DataZoom component, successfully solving the problem of Gantt charts lacking flexible zoom interaction. Core technologies include: ZoomScaleManager intelligent zoom manager, DataZoom bidirectional synchronization mechanism, dynamic boundary calculation algorithm, etc. It supports multiple interaction methods such as Ctrl+scroll wheel and trackpad pinch-to-zoom, achieving seamless zoom experience from macro to micro views. The author, Lai Jingkang, as a junior student majoring in Computer Science at Central China Normal University, completed the full development of this feature in the Summer of Open Source project, and shares his experience in balancing studies, internship and open source, as well as the important development principle "don't try to solve the problem before finding its cause."

Author of this article, ***shufufufu*** (<https://github.com/shufufufu>):

A student majoring in Computer Science and Technology at the School of Computer Science, Central China Normal University, who has just entered the third year of undergraduate studies. I am passionate about frontend technology and graphics, eager for technical progress, often follow the open source community, and willing to hone my engineering skills in practice.

# Project Description and Reflection

Our project is committed to developing an intelligent zoom system for the VTable Gantt chart component in the VisActor ecosystem and integrating the VRender scalable scrollbar functionality. Through this development, we hope to provide a more intuitive and efficient timeline interaction experience for scenarios such as project management and task scheduling.

The unique aspect of VTable Gantt is that it not only needs to handle complex time data visualization but also automatically select the most appropriate time scale combination at different zoom levels. The system includes two key components:

* **ZoomScaleManager**: As the core zoom manager, it is responsible for intelligent switching of multi-level time scales and precise control of `millisecondsPerPixel`.

* **DataZoomIntegration**: As the visualization scrollbar integrator, it implements bidirectional synchronization with Gantt charts based on the VRender graphics engine.

Given the complexity of frontend graphics rendering and high requirements for user interaction experience, the core challenge of this project is to achieve smooth zoom transitions and precise bidirectional data binding while ensuring performance. We ultimately successfully implemented multi-level intelligent zoom, visual range selection, and complete responsive layout adaptation.

# Implementation Approach

This development is mainly divided into two core modules: intelligent zoom system and DataZoom integration.

### Intelligent Zoom System Design

The goal of the intelligent zoom system is to allow users to automatically obtain the most appropriate time scale display when zooming Gantt charts. The core of the entire system is the key concept of `millisecondsPerPixel`.

**Core Concept: Design Principle of millisecondsPerPixel**

`millisecondsPerPixel` is the foundation of the entire zoom system, representing how many milliseconds of time span each pixel represents. The design idea of this concept is as follows:

1. **Mapping Relationship Between Time and Space**: In Gantt charts, the horizontal axis represents time, and we need to map the continuous time axis to limited pixel space. `millisecondsPerPixel` is the proportional factor of this mapping.

2. **The Essence of Zooming**: When users perform zoom operations, they are actually changing this proportional factor:

   * Zoom In: Decrease the `millisecondsPerPixel` value, making each pixel represent less time, thus displaying more details

   * Zoom Out: Increase the `millisecondsPerPixel` value, making each pixel represent more time, thus displaying a larger time range

3. Since the pixel width of the viewport remains fixed, by adjusting the value of `millisecondsPerPixel`, we can precisely control the display granularity of the timeline, achieving a seamless zoom experience from macro to micro.

4. **Intelligent Level Switching Trigger Mechanism**: When the `millisecondsPerPixel` value changes, the system automatically selects the most appropriate user-defined time scale combination (such as year-month-day, month-day-hour, etc.) based on preset boundary values, ensuring users can always see meaningful time markers.

Through this design, we achieved a complete mapping from underlying mathematical models to user interaction experience, making complex time zooming intuitive and smooth.

**Multi-level Boundary Calculation Algorithm**

To achieve smooth level switching, I designed a boundary calculation algorithm based on ideal boundaries and dynamic adjustment. The system first calculates the ideal `millisecondsPerPixel` boundary value based on the minimum time unit and minimum column width of each time level. Then, through a dynamic adjustment mechanism, it ensures that boundary values maintain a decreasing order: when boundary value conflicts are detected, the arithmetic mean `(prevBoundary + nextBoundary) / 2` is used to recalculate the boundary point. Starting from small time levels, cell width is guaranteed to ensure complete text display within cells. This method effectively avoids frequent jittering near boundaries. Finally, all boundary values are subject to global range restrictions, ensuring they are all between `globalMinMillisecondsPerPixel` and `globalMaxMillisecondsPerPixel`. When users zoom, the system compares the current `millisecondsPerPixel` value with these pre-calculated boundaries and automatically selects the most appropriate time scale combination.

**Zoom Range Control Mechanism**

To prevent users from zooming to unreasonable ranges, I implemented a dynamic zoom limiting system. Through `minMillisecondsPerPixel` and `maxMillisecondsPerPixel` parameters, the system can intelligently limit zoom boundaries, ensuring users can always see meaningful time information.

**Interaction Design and User Experience**

In terms of interaction design, I implemented multiple intuitive zoom operation methods, allowing users to naturally control the display precision of the timeline:

* **Ctrl + Mouse Wheel Zoom**: This is the most commonly used zoom method. Users can achieve precise zoom control by holding the Ctrl key while scrolling the mouse wheel. Scroll up to zoom in (decrease `millisecondsPerPixel`), scroll down to zoom out (increase `millisecondsPerPixel`).

* **Trackpad Pinch-to-Zoom**: For laptop users, I adapted the trackpad's pinch-to-zoom gesture. Users can control zoom through pinching and spreading gestures with two fingers. This operation method is more intuitive, especially suitable for mobile devices and modern laptop usage habits.

* **Zoom Center Point Maintenance**: Regardless of which zoom method is used, the system intelligently maintains the time point that the user is currently focusing on in the center of the view, avoiding the problem of needing to relocate target content after zooming. This design makes zoom operations more in line with users' intuitive expectations.

* **Programmatic API Interface**: In addition to user interaction, I also provide a complete programmatic control interface, allowing developers to precisely control zoom behavior through code:

  * `zoomByPercentage(percentage)` - Zoom by percentage, supporting positive and negative values corresponding to zoom in and zoom out
  * `setZoomPosition(millisecondsPerPixel)` - Directly set a specific `millisecondsPerPixel` value for precise zoom positioning
  * `getCurrentZoomLevel()` - Get current zoom level information for state management and UI synchronization
  * `setMillisecondsPerPixel(value)` - Low-level zoom control interface providing the most direct zoom control capability

### DataZoom Bidirectional Synchronization Implementation

DataZoom integration is the most challenging part of the project, requiring seamless integration of VRender's DataZoom component into the existing Gantt rendering system.

**VRender Component Integration Strategy**

To seamlessly integrate VRender's DataZoom component into the existing Gantt rendering system, I adopted an independent rendering environment strategy. First, create dedicated Canvas elements and corresponding Stage rendering stages, configure appropriate pixel density and layout parameters. Then instantiate the DataZoom component and set its position, size and other attributes. Finally, add the component to the default layer of the Stage. This method ensures normal rendering of DataZoom while avoiding conflicts with the Gantt main rendering process.

**Bidirectional Synchronization Mechanism Design**

The most complex part is implementing bidirectional data binding between the Gantt chart and the DataZoom scrollbar. I designed a complete debounce mechanism to avoid infinite loop updates. When the Gantt chart zooms or scrolls, the system calculates the current view boundary ratio and synchronously updates the DataZoom selection range. For reverse synchronization, when users operate the DataZoom scrollbar, the system calculates the corresponding time range based on the selected start and end ratios, then derives the target `millisecondsPerPixel` value and applies it to the Gantt chart. To prevent infinite loops caused by bidirectional synchronization, I introduced state flags and delay mechanisms to ensure that when one synchronization operation is in progress, the other direction's synchronization is paused.

**Responsive Layout Adaptation**

To ensure that the DataZoom component can correctly adapt to various container sizes, I implemented a complete responsive update mechanism. When container size changes, the system automatically adjusts the position and size of DataZoom without affecting its selection state.

<img src='https://cdn.jsdelivr.net/gh/xuanhun/articles/visactor/sourcecode/img/BQ0FbEi54ohfxexyHhbcBSkTnzg.gif' alt='' width='1000' height='auto' />

# Project Results

I was responsible for the complete development of the project. Through in-depth technical research and innovative design, I successfully achieved a major breakthrough in VTable Gantt chart zoom functionality. My work results are mainly reflected in the following aspects:

**Core Functionality Breakthroughs**

* **Intelligent Multi-level Zoom System**: First implementation of intelligent level switching based on `millisecondsPerPixel` in VTable Gantt, supporting multiple custom time scale levels, completely solving the problem of the original chart lacking zoom functionality

* **Deep VRender DataZoom Integration**: Successfully seamlessly integrated VRender ecosystem's DataZoom component into Gantt charts, achieving a 1+1>2 component reuse effect

* **Complex Bidirectional Synchronization Mechanism**: Conquered the technical challenge of bidirectional data binding between Gantt charts and DataZoom scrollbars, achieving millisecond-level real-time synchronization, including complete debounce, anti-loop and boundary handling mechanisms

**Interaction Experience Innovation**

* **Multi-mode Zoom Interaction**: Simultaneously supports Ctrl+scroll wheel, trackpad pinch-to-zoom and other interaction methods, and provides complete programmatic API interfaces, greatly improving user operation flexibility and developer integration convenience

* **Responsive Layout Adaptation**: Implemented fully responsive design for DataZoom component, automatically adapting to container size changes, header dragging and other complex scenarios, ensuring stable performance in various usage environments

**Technical Architecture Innovation**

* **Independent Rendering Environment Design**: Innovatively adopted independent Canvas and Stage architecture solution, ensuring normal rendering of DataZoom while avoiding conflicts with main rendering process, providing new technical ideas for complex component integration

* **Mathematical Model Optimization**: Designed a boundary calculation algorithm based on dynamic arithmetic mean, effectively solving the jitter problem during level switching, improving the smoothness and stability of zoom operations

**Documentation and Ecosystem Building**

I wrote a complete Chinese and English technical documentation system, including:

* **Demo Examples**: Provide runnable code examples and effect demonstrations, lowering the threshold for developers

* **Guide Tutorials**: Detailed feature introductions and best practice guidance to help users get started quickly

* **Options Documentation**: Complete API documentation and TypeScript type definitions, ensuring development experience

All achievements have been successfully merged into the VTable main branch, becoming official feature characteristics, making important contributions to the functional improvement of VTable Gantt charts.

# Online Demo & Tutorials

https://visactor.com/vtable/demo/gantt/gantt-zoom

https://visactor.com/vtable/demo/gantt/gantt-datazoom

https://visactor.com/vtable/guide/gantt/gantt_zoom_scale

https://visactor.com/vtable/guide/gantt/gantt_datazoom

# Personal Interview

#### --Self-Introduction--

**VisActor Assistant**: Please introduce yourself

**Lai Jingkang**: I am Lai Jingkang, a student majoring in Computer Science and Technology at the School of Computer Science, Central China Normal University, who has just entered the third year. I have been interning at an internet company in Shenzhen since June this year. I am passionate about frontend technology and graphics, eager for technical progress, often follow the open source community, and willing to hone my engineering skills in practice.

**VisActor Assistant**: So you were still a sophomore when you participated in the Summer of Open Source project, and you were also interning during the summer. How did you balance your time between studies, internship and open source?

**Lai Jingkang**: First, I planned my goals very early and clarified what I needed to participate in at each time period. So when I just entered my sophomore year, I intentionally took courses for the second semester of sophomore year and junior year in advance, making time for internships and participating in projects, while still maintaining good grades. During the internship, I actively communicated with my mentor, balanced time between open source and internship development, improved my work efficiency, and ensured high-quality development on both sides. Although this inevitably sacrificed some of my entertainment time, the real progress in technology also brought me joy.

#### --Participating in Summer of Open Source--

**VisActor Assistant**: How did you learn about and participate in Summer of Open Source?

**Lai Jingkang**: Actually, when I was a freshman, a classmate participated in Summer of Open Source. I learned about this activity through his introduction, but I was still in the process of learning various technologies at that time. In my sophomore year, I had mastered the basic technologies and wanted to participate in the open source community to contribute to open source. When the project opened this year, I learned about many directions I was interested in.

**VisActor Assistant**: Among the many Summer of Open Source projects, why did you choose VisActor? What preparations did you make for writing the application?

**Lai Jingkang**: Because I decided to work in the frontend industry, visualization-related technologies are essential. In previous projects, I mostly used some open source graphics components, but I had not really participated in developing a graphics function myself. This made me want to participate in open source projects for visualization component libraries. After the project was announced, I saw VisActor, a familiar name. This series of visualization component libraries open-sourced by ByteDance impressed me very much when I used their components in previous development. So I decided to try to participate in this project. During the application stage, I actively contacted my mentors, proactively joined the early development group, and under the leadership of Teacher Liu Fangfang and Teacher Sun Rui, quickly understood the project, rapidly set up the development and debugging environment, and proactively claimed multiple issues in the community to try to participate in community development and maintenance. After real project development and maintenance, I gained some understanding of VTable, carefully polished my project application, and ensured there were no flaws in development.

**VisActor Assistant**: What experience can you share with everyone about project output, community and mentor communication?

**Lai Jingkang**: In this open source project, my long-standing development principle was once again strongly validated, which is also my valuable experience

"Don't try to solve the problem before finding its cause."

In this open source development, I intentionally tried to use AI agent to help me develop together. In the initial stage, the development process was always unsatisfactory, either unable to solve the problem, or not solving it thoroughly, or even solving this problem and causing another. The root cause is that whether it's the AI agent or me who initially trusted the AI agent very much, we both overlooked a problem, which is what problem we are trying to solve. We only know that this bug didn't meet expectations, but how did the bug appear, why did it appear, what part of the code affects this effect, whether there is already a solution to this problem in the project - I call all these problems "before finding the problem cause," and this is the problem I should solve first before trying to solve each problem. When I actively adjusted my development strategy and tried to explore the root cause of the problem and the deep starting point of subsequent development each time, problems became relatively easy, and development went smoothly.

Regarding communication with mentors and the community, since I intern at the company during the day, I actually communicate with my mentors more after work. I am still very grateful to Teacher Liu Fangfang and Teacher Sun Rui for their tolerance and understanding. I think like my situation, special circumstances need to be communicated in advance, and countermeasures and solutions should be discussed together. I could only communicate with my mentors after work, but this did not affect the development progress. While maintaining efficient communication, we had an online acceptance meeting and discussion every two weeks to handle previous tasks and upcoming tasks in a timely manner. I think the key is to actively communicate and leave messages when there are problems, and handle online messages and feedback in a timely manner. This is the premise of ensuring efficient development.

**VisActor Assistant**: You just mentioned "don't try to solve the problem before finding its cause." Can you use a difficulty you encountered in this open source project to expand on this?

**Lai Jingkang**: At the beginning of the project, I wrote a simple demo that controlled colWidth (cell width) through scroll wheel and pinch-to-zoom, achieving a simple zoom effect by changing the size of colWidth. Later, I needed to develop intelligent header effects, that is, dynamically displaying and hiding relevant data during interaction, and being able to change the time unit of the header at appropriate size changes. At first, I found that the time unit of the header was controlled through minUnit (minimum time unit) and step (how many minimum time units one cell has), so I simply thought that if I detected colWidth reaching a certain threshold, I would change the corresponding minUnit and step values. But no matter how I tried, the effect was not what I had envisioned. I always encountered situations where the entire table instantly doubled in size or halved, and the size of the minimum cell never changed when switching at the threshold edge. At first, I couldn't find the reason, but eager to get results, I blindly tried and let AI program, but all returned empty-handed. Later, I calmed down and carefully looked for the reason for this situation and found that colWidth is actually minUnitColWidth, controlling the cell width of the minimum time unit, so the time corresponding to this value is inherently a changing value that changes time weight with changes in step. So theoretically both values should be parameters we need to adjust during the zoom process, not operating with one value as a reference as at the beginning. After finding this problem, I adjusted the development direction. The most important thing was to find an unchanging reference value to adjust parameters. After communicating my findings with my mentor, under the mentor's guidance, I found the correct development direction and introduced millisecondsPerPixel (milliseconds per pixel) as the reference value, solving the underlying development difficulty.

**VisActor Assistant**: What key guidance and help did mentors and the community give you? How important do you think a good mentor is for open source beginners?

**Lai Jingkang**: Mentors provided me with crucial guidance and support throughout the development process. As a newcomer who had just entered a large open source project, my understanding of the project was not comprehensive, and I encountered many unfamiliar architectural problems. I am very grateful to Mentor Liu Fangfang and Mentor Sun Rui for their patience and tolerance - they not only answered my questions in a timely manner but also continuously followed my development progress and direction, pointed out problems I had not noticed, and guided me to find appropriate solutions. We held an online progress acceptance and discussion meeting every two weeks, which not only helped me clarify the development focus for the next stage but also made high-quality suggestions on code implementation details, pushing me to continuously polish my code style and improve my development ability. It is precisely because of such patient and responsible mentors that my development work can progress steadily and achieve rapid growth in the process. It can be said that a patient and responsible mentor is the key to smooth project development and rapid growth of beginners.

**VisActor Assistant**: What do you think is the greatest value of this project for the VTable community?

**Lai Jingkang**: Before this, although VTable Gantt could display time tasks, it lacked flexible zoom interaction. Users often needed to manually adjust or rely on fixed scales, which was not user-friendly. The **intelligent zoom system** and **DataZoom integration** developed this time allow users to browse the task timeline as intuitively as operating a map: they can quickly view the macro overall schedule or zoom in to specific hour-level details. This not only improves the interaction experience but also makes VTable's application scenarios closer to real project management needs. I think this is a very critical step for the community ecosystem.

**VisActor Assistant**: In this project, what do you think is your greatest growth point?

**Lai Jingkang**: The greatest growth lies in the improvement of "systematic thinking" and "engineering ability." Previously, when doing projects at school, it was more about completing a certain function, but in open source projects, I must consider code maintainability, extensibility and team collaboration. For example, when designing zoom boundary calculations, I tried for the first time to combine mathematical models with user experience, not only to ensure that the function works but also to consider boundary stability and interaction smoothness. In addition, the development pace of the open source community also made me realize that documentation and communication are equally important. A clear design specification can often greatly improve the efficiency of development and review. It can be said that this project allowed me to move from "being able to write code" to "being able to write good code."

**VisActor Assistant**: After completing the Summer of Open Source project, what new plans or expectations do you have for the future?

**Lai Jingkang**: This experience has strengthened my determination to pursue frontend and visualization directions. In the future, I hope to continue to deeply participate in the development of the VisActor community and will try more topics related to underlying graphics rendering and performance optimization. At the same time, I also hope to share this experience with more junior students, letting them know that open source is not just "writing code" but also a way to grow rapidly in a real environment. In the long run, I hope to become a frontend engineer with full-stack vision and deep understanding of complex interaction and graphics, bringing the open source spirit to more practical work and projects.

#### --Advice and Message--

**VisActor Assistant**: What would you like to say to students planning to participate in Summer of Open Source activities?

**Lai Jingkang**: I want to say the most important thing is to dare to try and actively communicate. When you first come into contact with open source projects, you may feel that the codebase is huge and the architecture is complex, not knowing where to start, but in fact, the community and mentors are very welcoming to new students. Don't be afraid to ask questions, and don't worry that your code is not perfect enough. The key is to maintain a learning and exploring mindset.

In addition, you must plan your time in advance and grasp the rhythm well, so that you can make steady progress in the process of participating in open source projects without cramming at the last minute. When solving problems, I want to emphasize again what I said before: "Don't try to solve the problem before finding its cause." Only by understanding the essence of the problem first can you find truly effective methods.

Open source is not just about completing a project, but also a journey of learning and growth. As long as you bravely take the first step, you will find that the world of open source is more interesting and rewarding than you imagined.

