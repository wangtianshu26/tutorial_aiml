\<srai>标签
================
\<srai>能够简化对话流程，帮助编写者更快的编写聊天机器人。当不同的\<pattern>有着相同或部分相同的回答时，这一标签可以将\<template>中的内容转到统一的回答上面。下面一起来看一下如何应用它吧！  

比如我现在想实现一下对话：  

User：I like eating carrots very much.  
Bot：Carrots are good for people's health. Glad to know you like them.
User：I hate carrots.  
Bot：Carrots are good for people's health. You should definitely try them.  

可以看到在这一对话中，bot响应的内容有重复的部分，那么如何避免重复编写呢？请看下面的代码：  

![14](images/14.png)  

看到这里您是否明白 `<srai>` 标签的作用了呢？第二个对话的回答想引用第一个对话的回答，于是在 `<template>` 标签中添加了 `<srai>` ,内容是第一个对话的 `<pattern>` 中的内容。  

测试：  

![15](images/15.png)  

这一节中我们介绍了 `<srai>` 标签的作用，在下一节中我们将继续探索其他标签，使我们的聊天机器人更加丰富！一起学习吧！