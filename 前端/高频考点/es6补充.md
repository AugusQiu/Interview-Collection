## 反引号 ` 标识?
ES6 模板字符串(Template String)是增强版的字符串，用反引号(`)标识，它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量
## 使用 ES6 改下面的模板？
````
let iam  = "我是";
let name = "Oli";
let str  = "大家好，" + iam + name + "，多指教。";

let str = `大家好，${iam + name},多指教。`;
````
##  ES6/7/8新增字符串方法
模板字符串、includes()、startsWith()、endsWith()、String.raw(转义)
##  call、apply 以及 bind 函数内部实现是怎么样的？
https://juejin.im/post/5c73a602e51d457fd6235f66
## 使用解构，实现两个变量的值的交换？
````
[a,b] = [b,a];
````
## 解构赋值？
基本原则：
* 数组的元素是按次序排列的，变量的取值由它的位置决定
````
let [x, y]= [1, 2];
````
* 对象的属性没有次序，变量必须与属性同名，才能取到正确的值
````
let { foo , bar } = { foo: "aaa", bar: "bbb" };
````
## 默认参数值
undefined
## ES6中的TDZ（暂时性死区）
let/const会形成封闭的块级作用域，不存在像var一样的变量提升，**使用let命令声明变量之前，该变量都是不可用的，会报错**，这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）

## Promise 有几种状态？Promise 的特点是什么，分别有什么优缺点？
三种状态：pending(等待中)、resolved(完成了)、rejected(拒绝了)  
特点：
* 一旦从等待状态变成为其他状态就永远不能更改状态了
* 链式调用，也就是说每次调用then之后返回的都是一个Promise，并且是一个全新的Promise，原因也是因为状态不可变。如果你在then中 使用了return，那么return的值会被Promise.resolve()包装  

优缺点：优：解决回调地狱 缺：无法取消Promise，错误需要通过回调函数捕获
## Promise 构造函数是同步还是异步执行？then 呢？Promise 如何实现 then 处理？
构造函数是同步立即执行，then异步
````
//简易版Promise
function Promise(fn) {
  var value = null;
  var callbacks = [];
  this.then = function (onFulfilled) {
    callbacks.push(onFulfilled);
  } 

  function resolve (value) {
    callbacks.forEach((callback) => {
      callback(value);
   })
  fn(resolve); 
  }
}
````
https://www.jianshu.com/p/23f7ab7ae6e6
##  Promise 和 setTimeout 的区别？
https://juejin.im/post/5b7057b251882561381e69bf  
MacroTask Queue(宏任务队列)：setTimeout, setInterval, setImmediate, requestAnimationFrame, UI rendeing, NodeJS中的`I/O等  
MicroTask Queue(微任务队列)：  
* 独立回调microTask:Promise,其成功／失败回调函数相互独立
* 复合回调microTask:Object.observe,MutationObserver 和NodeJs中的 process.nextTick ，不同状态回调在同一函数体
## Process.nextTick 和 setImmediate 的区别？
https://www.zhihu.com/question/23028843
## 如何实现 Promise.all() ？
* 有一个接收一个 Promise 实例的数组
* 如果元素不是 Promise 对象，则使用 Promise.resolve 转成 Promise 对象
* 如果全部成功，状态变为 resolved，返回值将组成一个数组传给回调
* 只要有一个失败，状态就变为 rejected，返回值将直接传递给回调
all() 的返回值也是新的 Promise 对象
````
function promiseAll(promises) {
  return new Promise(function(resolve, reject) {
    if (!Array.isArray(promises)) {
      return reject(new TypeError('arguments must be an array'));
    }
    var resolvedCounter = 0;
    var promiseNum = promises.length;
    var resolvedValues = new Array(promiseNum);
    for (let i = 0; i < promiseNum; i++) {
        Promise.resolve(promises[i]).then(function(value) {
          resolvedCounter++
          resolvedValues[i] = value
          if (resolvedCounter == promiseNum) {
            return resolve(resolvedValues)
          }
        }, function(reason) {
          return reject(reason)
        })
    }
  })
}
````
## 如何实现 Promise.prototype.finally() ？
finally() 方法返回一个Promise，在promise结束时，无论结果是fulfilled或者是rejected，都会执行指定的回调函数，来进行一些你想要的处理
````
Promise.prototype.finally = function(onFinally) {
  return this.then(
    /* onFulfilled */
    res => Promise.resolve(onFinally()).then(() => res),
    /* onRejected */
    err => Promise.resolve(onFinally()).then(() => { throw err; })
  );
};
````