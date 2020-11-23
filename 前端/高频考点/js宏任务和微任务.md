https://juejin.im/post/6844903638238756878
````
// 下面示例的打印顺序：1、2、3、4
setTimeout(_ => console.log(4)) //异步

new Promise(resolve => {
  resolve()
  console.log(1) //同步
}).then(_ => {
  console.log(3) //异步
})

console.log(2)   //同步
````
解析：
> setTimout是一个典型的宏任务，Promise.then则是一个典型的微任务  
**Promise在实例化的过程中执行的代码是同步**的，在then中注册的回调才是异步执行  
同步代码执行完成之后才会去检查是否有异步任务完成，若有就执行对应的回调  

async/await变形
````
setTimeout(_ => console.log(4))

async function main() {
  console.log(1)
  await Promise.resolve()
  console.log(3)
}

main()
console.log(2)
````
上面打印依旧是：1、2、3、4  
async/await其实就是promise的语法糖，async函数在await之前的代码都是同步执行的，可以理解为await之前的代码属于new Promise时传入的代码，await之后的所有代码都是在Promise.then中的回调
````
async function aysnc1(){
    console.log(2)
    await console.log(3)
    console.log(6)
}

console.log(1)
aysnc1()
new Promise((resolve)=>{
   console.log(4)
   resolve()
}).then(()=>{
   console.log(7)
})
console.log(5)
````
><font color="red">上述代码输出：1、2、3、4、5、6、7，await之前包含await是同步的</font>

````
async function aysnc1(){
    console.log(2)
    await new Promise((resolve)=>{
       console.log(3)
       resolve()
    }).then(()=>{
        console.log(8)
     })
    console.log(6)
}

console.log(1)
aysnc1()
new Promise((resolve)=>{
   console.log(4)
   resolve()
}).then(()=>{
   console.log(7)
})
console.log(5)
````
><font color="red">上述代码输出：1、2、3、4、5、8、7、6</font>

````
async function aysnc1(){
   console.log(2)
   await new Promise((resolve,reject)=>{
      console.log(3)
      //没有resolve或者reject()没有被catch，6就不会被打印
   })
   console.log(6)
}

console.log(1)
aysnc1()
new Promise((resolve)=>{
   console.log(4)
   resolve()
}).then(()=>{
   console.log(7)
})
console.log(5)
````
><font color="red">上述代码输出：1、2、3、4、5、7 没有6!!!</font>
## 重点
微任务是宏任务的一个步骤，所以应当是一个宏任务先执行，然后把**当前所有的微任务逐条执行**，再执行下一个宏任务，重复这个loop,而**script标签是第一个要执行的宏任务**，所以上面示例中的promise.then会先于setTimeout执行
````
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
上面这个复杂示例执行顺序：1、7、6、8、2、4、3、5、9、11、10、12
````
````
setImmediate(function(){
    console.log(1)
},0)
setTimeout(function(){
    console.log(2)
},2)
new Promise(function(resolve){
    console.log(3)
    resolve()
    console.log(4)
}).then(function(){
    console.log(5)
})
console.log(6)
process.nextTick(function(){
    console.log(7)
})
console.log(8)
// 输出：3 4 6 8 7 5 2 1
````
````
<div id="div">
  begin
</div>
setTimeout(function() {
    alert(' ui 已经渲染完毕了吗？ ');
    console.log('timeout1');
    new Promise(function(resolve) {
        console.log('promise3');
        resolve();
        console.log('promise4');
    }).then(function() {
        alert(' 为什么这里ui已经渲染成finally了 ');
        console.log('then2');
    })
    document.getElementById('div').innerHTML = 'finally';
})

new Promise(function(resolve) {
    console.log('promise1');
    resolve();
    console.log('promise2');
}).then(function() {
    console.log('then1');
    alert(' ui 开始渲染? ');
    console.log(document.getElementById('div').innerHTML)
    alert(' ui 开始渲染 ');
})

console.log('global1');
document.getElementById('div').innerHTML = 'end';
console.log(document.getElementById('div').innerHTML)
````
输出：promise1 -> promise2 -> global1 -> end -> then1 -> ui 开始渲染? -> end -> ui 开始渲染 -> ui 已经渲染完毕了吗？ -> timeout1 -> promise3 -> promise4 -> 为什么这里ui已经渲染成finally了 -> then2
## 宏任务、微任务分类
执行环境区分浏览器和node
### 浏览器、node相同点
宏任务：整体script代码、I/O、setTimeout、setInterval  
微任务：Promise.then catch finally  
### 浏览器特有
宏任务: requestAnimationFrame  
微任务: MutationObserver
### node特有
宏任务: setImmediate  
微任务: process.nextTick
## js单线程
所谓单线程，是指在js引擎中负责解释和执行js代码只有一个，即**主线程**  
但是实际上还存在其他的线程，比如：处理ajax的线程、处理DOM事件的线程、定时器线程、读写文件(I/O)等等，这些线程可能存在于js引擎之内，也可能存在于js引擎之外，可归类于**工作线程**
### 异步过程要素
>主线程发起一个异步请求，相应的工作线程接收请求并告知主线程，已经收到  
主线程继续执行后面的代码，同时工作线程执行异步任务  
工作线程完成任务后，通知主线程，主线程再去执行异步任务对应的回调函数

异步任务常见形式：
````
A(args...,callbackFn)

譬如：fs.readFile('demo.txt','utf-8', (err,data)=>{
    ....
})
````
一个异步过程包括两个要素：
* 发起函数（注册函数） 即 A
* 回调函数 callbackFn
