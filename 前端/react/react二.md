## 调用setState发生了什么
````
import React,{Component} from 'react'
import ReactDOM from 'react-dom'
import './index.css'

class LikeButton extends Component{
    constructor(){
        super()
        this.state = { isLike:false }
    }

    handleClickOnLikeButton(){
        this.setState({
            isLiked:!this.state.isLiked
        })
    }

    render(){
        <button onClick={this.handleClickOnLikeButton.bind(this)}>
            {this.state.isLiked ? '取消' : '点赞'} 
        </button>
    }
}
````
在代码中调用 setState 函数之后，React 会将传入的参数对象与组件当前的状态合并，然后触发所谓的调和过程（Reconciliation）。经过调和过程，React 会以相对高效的方式根据新的状态构建 React 元素树并且着手重新渲染整个 UI 界面。在 React 得到元素树之后，React 会自动计算出新的树与老树的节点差异，然后根据差异对界面进行最小化重渲染。在diff算法中，React 能够相对精确地知道哪些位置发生了改变以及应该如何改变，这就保证了按需更新，而不是全部重新渲染
## React生命周期函数
### 初始化状态
* getDefaultProps:获取实例的默认属性
* componentWillMount：组件即将被装载、渲染到页面上
* render:组件在这里生成虚拟的 DOM 节点
* componentDidMount:组件真正在被装载之后
### 运行中状态
* componentWillReceiveProps:组件将要接收到属性的时候调用
* shouldComponentUpdate:组件接受到新属性或者新状态的时候（返回 false，接收数据后不更新，阻止 render 调用）
* componentWillUpdate:组件即将更新,还没修改属性和状态
* render:组件重新描绘
* componentDidUpdate:组件已经更新
### 销毁
* componentWillUnmount:组件即将销毁
## react diff原理
* DOM树层级分解，逐步比较同级元素
* 给列表结构的每个单元添加唯一的 key 属性，方便比较
* React 只会匹配相同 class 的 component（这里面的 class 指的是组件的名字）
* 合并操作，调用 component 的 setState 方法的时候, React 将其标记为 dirty.到每一个事件循环结束, React 检查所有标记 dirty 的 component 重新绘制
* 选择性子树渲染。开发人员可以重写 shouldComponentUpdate 提高 diff 的性能
## React中refs的作用是什么？
Refs 是 React 提供给我们的安全访问 DOM 元素或者某个组件实例的句柄
## 类组件(Class component)和函数式组件(Functional component)之间有何不同
* 类组件有自身的状态和生命周期钩子，也能使组件直接访问 store 并维持状态
* 当组件仅是接收 props，将组件自身渲染到页面展示数据时，该组件就是一个 '无状态组件(stateless component)'，可以使用一个纯函数来创建这样的组件。这种组件也被称为哑组件(dumb components)或展示组件
## 何为受控组件(controlled component)
在 HTML 中，类似 input, textarea 和 select 这样的表单元素会维护自身的状态，并基于用户的输入来更新。当用户提交表单时，前面提到的元素的值将随表单一起被发送。但在 React 中会有些不同，包含表单元素的组件将会在 state 中追踪输入的值，并且每次调用回调函数时，如 onChange 会更新 state，重新渲染组件。一个输入表单元素，它的值通过 React 的这种方式来控制，这样的元素就被称为"受控元素