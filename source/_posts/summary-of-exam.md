---
title: 关于exam项目的总结
date: 2018-04-01 11:14:55
tags: 
- 总结
- react
- redux
- webpack
categories: 总结
---
# v1.0.0
之前一直用的原生js以及jq, 后来慢慢感受到, 随着项目越来越复杂, 没有框架的约束, 以及我飘忽不定的构建方式, 让代码的可维护性越来越低. 在做这个项目的一开始就感觉到, 这件事情并不简单. 
## react
react是现在的三大框架之一, 关于它的理念官网上有, 就不多说, 关于它我主要谈一谈自己对框架的理解与对react的理解.

### react与框架
之前用Python写过爬虫, 用node, c写过后端, 也写过前端, 但是写的都是一些非常零散的东西, 使用的是一些库, 而不是框架. 但是通过对react的理解, 我大概猜到, 无论是前后端, 还是测试等等多个领域(也可以说只是一个领域-软件工程师), 框架的作用是什么呢, 就是能够规范代码的结构, 在多人的协作过程中, 采用一种常规且通用的方法来实现项目. 因为使用框架写出来的代码, 结构是固定的, 对于了解规范与理念的协作者, 也更容易读懂他人的代码以及后期读懂自己的代码, 提高了项目的可维护性.

### react的理解
**react是一个用来完成ui的库, 它并不提供对数据的处理方法**, 单向的数据流自顶向下流动, 将数据传到更深的层级. ui注重的是可重用性, 所以使用react, 最终写出的是一个个可重用的组件. 

#### 组件的设计
在设计复杂, 可重用性强的react组件的时候, 主要样式的表现, 最好是由父组件向下, 通过传递props来进行改变子组件样式, 这样能够更好地使用数据来控制样式. 让组件自身管理数据, 通过调用自身的setState来改变自身的表现, 这样的设计在简单的应用中确实简单好用, 不需要其他开发者来管理组件的表现, 但是在复杂的应用中, 可能会出现通过数据来人为改变组件表现样式的需求, 比如在初始化页面的时候就使组件的样式根据数据达到特定的状态(在做复杂的webApp的过程中这样的需求很常见). 

react组件的设计需要看实际情况而定, 当组件或者应用特别简单时, 就可以考虑使用setState让组件来自己管理数据, 但当应用复杂, 需要用数据对组件样式实现精细控制时, 使用props来接受上层是数据控制组件的样式是一种更好的选择, 特别是结合redux等工具使用时, 能够让样式与数据分离, 极大提高可复用性.

**2018-6-17更新**
发现我当时的感悟就是完全受控组件, 包含react16.04中想要体现出来的一种思想. 文章[[译] 你可能不需要 Derived State](https://zhuanlan.zhihu.com/p/38090110)写得挺好

#### 组件的渲染
react的生命周期在官网说得很明确了, 没必要再写一遍占空间. 这里要说的是, 明确react的渲染过程.使用react一定要牢记的是**组件只是虚拟dom, 并不会在浏览器中显示, 真正在浏览器中渲染的是dom(看上去的那些html标签)**, 牢记这一点, 对于react组件渲染的理解就容易很多, 对react组件的表现也能够很好地分析.

组件在更新时, 当子树前后的根节点(组件的类型)不同时, 将卸载整个子树, 若根节点相同, 更新属性, 递归地向下更新. 组件进行渲染前接受到属性, 进行更新, 这里的更新实在虚拟dom上进行更新, 并不直接在浏览器中表现出来. react再根据虚拟dom更新仅需要更新的属性以及dom. 通常情况下, 改变props不会改变state, 这就是为什么在复杂应用中, 使用props控制样式是更好的选择.

## redux
redux和react并不是绑定在一起的, redux是JavaScript状态容器, 能够在很多场景使用. react-redux为redux与react提供了桥梁, 用于给组件提供数据. react-redux是怎么提供数据的呢? 其实就是在react组件外层包裹了一层组件, 称为container, 通过props来给组件提供数据. container监听全局state的变化, 提供分发action的dispatch.

### redux中action与reducer的设计
在简单的应用中, 我认为action与reducer并不需要过多地设计, 能够实现功能即可. 但是在复杂的应用中, reducer与action应该设计得正交化(即每个action只对应一个reducer). 比如简单的应用中, 使用如下的设计方式是完全没有问题的, 甚至是较好的实践.
{% codeblock 简单应用中 lang:javascript %}
// action与reducer一对多
// actions/doSomething.js
export default () => ({
    type: "doSomething"
})

// reducers/currentPage.js
export default (currentPage="mainPage", action) => {
    switch (action.type) {
        case "doSomething": {
            return "page1"
        }
        default: {
            return currentPage
        }
    }
}

// reducers/theme.js
export default (theme="light", action) => {
    switch (action.type) {
        case "doSomething": {
            return "dark"
        }
        default: {
            return theme
        }
    }
}
{% endcodeblock %}

但是在复杂的应用中, action与reducer的设计不正交化, 最终的结果很可能是action变得冗杂(action与reducer一对多), 或者reducer不能明确地表现应用的状态(action与reducer多对一). 上面的例子在复杂应用中可以改写为如下
{% codeblock %}
// action与reducer一对一
// actions/setCurrentPage.js
export default (page) => ({
    type: "SET_CURRENT_PAGE",
    page
})

// actions/setTheme.js
export default (theme) => ({
    type: "SET_THEME",
    theme
})

// reducers/currentPage.js
export default (currentPage="mainPage", action) => {
    switch (action.type) {
        case "SET_CURRENT_PAGE": {
            return action.page
        }
        default: {
            return currentPage
        }
    }
}

// reducers/theme.js
export default (theme="light", action) => {
    switch (action.type) {
        case "SET_THEME": {
            return action.theme
        }
        default: {
            return theme
        }
    }
}
{% endcodeblock%}

感觉正交化的设计在很多方面都是很好的实践, 比如平面坐标系的平面直角坐标系, 力的正交分解. 而css, 因为并不是正交化的设计, 多种属性之间相互作用, 所以现在css比较难学以及难以使用.(顺便提一下, [张鑫旭](http://www.zhangxinxu.com)老师对于css使用形象化理解, 反而更能节省时间. 就像现实生活中, 很多事情都是息息相关的, 我们很难去具体地分析, 靠着大脑对这个世界的适应与理解反而有时候会比较轻松)

关于react-redux使用方面的细节官方文档也提供了, 便不赘述. 对于mapStateToProps与mapDispatchToProps的设置, 官网上不是很清晰, 可以参考阮一峰老师的教程, 有例子比较容易理解.(这是觉得阮一峰老师的教程比官方文档好的为数不多的时候)

## webpack
webpack是构建应用非常好的自动化打包工具?可配置性强, 功能好用, 不过暂时没有了解webpack4. 不过这里我不是要感叹有多好用, 而是觉得使用webpack对于应用的缓存配置十分方便, 能够提高加载速度.

### webpack与应用缓存
通过webpack给最后生成的文件名加上chunck, 然后对html设置no-chache, 将生成的静态文件设施较长事件的缓存(我设置的是一年), 关闭etag节省服务器的运算能力, 第二次加载的时候只用加载非常小的html了, 甚至可以对html也关闭etag的计算, 牺牲网络请求来节省计算能力.

## 随便写点什么
还没有接触vue, 暂时对react感觉非常良好, 能够使用js精确地控制一切(不是dom, 而是组件和css), 这种完全控制的感觉非常好.

[项目地址](https://github.com/Zack-Bee/exam)