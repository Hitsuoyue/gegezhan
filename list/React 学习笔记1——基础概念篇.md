# React 学习笔记——基础概念篇
---
## 1.1React 简介
React 把用户界面抽象成一个个组件，开发者通过组合这些组件，最终得到功能丰富、可交互的页面。通过引入 JSX 语法，复用组件变得非常容易，同时也能保证组件结构清晰。有了组件这层抽象，React 把代码和真实渲染目标隔离开来，除了可以在浏览器端渲染 DOM 来开发网页，还能用于开发原生移动应用。

#### 1.1.1 专注视图层
React 并不是完整的 MVC/MVVM 框架，它专注于提供清晰、简洁的 View（视图）层解决方案，又是一个包括 View 和 Controller 的库。对于复杂的应用，可根据应用场景自行选择业务层框架，并根据需要搭配 Flux、Redux、Graph/Relay 来使用。

#### 1.1.2 Virtual DOM
真实页面对应一个 DOM 树。传统页面开发中，每次要更新页面时，都要手动操作 DOM 来更新，如图：

![传统页面开发更新页面](https://upload-images.jianshu.io/upload_images/8879462-11562dcc72e1ca88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


但在前端开发中，性能消耗最大的就是 DOM 操作，且这部分代码会让整体项目的代码变得难以维护。

React 把真实 DOM 树转换成 JavaScript 对象树，即 Virtual DOM，如图：

![React Virtual DOM 页面更新](https://upload-images.jianshu.io/upload_images/8879462-02074d0e7356423c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


每次数据更新后，重新计算 Virtual DOM，并和上一次生成的 Virtual DOM 做对比，对发生变化的部分做批量更新。

React 也提供了直观的 shouldComponentUpdate 生命周期函数，来减少数据变化后不必要的 Virtual DOM 对比过程，以保证性能。

Virtual DOM 最大的好处在于方便和其他平台集成：如 react-native 就是基于 Virtual DOM 渲染出的原生控件，React 组件可以映射为对应的原生控件，输出时，是输出 Web DOM、Android 控件还是 iOS 控件，则由平台本身决定。

#### 1.1.3 函数式编程

函数式编程，对应的是声明式编程，它是人类模仿自己逻辑思考方式发明出来的。
React 把过去不断重复构建 UI 的过程抽象成了组件，且在给定参数的情况下约定渲染对应的 UI 界面。
React 能充分利用很多函数式方法去减少冗余代码。
且由于它本身就是简单函数，所以易于测试。

可以说，**函数式编程才是 React 的精髓。**

## 1.2 JSX 语法

React 为方便 View 层组件化，承载了构建 HTML 结构化页面的职责，但 React 是通过创建与更新虚拟元素来管理整个 Virtual DOM 的。

虚拟元素可以理解为真实元素的对应，它的构建与更新都是在内存中完成的，并不会真正渲染到 DOM 中去。在 React 中创建的虚拟元素可分为两类： DOM 元素和组件元素，分别对应原生 DOM 元素和自定义元素。

#### 1.2.1 JSX 的由来

* **1.  DOM 元素**
用 HTML 语法表示一个按钮：

```
<button class="btn btn-blue">
    <em>Confirm</em>
</button>
```

Web 页面是由一个个 HTML 元素嵌套组合而成，当时用 JavaScript 来描述这些元素时，这些元素可以简单地被表示成纯粹的对象，如上面的 button，用JavaScript 描述则如下，且依旧包括元素类型及属性：

```
    {
            type: 'button',
            props: {
                className: 'btn btn-blue',
                children: [{
                    type: 'em',
                    props: {children: 'Confirm'}
                }]
          }
    }
```

这样，我们就可以在 JavaScript 中创建 Virtual DOM  元素了。
但在 React 中，我们无法通过方法去调用这些元素，它们只是不可变的描述对象。

* **2.  组件元素**
如下，我们可以封装上述 button 元素，得到一种构建按钮的公共方法：

```
        const Button = ({ color, text }) => {
            return {
                type: 'button',
                props: {
                    className: `btn btn-${color}`,
                    children: {
                        type: 'em',
                        props: {    children: text,  },
                    },
                },
            };
        }
```

我们要生成 DOM 元素中具体的按钮时，就可以方便的调用 
`Button（{color: 'blue', text: 'confirm'}）`
来创建。

那么上面你的 Button 方法也可以作为元素而存在，方法名对应元素类型，参数对应 DOM 元素属性，它就具备了元素的两大必要条件，这样构建的就是自定义类型的元素（组件元素）。

用 JSON 结构来描述则如下：
`{  type: Button,  props: {    color: 'blue',    children: 'Confirm'   } }`

接下来我们可以再对 Button 进行封装：

```
        const DangerButton = ({ text }) => ({
            type: Button,
            props: {
                  color: 'red',
                children: text
              }
        });
```

也可以继续封装更复杂些的功能模块：

```
        const DeleteAccount = () => ({type: 'div',
            props: {
                children: [{type: 'p', props: {children: 'Are you sure?',},}, {
                    type: DangerButton,
                    props: {children: 'Confirm',},
                }, {type: Button, props: {color: 'blue', children: 'Cancel',},}],
            }
        });
```

但在表达这种结构时，就有些繁琐，就有了 JSX ，用 JSX 语法来描述上述组件元素如下：

```
        const DeleteAccount = () => (
            <div>
                <p>Are you sure?</p>
                <DangerButton>Confirm</DangerButton>
                <Button color="blue">Cancel</Button>
            </div>
        );
```

这样就简洁了许多。

**小结：**
JSX 是 JavaScript 语言的一种语法扩展，JSX 结构其实就是 JavaScript 对象。编译时，这种 JSX 结构会被转换成 JavaScript 的对象结构。所以，使用 React 和 JSX 一定要经过编译。

#### 1.2.2 JSX 基本语法

可以说，JSX 基本语法基本被 XML 囊括了，但也有少许不同之处。下面从基本语法、元素类型、元素属性、JavaScript 属性表达式等维度一一讲述。

##### 1.2.2.1 XML 基本语法

使用类 XML 语法的好处是标签可以任意嵌套，我们可以像 HTML 一样清晰地看到 DOM 树状结构及其属性。如构造一个 LIst 组件：

```
        const List = () => (
            <div>
                <Title>This is title</Title>
                <ul>
                    <li>list item</li>
                    <li>list item</li>
                    <li>list item</li>
                </ul>
            </div>
        );
```

> **注：**
> * 定义标签时，只允许被一个标签包裹：即最外层只能是一个标签，不能使并列的标签，否则会报错。
> * 标签一定要闭合： <div></div>、<p></p>之类标签一定要闭合， HTML 中自闭合的标签则可以自自合。自定义标签可根据是否有子组件或文本来决定是否闭合。

##### 1.2.2.2.  元素类型

如 1.2 节中说到两种元素：**DOM 元素**和**组件元素**。在 JSX 里对应规则是：DOM 元素 HTML 标签首字母小写，组件元素 HTML 标签首字母大写。

HTML 标准中，还有些**特殊的标签**：

**注释：**
HTML 中，注释写成<!-- content --> 这样的形式，但 JSX 中并没有定义注释的转换方法，事实上，JSX 还是 JavaScript，依然可以用简单方法注释，但在一个组建的子元素位置使用注释要用 {} 包起来，否则会在页面中直接显示出来。如：

```
        const App = (
            <Nav>
                {/* 节点注释 */}
                <Person
                    /* 多行 
                      注释 */
                    name={window.isLoggedIn ? window.name : ''}/>
            </Nav>
        );
```

**DOCTYPE：**
DOCTYPE 头是一个非常特殊的标志，一般会在使用 React 作为服务端渲染时用到。在 HTML 中，DOCTYPE 是没有闭合的，也就是说我们无法渲染它。 常见的做法是构造一个保存 HTML 的变量，将 DOCTYPE 与整个 HTML 标签渲染后的结果串连起来。第 7 章会详细讲到。

##### 1.2.2.3.  元素属性

**DOM 元素：**
DOM 元素的属性是标准规范属性，但有两个例外——class 和 for，在 JavaScript 中这两个单词都是关键词。因此，React 将其这样转换：
> class 属性改为 className；
> for 属性改为 htmlFor。


**组件元素：**
组件元素的属性是完全自定义的属性，也可以理解为实现组件所需要的参数。如：

`const Header = ({title, children}) => (  <h3 title={title}>{children}</h3> );`

我们给 Header 组件加了一个 title 属性，则可以这样调用：

`<Header title="hello world">Hello world</Header>`

可以看出，Header 中 title 属性代表的是自定义标签的属性可以传递；
h3 中的 title 属性是标签自带的属性无法传递。

**注：**自定义属性都使用小驼峰写法。

**JSX 特有的属性表达：**

Boolean 属性：Boolean 属性省略时默认为 true，要传 false，则必须使用属性表达式。如下：

<Checkbox checked={true} /> 可以简写为 <Checkbox checked />，反之，设置 checked 属性为 false 时则必须写： <Checkbox checked={false} />。

**展开属性：**
如果事先知道组件需要的全部属性，JSX 可以这样来写：
`var component = <Component foo={x} bar={y} />;`
如果你不知道要设置哪些 props，那么现在好不要设置它：

```
var component = <Component />; 
component.props.foo = x; // 不好 
component.props.bar = y; // 同样不好
```

上面这是反模式，因为 React 不能帮你检查属性类型（propTypes）。这样即使你的 属性类型有错误也不能得到清晰的错误提示。

**Props 应该被认为是不可变的。**在别处修改 props 对象可能会导致预料之外的结果，所以原则上这将是一个冻结的对象。

那么你可以使用 JSX 的新特性——**展开属性**：（使用了 ES6 的 rest/spread 特性（...））

```
var props = {};
props.foo = x; 
props.bar = y;
var component = <Component {...props} />;
```

传入对象的属性会被复制到组件内。它能被多次使用，也可以和其它属性一起用。注意顺序很重要，后面的会覆盖掉前面的。

```
var props = { foo: 'default' }; 
var component = <Component {...props} foo={'override'} />; 
console.log(component.props.foo); // 'override'
```

**自定义 HTML 属性：**

DOM 标签自定义属性要使用 data- 前缀，这与 HTML 标准一致：
`<div data-attr="xxx">content</div>`

组件标签中任意属性都是被支持的：
`<x-my-component custom-attr="foo" />`

##### 1.2.2.4.  JavaScript 属性表达式

属性值要使用表达式，即用 **{}** 代替 ""即可。
`const person = <Person name={window.isLoggedIn ? window.name : ''} />;`

子组件也可以使用表达式：
`const content = <Container>{window.isLoggedIn ? <Nav /> : <Login />}</Container>;`


##### 1.2.2.5.  HTML 转义
React 会将所有要显示到 DOM 的字符串转义，防止 XSS。所以，如果 JSX 中含有转义后的 实体字符，比如 &copy;（©），则后 DOM 中不会正确显示，因为 React 自动把 &copy; 中的特 殊字符转义了。有几种解决办法： 

> 直接使用 UTF-8 字符 ©； 
> 使用对应字符的 Unicode 编码查询编码； 
> 使用数组组装 <div>{['cc ', <span>&copy;</span>, ' 2015']}</div>；
> 直接插入原始的 HTML。 

此外，React 提供了 **dangerouslySetInnerHTML** 属性。正如其名，它的作用就是避免 React 转 义字符，在确定必要的情况下可以使用它：
`<div dangerouslySetInnerHTML={{__html: 'cc &copy; 2015'}} />`

##### 1.2.2.6.  事件监听
React.js 不需要手动调用 addEventListener 进行监听，它帮我们封装好了一系列的 on* 属性，当你要为某元素监听某个事件时，只需要简单地给它加上相应的 on* 即可。你也不需要考虑不同浏览器兼容性问题，React.js 已经将其封装好了。
**注：**on*只能用在普通的 HTML 标签上，而不能用在组件上。

**event 对象**
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

**关于事件中的 this**
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

## 1.3 React 组件
#### 1.3.1 组件的演变
**传统组件：**
组件封装的基本思路就是面向对象思想。交互基本上以操作 DOM 为主，结构上哪里需要变，就操作哪里。
> 基本的封装性：通过实例化方法来构造对象。
> 简单的生命周期呈现：如 constructor 和 destroy 代表组建的挂载和卸载过程。
> 明确的数据流动：根据传入参数的不同做出不同相应，从而得到渲染结果。
Web Components：
![Web Components](https://upload-images.jianshu.io/upload_images/8879462-3153f204feb322e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1.3.2 React 组件的构建
React 组件即为组件元素。组件元素被描述成纯粹的 JSON 对象，意味着可以使用方法或类来构建。
React 组件基本上由 3 个部分组成——属性（props）、状态（state）以及生命周期方法。如下图：
![React 组件](https://upload-images.jianshu.io/upload_images/8879462-5ba7f635d9248797.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

React 组件可以接收参数（props），也可能由自身状态（state），接收到的参数或自身状态有所改变，都会导致 React 组件执行相应的生命周期方法，最后渲染。

React 组件引用方式遵循 ES6 module 标准。

**React 组件的构建方法**

官方在 React 组件构建上提供了 3 种不同的方法：**React.createClass**、**ES6 classes** 和**无状态函数**。

**1.  React.createClass**
用 React.createClass 构建组件是 React 最传统、兼容性最好的方法。

```
const Button = React.createClass({
    getDefaultProps() {
        return {
            color: 'blue',
            text: 'Confirm',
        };
    },
    render() {
        const { color, text } = this.props;
        return (
            <button className={`btn btn-${color}`}>
                <em>{text}</em>
            </button>
        );
    }
});
```

从表象看，React.createClass 方法就是构建一个组件对象。另一个组件要调用 Button 组件时，只需写成 `<Button/>`,就可以被解析成 React.createClass(Button) 方法来创建 Button 实例。

React.createClass会自绑定函数方法（不像[React.Component](http://react.component/)只绑定需要关心的函数）导致不必要的性能开销，增加代码过时的可能性。

**2.  ES6 classes**

ES6 classes 的写法是通过 ES6 标准的类语法方式来构建方法：

```

import React, { Component } from 'react';

class Button extends Component {
    constructor(props) {
        super(props);
    }
    static defaultProps = {
        color: 'blue',
        text: 'Confirm',
    };
    render() {
        const { color, text } = this.props;
        return (
            <button className={`btn btn-${color}`}>
                <em>{text}</em>
            </button>
        );
    }
}
```

这里的直观感受是从调用内部方法变成了用类来实现。与 createClass 的结果相同的是，调用类实现的组件会创建实例对象。

**说明：**
React 的所有组件都继承自顶层类 [React.Component](http://react.component/)。它的定义非常简洁，只是初始化了[React.Component](http://react.component/)方法，声明了 props、context、refs 等，并在原型上定义了 setState 和forceUpdate 方法。内部初始化的生命周期方法与 createClass 方式使用的是同一个方法创建的。

**3.  无状态函数**
使用无状态函数创建的组件成为无状态组件，是官方颇为推崇的组件。

```
function Button({ color = 'blue', text = 'Confirm' }) {
    return (
        <button className={`btn btn-${color}`}>
            <em>{text}</em>
        </button>
    );
}
```

无状态组件只传入 props 和 context 两个参数，不存在 state，也没有生命周期方法组件本身即是一个 render 方法。不过，像 propTypes 和 defaultProps 还是可以通过向方法设置静态属性来实现的。

**注：**只要有可能，尽量使用无状态组件。

无状态组件在调用时不会创建新实例，它创建时始终保持了一个实例，避免了不必要的检查和内存分配，做到了内部优化。
