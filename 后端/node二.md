https://cloud.tencent.com/developer/article/1514668
## node里的模块是什么
在node中，每个文件模块都是一个对象，它的定义如下
````
function Module(id, parent) {
  this.id = id;
  this.exports = {};
  this.parent = parent;
  this.filename = null;
  this.loaded = false;
  this.children = [];
}

module.exports = Module;

var module = new Module(filename, parent);
````
**所有的模块都是 Module 的实例**,可以看到，当前模块（module.js）也是 Module 的一个实例
## require的模块加载机制
* 计算模块路径
* 如果模块在缓存里面，就取出缓存
* 加载模块
* 输出模块的exports属性即可
## 加载模块时，为什么每个模块都有__dirname,__filename属性呢，new Module的时候我们看到是没有这两个属性的，那么这两个属性是从哪里来的
每个module里面都会传入__filename, __dirname参数，这两个参数并不是module本身就有的，是外界传入的
## node导出模块有两种方式，一种是exports.xxx=xxx和Module.exports={}有什么区别吗
exports其实就是module.exports
## 介绍一下node事件循环(event loop)的过程
* 在进程启动时，Node会创建一个类似于while(true)的循环，每执行一次循环体的过程我们成为Tick
* 每个Tick的过程就是查看任务队列中是否有事件待处理。如果有就取出事件及其相关的回调函数
* 然后进入下一个循环，如果不再有事件处理，就退出进程
## 每个tick的过程，如何判断是否有事件需要处理呢？
* 每个事件循环中有一个或多个观察者，判断是否有事件需要处理，也就是向这些观察者询问是否有要处理的事件
* 在Node中，事件主要来源于网络请求、文件的I/O等，这些事件对应的观察者有文件I/O观察者，网络I/O的观察者
* 事件循环是一个典型的生产者/消费者模型。异步I/O，网络请求等则是事件的生产者，源源不断为Node提供不同类型的事件，这些事件被传递到对应的观察者那里，事件循环则从观察者那里取出事件并处理
* 在windows下，这个循环基于IOCP(输入输出完成端口,是支持多个同时发生的异步I/O操作的应用程序编程接口)创建，在unix下则基于多线程创建
## 描述整个异步I/O的流程
### 异步调用阶段
````
发起调用->封装请求对象->设置参数和回调函数->将请求对象放入线程池等待执行
````
### 线程池
````
如果线程可用->执行请求对象中的I/O操作->将执行完成的结果放在请求对象中->通知IOCP调用完成->归还线程
````
### 事件循环
````
创建主循环 ->从I/O观察者取到可用的请求对象 -> 取出回调函数和结果调用执行 -> 线程池那儿获取完成的I/O交给I/O观察者
````
## 如何查看V8的内存使用情况
使用process.memoryUsage(),返回如下
````
{
  rss: 4935680,
  heapTotal: 1826816,
  heapUsed: 650472,
  external: 49879
}
````
heapTotal 和 heapUsed 代表V8的内存使用情况; external代表V8管理的，绑定到Javascript的C++对象的内存使用情况; rss, 驻留集大小, 是给这个进程分配了多少物理内存(占总分配内存的一部分)，这些物理内存中包含堆，栈，和代码段
## V8的内存分代和回收算法请简单讲一讲
在V8中，主要将内存分为新生代和老生代两代。**新生代中的对象存活时间较短的对象，老生代中的对象存活时间较长，或常驻内存的对象**
### 新生代
新生代中的对象主要通过**Scavenge算法**进行垃圾回收。这是一种采用复制的方式实现的垃圾回收算法。**它将堆内存一份为二**，每一部分空间成为semispace。在这两个semispace空间中，**只有一个处于使用中，另一个处于闲置状态**。处于使用状态的semispace空间称为From空间，处于闲置状态的空间称为To空间
* 当开始垃圾回收的时候，**会检查From空间中的存活对象，这些存活对象将被复制到To空间中，而非存活对象占用的空间将会被释放**。完成复制后，From空间和To空间发生角色对换
* 当一个对象经过多次复制依然存活，它将会被认为是生命周期较长的对象。这种新生代中生命周期较长的对象随后会被移到老生代中
### 老生代
老生代主要采取的是标记清除的垃圾回收算，在标记阶段遍历堆中的所有对象，并标记活着的对象，只清理死亡对象


## 内存泄漏是什么，以及常见内存泄漏的原因，和排查的方法
### 内存显露
内存泄漏(Memory Leak)指由于疏忽或错误，程序不用的内存未能及时释放  
如果内存泄漏的位置比较关键，那么随着处理的进行可能持有越来越多的无用内存，这些无用的内存变多会引起服务器响应速度变慢  
常见原因：闭包、全局变量、定时器、dom引用、同一个事件重复监听(addEventListener)  
### 定位内存泄漏
Google Chrome浏览器提供了非常强大的JS调试工具，**Heap Profiling**便是其中一个。Heap Profiling可以记录当前的堆内存（heap）快照，并生成对象的描述文件，该描述文件给出了当时JS运行所用到的所有对象，以及这些对象所占用的内存大小、引用的层级关系等等

## 新建Buffer会占用V8分配的内存吗
不会，Buffer属于堆外内存，不是V8分配的
## Buffer.alloc和Buffer.allocUnsafe的区别
Buffer.allocUnsafe创建的 Buffer 实例的底层内存是未初始化的。新创建的 Buffer 的内容是未知的，可能包含敏感数据。使用 Buffer.alloc() 可以创建以零初始化的 Buffer 实例
## Buffer的内存分配机制
为了高效的使用申请来的内存，Node采用了slab分配机制。slab是一种动态的内存管理机制。**Node以8kb为界限来来区分Buffer为大对象还是小对象**，如果是小于8kb就是小Buffer，大于8kb就是大Buffer  
例如第一次分配一个1024字节的Buffer，Buffer.alloc(1024),那么这次分配就会用到一个slab，接着如果继续Buffer.alloc(1024),那么上一次用的slab的空间还没有用完，因为总共是8kb，1024+1024 = 2048个字节，没有8kb，所以就继续用这个slab给Buffer分配空间
如果超过8bk，那么直接用C++底层的SlowBuffer来给Buffer对象提供空间
## Buffer乱码问题
一般情况下，只需要设置rs.setEncoding('utf8')即可解决乱码问题
## webSocket与传统的http有什么优势
* 客户端与服务器之间只需要一个TCP连接，比http长轮询使用更少的连接
* webSocket服务端可以主动推送数据到客户端
* 更轻量的协议头
## webSocket协议升级时什么，能简述一下吗？
WebSocket依赖于HTTP协议，Upgrade首部用来把当前的HTTP请求升级到WebSocket协议
## 该请求和普通的HTTP请求有几点不同：
* GET请求的地址不是类似/path/，而是以ws://开头的地址
* 请求头Upgrade: websocket和Connection: Upgrade表示这个连接将要被转换为WebSocket连接
* Sec-WebSocket-Key是用于标识这个连接，并非用于加密数据
* Sec-WebSocket-Version指定了WebSocket的协议版本
## 简述一下node的多进程架构
面对node单线程对多核CPU使用不足的情况，Node提供了child_process模块，来实现进程的复制，node的多进程架构是主从(Master-Worker)模式  
主进程 fork(复制)多个工作进程，工作进程受主进程控制
````
var fork = require('child_process').fork;
var cpus = require('os').cpus();
for(var i = 0; i < cpus.length; i++){
    fork('./worker.js');
}
````
## 宏任务和微任务
微任务和宏任务皆为异步任务，它们都属于一个队列，主要区别在于他们的执行顺序，Event Loop的走向和取值  
区别:
* 宏任务一般是：包括整体代码script，setTimeout，setInterval、setImmediate
* 微任务：原生Promise(有些实现的promise将then方法放到了宏任务中)、process.nextTick、Object.observe(已废弃)、 MutationObserver记住就行了
## Cluster/libuv/pm2
* cluster:fork多个工作子进程，子进程受主进程控制
* libuv：用于实现进程间IPC通信，windows下是基于命名管道,应用层上体现为message事件和send()方法
* pm2实现进程守护：node app.js 开启一个服务进程之后，我还可以在这个终端上做些别的事情，且不会相互影响。当出现问题可以自动重启