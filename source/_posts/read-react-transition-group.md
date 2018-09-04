---
title: 阅读react-transition-group源码
date: 2018-09-03 22:49:23
tags: 阅读源码
categories: 阅读源码
---

#### react-transition-group版本: 2.4.0

### react-transition-group主要代码组成
{% asset_img 代码组成.png react-transition-group的主要代码组成 %}

### 详细功能
{% asset_img 详细功能.png 详细功能 %}

### 主要流程
这部分直接看源码即可, 过程比较清晰, 就是状态机转化状态

### 整体分析
react-transition-group使用状态机维护状态, 将in属性的变化映射成各个状态, 通过state暴露给子组件. 源码中关于闭包的使用看起来比较优雅, 值得学习. TransitionGroup使用的childMapping看起来比较难理解.

源码的注释在[github](https://github.com/Zack-Bee/read-react-transition-group)