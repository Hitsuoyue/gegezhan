# React 学习笔记——组件使用篇
---
React 核心思想 —— 组件化
React 将界面分成了一个个组件，通过组件的组合、嵌套构成页面。其中，组件可复用，提高开发效率。

### 点赞按钮实现
##### 1.原生 JavaScript 实现：

```
<!DOCTYPE html>
<html lang="en">

<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>Document</title>
</head>

<body>
<div>
<button class="like-btn">
<span class="like-text">点赞</span>
</button>
</div>
<script>
const btn = document.querySelector('.like-btn');
const btnText = btn.querySelector('.like-text');
let isLiked = false;
btn.addEventListener('click', () => {
isLiked = !isLiked;
if (isLiked) {
btnText.innerHTML = '取消';
} else {
btnText.innerHTML = '点赞';
}
}, false)
</script>
</body>

</html>
```

以上，可实现点击按钮点赞取消功能，但若其他地方或其他项目想要用这个按钮，需要把 button 相关结构及相关 JS 代码全部复制过去，即复用性不强。
而 React 的组件化则可解决一个结构（组件）的复用问题。


##### 2.React 实现：


**点赞按钮组件：**

```
import React, { Component } from 'react';

class ThumbsUp extends Component{    
        constructor(props){
            super(props);
            this.state = {
                isLiked: false
            }
        }    
        
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.setState({
                isLiked: !this.state.isLiked
            })
        }
        
        render(){
            const { isLiked } = this.state;
            const innerText = isLiked ? '取消' : '点赞';
            return(
                <button className='like-btn' onClick={this.isLikedChange}>
                    <span>{innerText}</span>
                </button>
            )
        }
    }

export default ThumbsUp;
```

**在要使用的地方引入点赞按钮**

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import ThumbsUp from './ThumbsUp.jsx';

class App extends Component {
componentDidMount(){
}
render() {
return (
<ThumbsUp/>
)
}
}

ReactDOM.render( < App / > , document.getElementById('app'));
```

以上，点赞按钮中，isLiked 是一个在组件的状态 this.state 中维护的变量，点击按钮时只需要通过 setState 方法来改变 isLiked 的状态，set
State 方法会触发组件的重新渲染，渲染时，会根据 this.state.isLiked 的不同重新构建不同的 DOM 元素。
如上，使用 React 的组件实现了 点赞按钮的复用，你可以在任何地方调用点赞组件，实现点赞取消功能。

**敲黑板：调用 setState ，组件会重新渲染。**

##### 3.React 组件配置优化
如果想通过父组件(<App/>)来控制子组件(ThumbsUp)，可以通过在调用子组件时给子组件属性赋值来实现数据传递。

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import ThumbsUp from './ThumbsUp.jsx';

class App extends Component {
    constructor(props){
        super(props);
        this.state = {
            color: 'red'
        }
    }

    render() {
        return ( 
            <div>
                <ThumbsUp color={this.state.color}/>
            </div>            
        )
    }
}

ReactDOM.render( < App / > , document.getElementById('app'));
```

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import ThumbsUp from './ThumbsUp.jsx';

class App extends Component {
    constructor(props){
        super(props);
        this.state = {
            color: 'red'
        }
    }

    colorChange = () => {
        console.log('index---change');
        let color = this.state.color === 'red' ? 'blue' : 'red';
        this.setState({
            color: color
        })
    }

    render() {
        return ( 
            <div onClick={this.colorChange}>
                <ThumbsUp color={this.state.color}/>
            </div>            
        )
    }
}

ReactDOM.render( < App / > , document.getElementById('app'));
```

如上，将<App/> 的 this.state.color 传给 <ThumbsUp/> 组件，在 <ThumbsUp/> 组件里，继承时通过 super(props) 把 props 传给父类，这样就可以通过 this.props 获取到配置参数。此处 this.props.color 即为 <App/> 组件传递过来的数据。

**小结：**
一个组件有自己的显示形态和行为，其显示形态和行为可以有数据状态 (state) 和 (配置参数) 共同决定。数据状态和配置参数的改变都可能会影响这个组件的显示形态。

### 使用 JSX 描述 UI 信息

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import ThumbsUp from './thumbs-up/ThumbsUp.jsx';
import UI from './ui/UI.jsx';

class App extends Component {
    constructor(props){
        super(props);
    }

    render() {
        return ( 
            <div>
                <h1 className='content'>react render</h1>
            </div>        
        )
    }
}

ReactDOM.render( < App / > , document.getElementById('app'));
```

如上，我们在头部文件中从 'react' 包中引入了 React 和 Component(React.js 的组件父类)，只要你要写 React.js 组件，就必须要引入 React 和 Component。
ReactDOM 是 React 组件渲染到页面上需要用到的。

后面的代码，一个组件继承了 Component 类，有一个 render 方法，在 render 里面返回这个组件的 HTML 结构。这就是所说的 JSX 语法——在 JavaScript 里写标签。

##### JSX 原理
**JSX 其实就是  JavaScript 对象**
JSX 语法是把 JavaScript 语法进行了扩展，让 JavaScript 语言能够支持这种直接在 JavaScript 里面写类似于 HTML 标签结构的语法，编译时，这种 JSX 结构会被转换成 JavaScript 的对象结构，如上面代码编译后会转换为：

```
ReactDOM.render( < App / > , document.getElementById('app'));

import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import './index.css'

class Header extends Component {
  render () {
    return (
     React.createElement(
        "div",
        null,
        React.createElement(
          "h1",
          { className: 'content' },
          "react render"
        )
      )
    )
  }
}

ReactDOM.render(
  React.createElement(Header, null), 
  document.getElementById('root')
);
```

React.createElement 会构建一个 JavaScript 对象来描述 HTML 结构的信息，包括标签名、属性、子元素，这样的代码就是合法的 JavaScript 代码了。所以使用 React 和 JSX 一定要经过编译。

有了这个表示 HTML 结构和信息的对象后，就可以通过 ReactDOM.render 去构造真正的 DOM 元素，然后把这个 DOM 元素塞到页面上。

**小结：**
1. JSX 是 JavaScript 语言的一种语法扩展。
2. JSX 到页面的过程： JSX 通过编译转化为 JavaScript 对象，JavaScript 对象通过ReactDOM.render 渲染为 DOM 元素，最后插入到页面中。

### 三.组件的 render 方法
在编写 Reac.js 组件时，需要继承 Component 父类，其有一个 render 方法必须要返回一个 JSX 元素，要注意的是，必须要用一个外层的 JSX 元素把所有内容包裹起来，返回并列多个 JSX 元素是不合法的。如下：

```
不合法：
render () {
  return (
    <div>第一个</div>
    <div>第二个</div>
  )
}

合法：
render () {
  return (
    <div>
      <div>第一个</div>
      <div>第二个</div>
    </div>
  )
}
```

##### 表达式插入
JSX 中你可以插入 JavaScript 表达式，表达式返回结果会相应渲染到页面上，表达式用 {} 包裹。
{} 内可以放任何  JavaScript  代码，包括变量、表达式计算、函数执行等等。例如：
```
render () {
  return (
    <div>
      <h1>React 小书 {(function () { return 'is good'})()}</h1>
    </div>
  )
}
```
表达式不仅可以用在标签内部，也可以用在标签属性上，例如：
```
render () {
  const className = 'header'
  return (
    <div className={className}>
      <h1>Hello React!</h1>
    </div>
  )
}
```
这样就可以为 div 标签添加一个 header 的类名。

**特例：**
因为 class 是  JavaScript  的关键字，所以 React 中用 className 来给元素添加类名；
因为 for 也是   JavaScript  的关键字，在类似于 `<label for='male'>Male</label>` 中，React 用 htmlFor 代替 for：`<label htmlFor='male'>Male</label>`。

##### 条件返回
如上面所说，JSX 内可以放任何表达式内容，也可以放 JSX，如下，可以在 render 内根据不同条件返回不同的 JSX。
```
//flag 为 true 时，显示  <strong> Hello React!</strong>，否则显示<span>Goodbye React!</span>
render () {
  const flag= true
  return (
    <div>
      <h1>
        {flag
          ? <strong> Hello React!</strong>
          : <span>Goodbye React!</span>
        }
      </h1>
    </div>
  )
}
```

如果表达式返回 null，则 React 会忽略该表达式插入，什么都不现实。结合条件返回，就可以显示或隐藏某些元素。

 ```
//flag 为 true 时，显示  <strong> Hello React!</strong>，否则隐藏。
render () {
  const flag= true
  return (
    <div>
      <h1>
        {flag
          ? <strong> Hello React!</strong>
          : null
        }
      </h1>
    </div>
  )
}
```

##### JSX 元素变量
JSX 元素可以像 JavaScript 对象那样自由地赋值给变量或者作为函数参数传递、或作为函数的返回值。

```
render () {
  const flag= true
  const hello = <strong>Hello React!</strong>
  const bye = <span> Goodbye React！</span>
  return (
    <div>
      <h1>
        {flag? hello : bye }
      </h1>
    </div>
  )
}
```
```
renderElem (hello, bye) {
  const flag= true
  return flag? hello : bye
}

render () {
  return (
    <div>
      <h1>
        {this.renderElem (
          <strong>Hello React!</strong>,
           <span> Goodbye React！</span>
        )}
      </h1>
    </div>
  )
}
```

### 组件嵌套
如下，用 Tree 组件来嵌套 Header 、Body、Footer 组件，再在 App 组件内调用 Tree 组件，即又进行了一层嵌套。

```
import React, { Component } from 'react';

class Header extends Component{
    render(){
        return(
            <h1>Header</h1>
        )
    }
}

class Body extends Component{
    render(){
        return(
            <h1>Body</h1>
        )
    }
}

class Footer extends Component{
    render(){
        return(
            <h1>Footer</h1>
        )
    }
}

export default class Tree extends Component {
    render() {
        return (
            <div>
                <Header/>
                <Body/>
                <Footer/>
            </div>
        );
    }
}
```

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import ThumbsUp from './thumbs-up/ThumbsUp.jsx';
import UI from './ui/UI.jsx';
import Tree from './tree/Tree.jsx';

class App extends Component {
    constructor(props){
        super(props);
    }

    render() {
        console.log('[render');
        return ( 
            <Tree />         
        )
    }
}

ReactDOM.render(<App/>, document.querySelector('#app'))
```
如上，形成了一个组件树，App 是最上层的父组件，潜逃了 Tree 组件，Tree 组件又嵌套了 Header 、Body、Footer 组件， Header 、Body、Footer 组件属于平级的组合关系。

### 事件监听
React.js 不需要手动调用 addEventListener 进行监听，它帮我们封装好了一系列的 on* 属性，当你要为某元素监听某个事件时，只需要简单地给它加上相应的 on* 即可。你也不需要考虑不同浏览器兼容性问题，React.js 已经将其封装好了。
**注：**on*只能用在普通的 HTML 标签上，而不能用在组件上。

##### event 对象
和普通浏览器一样，事件监听函数会自动传入一个 event 对象，这个对象和普通而浏览器 event 对象所包含的方法和属性基本一致。不同的是，这个对象并不是浏览器所提供的，而是 React.js 自己内部构建的。

```
        render(){
            const { isLiked } = this.state;
            const innerText = isLiked ? '取消' : '点赞';
            return(
                <button className='like-btn' onClick={this.isLikedChange} style={{background: this.props.color}}>
                    <span>{innerText}</span>
                </button>
            )
        }
```

##### 关于事件中的 this
React.js 在调用你所传给它的方法时，并不是通过对象方法的方式调用，而是直接通过函数调用，所以事件监听函数内并不能通过 this 获取到当前实例。如果你想在事件函数中使用当前的实例，你需要手动地将实例方法 bind 到当前实例上再传入给 React.js。

```
        render(){
            const { isLiked } = this.state;
            const innerText = isLiked ? '取消' : '点赞';
            return(
                <button className='like-btn' onClick={this.isLikedChange.bind(this)} style={{background: this.props.color}}>
                    <span>{innerText}</span>
                </button>
            )
        }
```

bind 会把实例方法绑定到当前实例上，然后我们再把绑定后的函数传给 React.js 的 onClick 事件监听。

## React 数据流
React 中，数据是自顶向下单向流动的，即从父组件到子组件，这条原则让组件间的关系变得简单且可预测。

组件的属性（**props**）和自身状态（**state**）是组件中最重要的概念。

如果顶层组件初始化 props 或 state 改变（父组件 state 改变会触发子组件对应生命周期函数 componentWillReceiveProps），React 会向下遍历整棵组件树，重新尝试渲染所有相关子组件。

state 是每个组件自己内部的状态，这些状态只能在组件内改变。

把组件看成一个函数，它接收了 props 作为参数，内部由 state 作为函数的内部参数，返回一个 Virtual DOM 的实现。

### 组件的 state 和 setState
##### state
如前面所说，一个组件的显示形态由它的数据形态和配置参数决定。
一个组件可以拥有自己的状态 —— state 就是来存储这种可变化的状态的。如前面的点赞按钮，span 可以根据当前状态的不同来显示不同的内容。

```
import React, { Component } from 'react';

class ThumbsUp extends Component{    
        constructor(props){
            super(props);
            this.state = {
                isLiked: false
            }
        }    
        
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.setState({
                isLiked: !this.state.isLiked
            })
        }
        
        render(){
            const { isLiked } = this.state;
            const innerText = isLiked ? '取消' : '点赞';
            return(
                <button className='like-btn' onClick={this.isLikedChange} style={{background: this.props.color}}>
                    <span>{innerText}</span>
                </button>
            )
        }
    }

export default ThumbsUp;
```

##### setState()
setState() 是所继承的 Component 提供的一个方法，用来更新组件的状态 state，重新调用 render 方法，再把最新内容渲染到页面上。
**注：**要改变组件状态时，不能使用 this.state=xxx ，这样 React.js 不会知道你修改了组件的状态，也不会更新页面。
setState() 方法接受一个对象或函数作为参数。
**setState() 接受对象参数**
传入对象时，这个对象表示该组件要更新的部分的新状态，不需要传入整个状态。
如点赞按钮的例子，state 里面还有其他的状态 num，但在改变 点赞状态时，不需要修改其他的状态：

```
import React, { Component } from 'react';

class ThumbsUp extends Component{    
        constructor(props){
            super(props);
            this.state = {
                isLiked: false,
                num: 1
            }
        }    
        
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.setState({
                isLiked: !this.state.isLiked
            })
        }
        
        render(){
            const { isLiked } = this.state;
            const innerText = isLiked ? '取消' : '点赞';
            return(
                <button className='like-btn' onClick={this.isLikedChange} style={{background: this.props.color}}>
                    <span>{innerText}</span>
                </button>
            )
        }
    }

export default ThumbsUp;
```

**setState() 接受函数参数**
**注：**
当你调用 setState() 时，React.js 不会马上修改 state，而是会把这个对象放到一个更新队列里面，稍后才会从脆裂中把新的状态提取出来合并到 state 中，然后再触发组件更新。如下：

```
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
           console.log(this.state.isLiked);
            this.setState({
                isLiked: !this.state.isLiked
            })
            console.log(this.state.isLiked);
        }
```

你会发现两次打印的状态是相同的，所以你获取到的还是原来的 isLiked 状态。
所以若你想在 setState() 后面直接使用更新后的状态，是不可行的，因为此时还没有改变，如下：

```
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.setState({
                isLiked: !this.state.isLiked
            })
            this.setState({ num: 0 }) //  undefined
            this.setState({ num: this.state.num + 1}) //  NaN
            this.setState({ num: this.state.num + 2}) //  NaN
        }
```

最后的值为 NaN。
这里，就可以使用函数作为参数的方法了，这个函数的会得到 React.js 传入的上一次setState() 的结果。

```        
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.setState({
                isLiked: !this.state.isLiked
            })
            this.setState((prevState)=>{
                return { num: 0 }
            })
            this.setState((prevState)=>{
                console.log('prevState', prevState);
                return { num: prevState.num + 1 }
            }) // prevState {isLiked: true, num: 0} num：1
            this.setState((prevState)=>{
                console.log('prevState', prevState);
                return { num: prevState.num + 1 }
            })  // prevState {isLiked: true, num: 1} num：2
            this.setState((prevState)=>{
                console.log('prevState', prevState);
                return { num: prevState.num + 1 }
            }) //prevState {isLiked: true, num: 2} num：3
        }
```

这样就可以利用上一次 setState() 的结果进行运算。

**小结：**
上面在一次点击操作中进行了三次 setState()，但实际上组件只会重新渲染一次，而非三次。因为 setState 是一个异步方法，一个生命周期内所有的 setState 方法会合并操作，之后再重新渲染组件。
**即：**在使用 React.js 时，不必担心多次进行 setState 会带来性能问题，React.js  内部会对其进行处理。

### 配置组件的 props
组件是相互独立、可复用的单元，可能在不同的地方被用到。比如前面的点赞按钮在其他场景用到时要根据是否已点赞的状态给按钮相应的颜色，但每个地方用的时候颜色不一定相同，那么这个颜色配置就不能写在组件里面，而是要在使用的它的地方进行配置，props 就可以打到这个效果。
props——每个组件都可以接受一个 props 参数，它是一个对象，包含了所有你对这个组件的配置，如：

```
//调用点赞按钮的父组件通过属性给点赞按钮组件传递 color 
class App extends Component {
    constructor(props){
        super(props);
    }

    render() {
        return ( 
            <ThumbsUp color={{liked: 'red',unliked: 'green'}}/>         
        )
    }
}
```

```
//点赞组件通过 this.props.color 拿到了父组件传递的数据，根据自己的点赞状态再进行渲染，这样其他人在调用时只需要传递一个 color 属性，规定点赞后和未点赞时要展示的颜色即可。
        render(){
            const { isLiked } = this.state;
            const innerText = isLiked ? '取消' : '点赞';
            const color = isLiked ? this.props.color.liked : this.props.color.unliked;
            return(
                <button className='like-btn' onClick={this.isLikedChange} style={{background: color}}>
                    <span>{innerText}</span>
                </button>
            )
        }
```

父组件可以通过标签上的属性传递给子组件的数据，自组件都可以通过 this.props 获取到。且数据可以为各种形式：字符串、数字、对象、数组、函数都可以。
现在，如果我们想在父组件内实现：
1. 设定color  和 text 的状态，通过属性传递给子组件；
2. 获取子组件的点赞状态，在父组件内的标题里显示当前状态

```
class App extends Component {
    constructor(props){
        super(props);
        this.state = {
            isLiked: false
        }
    }

    onStatusChange = (isLiked) => {
        this.setState({
            isLiked: !isLiked
        })
    }

    render() {
        const color={liked: 'red', unLiked: 'green'};
        const text={liked: '取消', unLiked: '点赞'};
        const title = this.state.isLiked ? '已点赞' : '未点赞'
        return ( 
            <div>
                <h1>点赞状态：{title}</h1>
                 <ThumbsUp onStatusChange={this.onStatusChange} text={text}/>  
            </div>
                  
        )
    }
}
```

```
class ThumbsUp extends Component{    
        constructor(props){
            super(props);
            this.state = {
                isLiked: false
            }
        }    
        
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.setState({
                isLiked: !this.state.isLiked
            })
            if(this.props.onStatusChange){
                this.props.onStatusChange(this.state.isLiked)
            }
        }
        
        render(){
            const { isLiked } = this.state;
            let text = isLiked ? this.props.text && this.props.text.liked || '取消' : this.props.text && this.props.text.unLiked || '点赞';
            let color = isLiked ? this.props.color && this.props.color.liked || 'red' : this.props.color && this.props.color.unLiked || 'green';
            return(
                <button className='like-btn' onClick={this.isLikedChange} style={{background: color}}>
                    <span>{text}</span>
                </button>
            )
        }
}
```

现在，点赞状态时的颜色和文字均由父组件来配置即可，可以在各处根据需求随意配置。

##### 配置默认 defaultProps
上面点赞组件中，默认配置是通过 || 来实现的，这种需要默认配置的情况在 React.js 中非常常见，所以 React.js 也提供了一种方式 defaultProps，可以方便的做到默认配置。如下：

```
class ThumbsUp extends Component{    

        static defaultProps = {
            color: {liked: 'red', unLiked: 'green'},
            text: {liked: '取消', unLiked: '点赞'}
        }

        constructor(props){
            super(props);
            this.state = {
                isLiked: false
            }
        }    
        
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.setState({
                isLiked: !this.state.isLiked
            })
            if(this.props.onStatusChange){
                this.props.onStatusChange(this.state.isLiked)
            }
        }
        
        render(){
            const { isLiked } = this.state;
            let text = isLiked ? this.props.text && this.props.text.liked || '取消' : this.props.text && this.props.text.unLiked || '点赞';
            let color = isLiked ? this.props.color && this.props.color.liked || 'red' : this.props.color && this.props.color.unLiked || 'green';
            return(
                <button className='like-btn' onClick={this.isLikedChange} style={{background: color}}>
                    <span>{text}</span>
                </button>
            )
        }
}
```

上面通过 defaultProps 对 this.props 进行了默认配置，在 render 可以直接使用 this.props 的相关属性而不必再判断。

##### props 不可变
在子组件里，this.props 是不可改变的。

```
        /**
         * 点击按钮，改变状态函数
         */
        isLikedChange = () => {
            this.props.color = {}
            this.setState({
                isLiked: !this.state.isLiked
            })
            if(this.props.onStatusChange){
                this.props.onStatusChange(this.state.isLiked)
            }
        }
```

如上，执行 `this.props.color = {}` 会导致报错
![报错](https://upload-images.jianshu.io/upload_images/8879462-23835e8758bf5522.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

若想改变组件的 this.props，只能改变父组件传进来的值，如在父组件进行 setState()，会导致父组件重新渲染，子组件也会传入新的 props，重新渲染。

### state VS props
**state：**——组件控制自己的状态
主要用于组件保存、控制、修改自己的可变状态。
在组件内部初始化，可被组件自身修改，外部不能访问也不能修改。
可通过 setState 进行更新，setState 会导致组件重新渲染。
**props：**——外部组件来控制自己的状态
使该组件的父组件可以传入参数来配置该组件。
外部传入的配置参数，组件内部无法控制也不乏修改。除非外埠组建主动传入新的 props，否则组件的 props 永远保持不变。

##### 无状态组件
没有 state 的组件叫做无状态组件，有 state 的组价叫做有状态组件。
因为状态会带来管理的复杂性，应尽量写无状态组件，这样会降低代码维护难度，也会在一定程度上增强组件的可复用性。

React.js 在0.14 版本引入了函数式组件——不能使用 state，一个原来要这样写的组件：

```
class HelloWorld extends Component {
  constructor() {
    super()
  }

  sayHi () {
    alert('Hello World')
  }

  render () {
    return (
      <div onClick={this.sayHi.bind(this)}>Hello World</div>
    )
  }
}
```

可以写成这样：

```
const HelloWorld = (props) => {
  const sayHi = (event) => alert('Hello World')
  return (
    <div onClick={sayHi}>Hello World</div>
  )
}
```

### 渲染列表数据
如果你往 JSX 的 {} 放一个数组，React.js 会帮你把数组里面一个个元素罗列并且渲染出来。
对于用表达式套数组罗列到页面上的元素，都要为每个元素加上 key 属性，这个 key 必须是每个元素唯一的标识。
一般来说，key 的值可以直接后台数据返回的 id，因为后台的 id 都是唯一的。

本书为阅读 《React.js 小书》的读书笔记
原文地址：http://huziketang.com/books/react/










