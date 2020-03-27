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
## Proxy原理
Proxy是构造函数，第一个参数target指向被拦截的对象，第二个参数handler是句柄(定义操作方法的一个集合),返回的是一个新代理后的对象 
## WeakMap和Map的性能有什么差别?
* weakMapZ只接受对象作为键名(null)
* **键名所引用的对象都是弱引用，不计入垃圾回收机制**
WeakMap 应用的典型场合就是 DOM 节点作为键名
### Map跟对象的区别
* **Object 键只能是 String 或者 Symbol，但 Map 键可以是任意值，包括函数、对象、基本类型**
* Map 中的键值是有序的（按插入顺序），而添加到对象中的键则不是
* 通过 size 属性可直接获取一个 Map 的键值对个数，而 Object 的键值对个数只能手动计算
* **Map 可直接进行迭代**，而 Object 的迭代需要先获取它的键数组，然后再进行迭代
* Map 在涉及频繁增删键值对的场景下会有些性能优势
## weakSet与set
WeakSet 结构与 Set 类似，也是不重复的值的集合
* **WeakSet 中的元素只能是对象**，不能是其他类型的值
* WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果该对象不再被其他变量引用，那么垃圾回收机制就会自动回收该对象所占用内存
* WeakSet 不可遍历
##  ESModule 对于 Tree-Shaking 有什么优势
tree shaking 是一个术语，通常用于描述移除 JavaScript的死代码，它依赖于 ES2015 **模块语法的 静态结构 特性**，例如 import 和 export
##  ES 最新的语法，越新越好
https://juejin.im/post/5ca2e1935188254416288eb2  
* ECMAScript 2018中扩展运算符增加了对对象的支持
````
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };

var clonedObj = { ...obj1 };
// 克隆后的对象: { foo: "bar", x: 42 }

var mergedObj = { ...obj1, ...obj2 };
// 合并后的对象: { foo: "baz", x: 42, y: 13 }
````
* 数组includes()方法，用来判断一个数组是否包含一个指定的值
* startwith、endwith 字符串方法
* SharedArrayBuffer对象：与ArrayBuffer相似，用来表示一个通用的，固定长度的原始二进制数据缓冲区,不过SharedArrayBuffer**不能被分离**
* Promise.finally()：在某些情况下，你想要在无论Promise运行成功还是失败，运行相同的代码，例如清除，删除对话，关闭数据库连接等
* ES10(2019):String去首尾空字符:trimStart()、trimEnd()
* Object.fromEntries() 
````
//将 Array 转化为 Object
const arr = [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ];
const obj = Object.fromEntries(arr);
console.log(obj); // { 0: "a", 1: "b", 2: "c" }
````