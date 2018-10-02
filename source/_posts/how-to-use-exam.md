---
title: 如何使用exam
date: 2018-03-31 20:47:28
tags: 
- 教程
categories: 教程
---
### 基本功能
可以选择本地的以.docx为结尾的题库, 选择后点击相应的条目, 即可进行测试,
管理员可以选择本地文件后上传到服务器, 并对服务器上的题目进行管理.

{% asset_img 1.jpg This is an example image %}

{% asset_img 2.jpg This is an example image %}

{% asset_img 3.jpg This is an example image %}

{% asset_img 4.jpg This is an example image %}

{% asset_img 5.jpg This is an example image %}

{% asset_img 6.jpg This is an example image %}

{% asset_img 7.jpg This is an example image %}

{% asset_img 8.jpg This is an example image %}

{% asset_img 9.jpg This is an example image %}

### 网址
[EXAM](https://exam.zackbee.cn)

### 文件类型
目前只支持以.docx为结尾的文件的解析, 试过支持.doc文件, 但是解析出来出现乱码, 不容易解析.可以自己用word将.doc另存为.docx

### 题库格式
**如果不按照格式来改动选择的本地文件, 也能够基本使用**, 可能会出现解析题目不全的情况. 云上的文件通常是由管理员上传, 一般是管理员修改后满足规定格式的.
如果你想要本地文件解析效果更好, 可以参照以下进行对文件的修改.
解析题目, 是通过下划线来判断填空题, 括号来判断选择题. 所以如果出现类似于<q>电压(工作时)</q>这样的形式, 将会被解析成选择题, 你可以将这种<q>()</q>改为<q>[]</q>. 
每个题目必须有<q>数字、</q>这样的题号, 否则解析不出来, 你可以手动地添加题号, 题号仅起到标示这是题目的作用, 即使所有的题目的题号都是<q>1、</q>, 也能够正常解析.
部分题库的题号, 会使用word的逐渐增加功能, 比如第一个题号是<q>1、</q>, 第二个自动地是<q>2、</q>. 这样的题目是解析不出来的, 可以手动删去, 再人为地加上题号.

### 注意
不能直接使用qq打开, 因为qq不支持在wev view中选择文件. 为了国内的用户能够及时地更新以及使用高级浏览器(推荐使用chrome, Firefox), 没有对浏览器做兼容处理, 如果出现了EXAM的标题, 却无法显示页面, 是因为浏览器过于古老.

### 项目地址
[地址](https://github.com/Zack-Bee/exam)