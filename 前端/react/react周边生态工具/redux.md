## 基本介绍
Redux三大核心概念：
* Action：表示应用中的各类动作或操作，不同的操作会改变相应的state状态
* Reducer
* Store：存储数据的仓库，通过redux当中的createStore方法来创建一个store,它提供了3个主要方法:getState、dispatch、 subscribe
````
//reducer接收旧的state和action,返回新的state
const todos = (state = [],action)=>{
  //根据不同的action.type来对state进行不同的操作，一般都是用switch语句
  switch(action.type){
    case 'ADD_TODO':
      return [
          ...state,
          {
              id:action.id,
              text:action.text,
              completed:false
          }
      ];
      default:
         return state;
  }
}
````
## redux遵循的三个原则
* 单个事实来源：整个应用程序的状态存储在单个对象/状态树中
* 状态只读：改变状态的唯一方法就是触发一个动作（就像state是数据的最小表示一样，action是数据更改的最小表示）
* 使用纯函数进行更改
## redux的中间件
中间件以第三方插件的模式，自定义拦截action -> reducer的过程，变成了 action -> middlewares ->reducer,这种机制可以让我们改变数据流，实现如异步action、action过滤、日志输出、异常报告等功能  
常用中间件:  
* redux-logger:提供日志输出
* redux-thunk:处理异步操作
* redux-promise:处理异步操作，actionCreator的返回值是promise
## redux有什么缺点？
一个组件所需要的数据，必须由父组件传过来，而不能像flux那样直接从store取  
当一个子组件相关数据更新时，即使父组件不需要用到这个组件，父组件还是会重新render,这时需要重写shouldComponentUpdate进行判断
## redux有什么优点？
* 结果可预测：因为总是有一个真实的来源(store),不容易将当前状态与操作和程序的其他部分混淆
* 服务器端呈现：只需将在服务器上创建的存储传递到客户端，这对于初始渲染非常友好
* 易于测试和维护——redux的代码主要是小的、纯的和隔离的函数