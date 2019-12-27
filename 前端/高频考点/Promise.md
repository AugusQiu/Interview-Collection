# ES6 Promise
ES6的Promise是用来帮助js实现异步编程的,避免回调地狱(回调函数多层嵌套)
## js异步
JS语言的执行环境是**单线程**的，一次只能执行一次任务，如果有多个任务就得排队(前面一个任务完成，才能去执行后面一个)。那么只要有一个任务耗时很长，就会拖延整个程序的执行，浏览器会无响应，导致页面卡死   
为了解决这个问题，js将任务的执行模式分为两种:**同步(Synchronous)**和**异步(Asynchronous)**  
异步模式:每一个任务有一个或者多个回调函数，前一个任务结束后，不是执行后一个任务，而是执行回调函数(callback);后一个任务也不等前一个是否回调完，就开始执行。在浏览器端，耗时很长的操作就应该异步执行,避免浏览器失去响应，像ajax  
### 常见的异步编程方法

1.回调函数(callback):a,b两个函数，b函数作为参数传到a函数里，即a(b)  
2.事件驱动:fn.on('done',f2)
3.发布-订阅模式    
4.Promise对象实现  

## Promise
ES6提供Promise构造函数，我们用它来new一个实例。Promise构造函数接收一个函数作为参数，这个传入的函数有两个参数，分别是两个函数 **resolve**和**reject**作用是，resolve将Promise的状态由未成功变为成功，将异步操作的结果作为参数传递过去；相似的是reject则将状态由未失败转变为失败，在异步操作失败时调用，将异步操作报出的错误作为参数传递过去。
````
let promise = new Promise((resolve, reject)=>{
    reject("拒绝了");
});
promise.then((data)=>{
    console.log('success' + data);
}, (error)=>{
    console.log(error)
});

执行结果："拒绝了"
````
### Promise的特点
* 对象不受外界影响，初始状态为pending(等待中)，结果状态为resolve或reject,只有异步操作的结果决定这一状态
* 状态只能由pending变为resolve或者reject这两种的一种，且改变后不可逆也不可再度修改
````
let promise = new Promise((resolve, reject)=>{
    reject("拒绝了");
    resolve("又通过了");
});
promise.then((data)=>{
    console.log('success' + data);
}, (error)=>{
    console.log(error)
});

执行结果： "拒绝了" 不会再执行resolve的方法
````
### then方法的规则
* then方法下一次的输入需要上一次的输出  
* 如果一个promise执行完后 返回的还是一个promise，会把这个promise 的执行结果，传递给下一次then中
* 如果then中返回的不是Promise对象而是一个普通值，则会将这个结果作为下次then的成功的结果
* 如果当前then中失败了 会走下一个then的失败
* 如果返回的是undefined 不管当前是成功还是失败 都会走下一次的成功
* catch是错误没有处理的情况下才会走
* then中不写方法则值会穿透，传入下一个then中