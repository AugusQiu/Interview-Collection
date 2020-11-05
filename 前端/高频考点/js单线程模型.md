[浏览器进程线程、任务队列和async/await](https://zhuanlan.zhihu.com/p/146039365)
# js单线程模型
js同时只能执行一个任务，注意，js只在一个线程上运行，不代表js引擎只有一个线程，**事实上,js引擎有多个线程，单个脚本(.js)只能在一个线程上运行,其它线程都在后台配合**  
为什么js采用单线程，而不是多线程？js从诞生之初就是单线程，原因是不想让浏览器变得太复杂，因为**多线程需要共享资源，且有可能修改彼此的运行结果**，对于一种网页脚本语言来说，这太复杂了  
例如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？所以，为了避免复杂性，从一诞生，JavaScript就是单线程，这已经成了这门语言的核心特征，将来也不会改变

为了利用多核CPU的计算能力，HTML5提出了**Web Worker**标准，允许js脚本创建多个线程，但是**子线程完全受主线程控制，且不得操作DOM**,所以这个新标准并没有改变js单线程的本质

单线程模型的缺点，自然就是新的任务追加在队列的尾部，但是只有等前面的任务完成，才会进行下一个。如果有一个任务特别耗时，那么后面的任务就得停在那儿等待，造成卡死  
如果排队是因为计算量大，CPU忙不过来，倒也算了，可是很大时候CPU是闲着的，原先，CPU得先等IO设备的结果出来，比如等待ajax请求网络数据，不出来，CPU就无法往后执行计算。后来，js的设计者作了改进，**CPU完全可以不管IO设备，挂起处于等待中的任务，等到IO设备返回了结果，再回头，把挂起的任务继续执行下去**，这就是js内部采用的**Event Loop**机制
## 消息队列
js运行时，除了一个运行线程，引擎还提供一个消息队列，队列里放的是各种需要当前程序处理的消息，新的消息进入队列，会自动排在队尾  
运行线程只要发现消息队列不为空，就会取出排在第一位的消息，执行它对应的回调函数，等到执行完，再取出本来排在第二位的消息，不断循环，直到消息队列为空  

每条消息与一个回调函数相联系，即程序只要收到这条消息，就会执行对应的函数。**进入消息队列的消息，必须有对应的回调函数，否则这个消息就会遗失**。比如，鼠标点击就会产生一条消息，报告click事件发生了，如果没有回调函数，这个消息就会遗失。有回调，这个消息就进入消息队列，等到程序的接收和执行相应的回调函数  

另一种情况是**setTimeout会在指定时间往消息队列添加消息**，如果队列中，此时没有其它消息，该条消息会立即执行，否则这条消息不得不先等其它消息处理完，因此**setTimeout指定的执行时间，只是一个最早可能发生的时间，并不能保证一定会在那个指定时间点发生**
## Event Loop
所谓Event loop机制，指的是一种内部循环，用来一轮又一轮地处理消息队列之中的消息，即执行对应的回调函数  
Wikipedia的定义是：“Event Loop是一个程序结构，用于等待和发送消息和事件”，**可以就把Event Loop理解成动态更新的消息队列本身**  
常见的js任务有：
* 执行js代码
* 对用户的输入(鼠标点击、键盘输入等等)作出反应
* 处理异步的网络请求

所有任务可以分成两种，一种是同步任务(synchronous)，另一种是异步任务(asynchronous).  
* 同步任务:在JavaScript主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务  
* 异步任务:不进入JavaScript执行线程、而进入“任务队列”(task queue)的任务，只有“任务队列”通知主线程，某个异步任务可以执行了，该任务(采用回调函数的形式)才会进入JavaScript线程执行

虽然JavaScript只有一个线程用来执行，但是并行的还有其他线程(比如，处理定时器的线程、处理用户输入的线程、处理网络通信的线程等等)这些线程通过向任务队列添加任务，实现与JavaScript线程通信.
## 浏览器和node的事件循环的区别
https://www.imooc.com/article/79674
### 任务类型的区别
**浏览器环境下的异步任务就分为宏任务和微任务**
* 宏任务：script代码、setTimeout、setInterval、I/O、UI render
* 微任务：Promise的then和catch, MutationObserver
><font color="red">注意点</font>：每个script中的代码是一个独立的task, 即会执行完前面的script中创建的 microTask后，再执行后面的script中的同步代码  

**node环境的任务类型** 
* 微任务
* process.nextTick
* timers:setTimeout、setInterval
* I/O callbacks:是否有已完成的 I/O 操作的回调函数，来自上一轮的 poll 残留
* poll:等待还没完成的 I/O 事件，会因 timers 和超时时间等结束等待
* check:执行 setImmediate 的回调
* close callbacks:关闭所有的 closing handles ，一些 onclose 事件

简而言之，node的宏任务队列:**Timers Queue、I/O Queue、Check Queue 和 Close Queue**  

循环中进行的操作：
* 清空当前循环内的 Timers Queue，清空 NextTick Queue，清空 Microtask Queue；

* 清空当前循环内的 I/O Queue，清空 NextTick Queue，清空 Microtask Queue；

* 清空当前循环内的 Check Queue，清空 NextTick Queue，清空 Microtask Queue；

* 清空当前循环内的 Close Queue，清空 NextTick Queue，清空 Microtask Queue；

进入下轮循环。
````
//浏览器环境
while(true){
   macroTaskQueue.shift()
   microTaskQueue清空
}

//node环境
while (true) {
    loop.forEach((阶段) => {
        当前阶段全部任务();
        nextTick全部任务();
        microTask全部任务();
    });
    loop = loop.next;
}
````