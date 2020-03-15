https://berlinen.github.io/interview-highlights/zh/configurations/js/react-part2.html
##  React 的主要特点是什么？
* 声明式设计、函数式编程
* 单向响应的数据流
* 虚拟DOM，diff
* 模块化
## React优缺点？
优点：虚拟DOM,diff,模块化开发,整合其他库  
缺点：React本身只是一个V而已，所以如果是大型项目想要一套完整的框架的话，也许还需要引入Flux和routing相关的东西；编码略复杂，学习成本较高
## 什么是 CRA 及其好处？
create-react-app【CRA】，React官网提供的脚手架(快速构建一个项目)
## vue VS React
* Vue的学习成本更低，更容易入手，模版语法开发起来个人觉得会比React的Jsx更快
* react组件受控很方便。单向树找BUG也方便。vue经常需要全局搜索
## 什么是 JSX？
HTML in JS,JSX是React的核心组成部分，它使用XML标记的方式去直接声明界面，界面组件之间可以互相嵌套
## React中元素与组件的区别
组件是由元素构成的
### 元素
````
const element = <div className="element">I'm element</div>

jsx用React.createElement()来构建元素
````
**React 元素不是真实的 DOM 元素**，它仅仅是 js 的普通对象（plain objects），所以也没办法直接调用 DOM 原生的 API。只有在这个元素渲染被完成后，才能通过选择器的方式获取它对应的 DOM 元素
### 组件
React 中有三种构建组件的方式。React.createClass()、ES6 class和无状态函数(function)
## createElement 和 cloneElement 有什么区别？
React.cloneElement()与React.createElement()相似，不同的是它传入的第一个参数是一个 React 元素，而不是标签名或组件。新添加的属性会并入原有的属性，传入到返回的新元素中，而就的子元素将被替换
## 什么是 React.Fragments？
React 中一个常见模式是为一个组件返回多个元素。Fragments 可以让你**聚合一个子元素列表，并且不在DOM中增加额外节点**
## 如何在 JSX 回调中绑定方法或事件处理程序？
````
class Component extends React.Componenet {
  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    // ...
  }
}

或者
<button onClick={this.handleClick}>
  {'Click me'}
</button>
````
## 如何将参数传递给事件处理程序或回调函数？
````
class Popper extends React.Component{
    constructor(){
        super();
        this.state = {name:'Hello world!'};
    }
    
    preventPop(name, e){    
        e.preventDefault();
        alert(name);
    }
    
    render(){
        return (
            <div>
                <p>hello</p>
                {/* Pass params via bind() method. */}
                <a href="https://reactjs.org" onClick={this.preventPop.bind(this,this.state.name)}>Click</a>
            </div>
        );
    }
}
````
## 为什么有组件名称要首字母大写？
因为浏览器是无法识别JSX语法的，因此我们需要通过 babel 对JSX语法进行转义，然后才能生成虚拟DOM对象  
babel在进行转义JSX语法时，是调用了 React.createElement() 这个方法,如果传递的是一个字符串，那么在创建虚拟DOM对象时，React会认为这是一个简单的HTML标签,**如果首字母大写，那么就会当成一个变量传递进去，这个时候React会知道这是一个自定义组件**，
## constructor 和 getInitialState 有什么区别？
constructor和getInitialState之间的区别是ES6和ES5本身之间的区别
````
//ES5
var TodoApp = React.createClass({ 
  propTypes: {
    title: PropTypes.string.isRequired
  },
  getInitialState () { 
    return {
      items: []
    }; 
  }
}); 

//ES6
class TodoApp extends React.Component {
  constructor () {
    super()
    this.state = {
      items: []
    }
  }
}; 
````
## 在使用 ES6 类的 React 中 super() 和 super(props) 有什么区别？
react 中的class 是基于es6的规范实现的, 继承是使用extends关键字实现继承的，子类必须在constructor()中调用super() 方法否则新建实例就会报错，报错的原因是 子类是没有自己的this对象的，它只能继承父类的this对象，然后对其进行加工，而super()就是将父类中的this对象继承给子类的，没有super() 子类就得不到this对象  
super(props)可以让我们在this的基础上使用构造函数里面的东西， 或者从父元素那边传过来的一些属性
## 需要把keys设置为全局唯一吗?
不需要,key是用来进行diff算法的时候进行同层比较,准备的说key只需要在兄弟节点之间唯一,一般情况key选取是后端定义的id.万不得已的时候可以选择index(选择index是万不得已的选择,因为选择了index后,一些操作会改变index的值,违背了唯一不变,在进行diff算法的时候出现问题)
## 为什么 React 使用 className 而不是 class 属性？
class创建组件的保留关键字
## 在 React 中如何使用 innerHTML？
dangerouslySetInnerHTML
## 如何有条件地渲染组件？
if、三目运算符
## 如何使用 React label 元素？
在React中，label中是要用htmlFor指向需要关联元素的ID值
## 静态类型检查推荐的方法是什么？
typeScript
## 什么是PropTypes？
 prop-types 库 类型检查
## 状态和属性有什么区别？
* 相同点：都是纯js对象，都会触发render的更新
* 不同点：需要动态修改数据的就是状态
## React-为什么不要直接改state
直接修改state，react不会重新render，setState的更新在钩子函数和合成事件中是异步的，直接修改state，react不会重新render，setState的更新在钩子函数和合成事件中是异步的，也就是说，我们在改变state的时候，需要重新生成一个对象去代替原来的state，而不是直接改原来的，直接修改state会导致state不可预期
## React调解、diff深入
https://juejin.im/post/5c3077e251882525487c30e5
## 如何使用 setState 防止不必要的更新？
在 React 16 中为了防止不必要的 DOM 更新，允许你决定是否让 .setState 更来新状态。在调用 .setState 时返回 null 将不再触发更新
## 什么时候组件的 props 属性默认为 true？
https://segmentfault.com/q/1010000008078481
## Shadow DOM 和 Virtual DOM 之间有什么区别？
* Shadow DOM 是浏览器提供的，提供了一个小型的沙盒，隔离外界的css样式，允许将隐藏的DOM树添加到常规的DOM树中——它以shadow root为起始根节点，在这个根节点的下方，可以是任意元素，和普通的DOM元素一样
* Virtual DOM 只是js模拟的DOM结构
## 什么是 React Fiber，它的主要目标是什么？
React Fiber是React v16中新的Reconciliation引擎，它的主要目标是实现虚拟DOM的增量渲染，可以将渲染工作拆分成块并将其分散到多个帧的能力  
https://zhuanlan.zhihu.com/p/77294188
## 什么是 "key" 属性，在元素数组中使用它们有什么好处?
key是一个特殊的字符串属性，你在创建元素数组时需要包含它。Keys帮助 React 识别哪些项已更改、添加或删除。
我们通常使用数据中的 IDs 作为keys
## 索引作为键的影响是什么？
当我向state中添加了一个新的item并重新排序映射时
React对哪些属性该映射到哪个组件感到困惑
## 安全地使用索引作为键的条件是什么？
* list和item是静态的——它们不会被计算，也不会改变;
* list中的item没有id;
* ist不会被重新排序或过滤
## react refs 有什么用？
React提供的这个ref属性，表示为对组件真正实例的引用，其实就是ReactDOM.render()返回的组件实例；需要区分一下，ReactDOM.render()渲染组件时返回的是组件实例；而渲染dom元素时，返回是具体的dom节点
## 如何创建 refs？
React.createRef() 方法，并通过 ref 属性添加到 React 元素上
## 什么是 forward refs？
Ref forwarding是一种将ref钩子自动传递给组件的子孙组件的技术  
https://juejin.im/post/5c0dd44b51882530e4617e92
## React组件生命周期的不同阶段是什么？
* Initialization初始化阶段：组件准备设置初始化状态和默认属性
* Mouting:react 组件已经准备装载到 DOM 上。这个阶段包含 getDerivedStateFromProps 和 componentDidMount 生命周期方法
* Updating：在这个阶段，组件通过两种方式进行更新，发送新的属性和更新状态。这个阶段包含 getDerivedStateFromProps，shouldComponentUpdate，getSnapshotBeforeUpdate 和 componentDidUpdate 生命周期方法
* Unmounting：在这个阶段，组件已经不再被需要了，他从浏览器 DOM 中卸载下来。这个阶段包含 componentWillUnmount 生命周期方法
* Error Handling：错误处理阶段，不论在渲染的过程中，还是在生命周期方法中或是在任何子组件的构造函数中发生错误，该组件都会被调用。这个阶段包含了 componentDidCatch 生命周期方法
## 在 React v15 中如何处理错误边界？
React 15 中有一个支持有限的错误边界方法 unstable_handleError。此方法不再起作用，同时自 React 16 beta 发布起你需要在代码中将其修改为 componentDidCatch
## 错误边界 无法 捕获如下错误:
* 事件处理
* 异步代码 （例如 setTimeout 或 requestAnimationFrame 回调函数）
* 服务端渲染
* 错误边界自身抛出来的错误 （而不是其子组件）
## 在 componentWillMount() 方法中使用 setState() 好吗？
因为componentWillMount是在render之前执行，所以在这个方法中setState不会发生重新渲染(re-render);  
https://zhuanlan.zhihu.com/p/24926575
## 在 React v16 中，哪些生命周期方法将被弃用？
废弃：
* componentWillMount
* componentWillReceiveProps
* componentWillUpdate

新增：
* getDerivedStateFromProps()：父组件传入的newProps和当前组件的prevState进行比较，判断时候需要更新state，返回值对象用作更新state，如果不需要则返回null
* getSnapshotBeforeUpdate：在真实的 DOM 更新前调用。可获取一些有用的信息然后作为参数传递给componentDidUpdate()
https://juejin.im/post/5ddcea315188256eaa0ebcf5
## 如何在调整浏览器大小时重新渲染视图？
````
resize = () => this.forceUpdate()

componentDidMount() {
  window.addEventListener('resize', this.resize)
}

componentWillUnmount() {
  window.removeEventListener('resize', this.resize)
} 
````
## 如何每秒更新一个组件？
shouldComponentUpdate定时器
## 为什么不能在 componentWillUnmount 中调用 setState() 方法？
在componentWillUnmount中setState是一定成功的，但由于组件即将被卸载，你的setState并不会再次触发组件的render函数，因此出现了state和render函数中不同步的问题
## 