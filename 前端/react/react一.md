https://juejin.im/post/5d5f44dae51d4561df7805b4
## setState只在合成事件和钩子函数中是“异步”，在原生事件和setTimeout 中都是同步的
### DOM事件流
* 事件捕获阶段:当某个事件触发时，文档根节点最先接收到事件，然后根据DOM树结构自顶向下向具体绑定事件的原生
* 目标阶段:具体元素已经捕获事件。之后事件开始向根节点冒泡
* 事件冒泡
### 合成事件
React并不是将click事件直接绑定在dom上面，而是采用事件冒泡的形式冒泡到document上面，然后React将事件封装给正式的函数处理运行和处理    
如果DOM上绑定了过多的事件处理函数，整个页面响应以及内存占用可能都会受到影响。React为了避免这类DOM事件滥用，同时屏蔽底层不同浏览器之间的事件系统差异，实现了一个中间层——SyntheticEvent  
1.当用户在为onClick添加函数时，React并没有将Click时间绑定在DOM上面  
2.而是在document处监听所有支持的事件，当事件发生并冒泡至document处时，React将事件内容封装交给中间层SyntheticEvent（负责所有事件合成）  
3.所以当事件触发的时候，对使用统一的分发函数dispatchEvent将指定函数执行
###  合成事件绑定方式
< div onClick={this.handleClick}>点我呀！</>
### 原生事件
addEventListener 第三个参数 true代表句柄事件在捕获阶段实现，false冒泡阶段，
同一个对象节点上绑定多个事件，执行的顺序是怎样的？  
其他元素先发生捕获阶段事件 -> 本元素按照代码写的顺序执行 -> 其他元素最后发生冒泡阶段事件 
# React和Vue的比较
相同：virtual DOM 、组件化模块化开发  
不同：Vue:模板语法、MVVM，React：just View(只关心视图层)、偏向一切用js解决，推崇函数式编程和单向数据流，Vue与React的变化侦测方式有所不同  
### 什么是函数式编程？
http://www.ruanyifeng.com/blog/2012/04/functional_programming.html  
由于 JavaScript 支持高阶函数、匿名函数、**函数是一等公民**、闭包、解构（模式匹配）等特性，所以它也能支持函数式编程范式，事实上 JavaScript 是一门基于原型(prototype-based)的多范式语言 
不可变数据结构：js六大基本数据类型、惰性求值:比如一些数学题，我们可能一开始并不需要把所有表达式都求值，这样可以在计算的过程中将一些表达式消掉  
柯里化；**只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数**
````
var add = function(x) {
  return function(y) {
    return x + y;
  };
};

var increment = add(1);
var addTen = add(10);

increment(2);
// 3

addTen(2);
// 12
````
## Props和State的区别
props是一个父组件传递给子组件的数据流，可以一直的被传递到子孙组件中.然而state代表的是子组件自身的内部状态