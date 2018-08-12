# React 学习笔记——组件使用篇
---
![react生命周期](https://upload-images.jianshu.io/upload_images/8879462-b646a80a1fba09c6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 挂载阶段
我们一般在 `constructor` 里面进行组件自身状态 `state` 的初始化；

**componentWillMount()——**组件挂载开始之前、即 `render` 方法开始前调用。

一般在 `componentWillMount()` 里面进行组件的启动工作：请求数据、启动定时器；


**componentDidMount()——**组件挂载完成、即 `render` 后， `DOM` 元素已经插入页面后调用。

一般在 `componentDidMount()` 里面进行一些需要依赖 `DOM` 的操作：动画等；


**componentWillUnmount()——**组件对应的 `DOM` 元素从页面中删除前调用。

在 `componentWillUnmount()` 里面进行数据的清理工作：定时器清理，监听卸载等。


### 更新阶段
一般有两种情况会导致组件更新：属性（props）改变或状态（state）改变。

**shouldComponentUpdate(nextProps, nextState):**

这个方法可控制是否重新渲染，返回 `true` 则重新渲染，返回 `false` 则不会重新渲染。

这个方法在优化性能上非常有用，但也不易于维护，若使用此方法不能在性能上得到很大提升，则不建议轻易使用。

状态（state）改变会直接调用 `shouldComponentUpdate(nextProps, nextState)` 。


**componentWillReceiveProps(nextProps):** 

组件从父组件接收到新的 `props` 前调用，进而调用 `shouldComponentUpdate()` ，判断是否重新渲染。


**componentWillUpdate():**
组件开始重新渲染前调用。


**componentDidUpdate():**
组件重新渲染 `DOM` 更新后调用。









