---
title: 阅读redux源码
date: 2018-07-27 13:45:45
tags: 阅读源码
categories: 阅读源码
---

#### redux版本: 4.0.0 
### redux的主要代码组成
{% asset_img 代码组成.png redux的主要代码组成 %}

### 详细功能
{% asset_img 详细功能.png 详细功能 %}

### 主要流程
{% asset_img 主要流程.png 主要流程 %}

### 整体分析
redux的代码通熟易懂, 有很好的可读性. redux作为一个管理数据的工具, 使用createStore函数, 利用闭包维护着状态树(state tree). 在内部维护的listeners队列每次调用dispatch时起到了类似于事件监听的作用(我原以为是使用了自定义事件来完成这个部分, 看了源码之后觉得很巧妙). 将所有的redux相关的状态利用闭包在内部维护, 避免了污染全局变量. 在compose部分对于Array.prototype.reduce的使用让我发现了reduce的一种有效的用途, 也算是开阔了一下眼界.

源代码的注释在[github](https://github.com/Zack-Bee/read-redux)上.
