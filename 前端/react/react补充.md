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