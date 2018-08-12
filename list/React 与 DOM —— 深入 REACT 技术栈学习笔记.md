# React 与 DOM —— 深入 REACT 技术栈学习笔记
---
### 1.1React 与 DOM
#### 1.1.1 ReactDOM
ReactDOM 中的 API 只有： findDOMNode、unmountComponentAtNode 和 render。

![React 生命周期（图片来源：网络）](https://upload-images.jianshu.io/upload_images/8879462-f84f47aa444fc491.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### 1. findDOMNode
React 中，DOM 真正被添加到 html 中的生命周期方法是 componentDidMount 和 componentDidUpdate 方法。这两个方法中，可以获取真正的 DOM 元素。

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom'; 
class App extends Component {
   componentDidMount() {
  // this 为当前组件的实例
  const dom = ReactDOM.findDOMNode(this);
   } 
 render() {}
 }
```

如上：dom 为 findDOMNode 返回的该 React 组件实例相应的 DOM 节点。
它可以用于获取表单的 value 以及用于 DOM 的测量。

##### 2. render
要把 React 渲染的 Virtual DOM 渲染到浏览器的 DOM 中，就要使用 render 方法，因此只有在顶层组件我们才不得不使用 ReactDOM。

```
ReactComponent render(
  ReactElement element,
  DOMElement container,
  [function callback]
 ) 
```
该方法吧元素挂载到 container 中，并且返回 element 的实例（即 refs 引用）。当然，如果 是无状态组件，render 会返回 null。当组件装载完毕时，callback 就会被调用。 
当组件在初次渲染之后再次更新时，React 不会把整个组件重新渲染一次，而会用它高效的 DOM diff 算法做局部的更新。这也是 React 大的亮点之一！ 
此外，与 render 相反，React 还提供了一个很少使用的 unmountComponentAtNode 方法来进行 卸载操作。 

#### 1.1.2 ReactDOM 中的不稳定方法
 ReactDOM 中不稳定的 API 方法： unstable_renderSubtreeIntoContainer。它的作用很简单，就是更新组件到传入的 DOM 节点上。
这个方法与 render 方法很相似，但 render 方法缺少一个插入某个节点的参数。

```
render：
ReactMount._renderSubtreeIntoContainer(null, nextElement, container, callback)
unstable_renderSubtreeIntoContainer：*
ReactMount._renderSubtreeIntoContainer(parentComponent, nextElement, container, callback) 
```

两者的区别在于是否传入父节点。

#### 1.1.3 ref
在组件内， JSX 是不会返回一个组件的实例的！它只是一个 ReactElement ，只是告诉 React 被挂载的组件应该长什么样。
ref 就是为此而生的，它是 React 组件中非常特殊的 prop，可以附加到任何一个组件上。从字面意思来看，ref 即 reference，组件被调用时会新建一个该组件的实例，而 ref 就会指向这个实例。
 
它可以是一个回调函数，这个回调函数在组件被挂载后立即执行：

```
 render() {
    return (
     <div>
        <input type="text" ref={(ref) => this.myTextInput = ref} /> 
        <input    type="button"    value="Focus the text input"    onClick={this.handleClick}/>
     </div>
    );
   } 
```

它也可以放到 React 组件上，那我们获得的就是这个组件实例。
refs 同样支持字符串。对于 DOM 操作，不仅可以使用 findDOMNode 获得该组件 DOM，还可以使用 refs 获得组件内部的 DOM。比如：

```
 import React, { Component } from 'react';
 import ReactDOM from 'react-dom'; 
class App extends Component {
   componentDidMount() {
     // myComp 是 Comp 的一个实例，因此需要用 findDOMNode 转换为相应的 DOM
      const myComp = this.refs.myComp;
      const dom = findDOMNode(myComp); 
  } 
 render() { 
     return ( 
      <div> 
          <Comp ref="myComp" />
       </div>
    );
   }
 } 
```

要获取一个 React 组件的引用，既可以使用 this 来获取当前 React 组件，也可以使用 refs 来 获取你拥有的子组件的引用。 
值得注意的是，findDOMNode 和 refs 都无法用于无状态组件中，因为无状态组件挂载时只是方法调用，没有新建实例。 

#### 1.1.4 React 之外的 DOM 操作
React 的声明式渲染机制把复杂的 DOM 操作抽象为简单的 state 和 props 的操作，因此避免了很多直接的 DOM 操作。不过，仍然有一些 DOM 操作是 React 无法避免或者正在努力避免的。
 举一个明显的例子，如果要调用 HTML5 Audio/Video 的 play 方法和 input 的 focus 方法， React 就无能为力了，这时只能使用相应的 DOM 方法来实现









