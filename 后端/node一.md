## 一.express的路由机制(从一个中间件到下一个中间件)
参考文章:https://cnodejs.org/topic/545720506537f4d52c414d87  
路由：客户端发请求到服务器，服务器进行处理的过程,即如何定位到服务端处理程序的过程  
url是不是指向文件是无所谓的，但最终都要**根据其定位到某个具体的处理程序**,也就是url到handler有个路由处理过程，只不过不同的框架有不同的处理方法.  
express框架的路由过程可以简单如下图表示：
<img src="https://upload-images.jianshu.io/upload_images/1864457-2e50b055a2302739.png?imageMogr2/auto-orient/strip|imageView2/2/w/573/format/webp" width="500px" height="250px"/>

## 二.express和koa2或者egg的区别
首先egg是基于koa开发的,所有egg会继承koa2对比express的所有优点

1.koa2更加轻量  
Express是一个**集合式**的框架，自身集成了router，static，views，bodyparse等等常用中间件，这样的设计让开发者能非常快速的搭建一个node后端服务。但是不管你有没有用到这些，都得引入  
Koa2**插件化**设计。只提供最基础的框架，所有功能都通过中间件引入。Koa将Express集成的中间件进行拆分，开发者按需引入即可使用  

2.Koa2能完美的支持ES6  
express是基于ES5开发的，受限制于当时的JavaScript标准，很多功能的实现方式不是很好。比如异步执行只能采用回调函数的方式  
Koa2是基于ES7（ES2016）开发的，原生支持异步函数 async/await，使用Promise作为异步处理的标准，支持箭头函数等ES6新语法

3.中间件执行机制  
express的中间件时**线性执行**的，每一个中间件处理完后只有两个选择：交给下一个中间件或者返回Response.只要请求离开了，下一个中间件就无法再次获得这个请求，更不能再对它进行处理  
Koa的中间件机制是**洋葱模型**，中间件像一层层的洋葱皮。请求要穿过洋葱，每个中间件就会被穿过两次
````
const Koa = require('koa');

const app = new Koa();
const PORT = 3000;

//#1
app.use(async (ctx, next)=>{
    console.log(1)
    await next();
    console.log(1)
});
//#2
app.use(async (ctx, next) => {
    console.log(2)
    await next();
    console.log(2)
})

app.use(async (ctx, next) => {
    console.log(3)
})

app.listen(PORT);
console.log(`http://localhost:${PORT}`);
````
访问http://localhost:3000，控制台打印：1 2 3 2 1 .当程序运行到await next()的时候就会暂停当前程序，进入下一个中间件，处理完之后才会回调过头来继续处理
## node的优缺点？
https://blog.csdn.net/u010775025/article/details/93861817
特点:**异步、事件驱动模型**，举例：很多人去餐厅吃饭，一个人点完餐后拿到了一个号码，拿到号码，就会在位置上等待，做自己的事情，而后面人的请求也会继续得到处理，同样是拿了一个号码然后到一旁等待，接待员能一直进行处理。等到饭菜做好了，会喊号码，我们拿到了自己的饭菜，进行后续的处理（吃饭）。这个喊号码的动作在NodeJS中叫做回调（Callback），能在事件（烧菜，I/O）处理完成后继续执行后面的逻辑（吃饭），这体现了NodeJS的显著特点，异步机制、事件驱动整个过程没有阻塞新用户的连接（点餐），也不需要维护已经点餐的用户与厨师的连接  
因此NodeJS能支持比Java、PHP程序更高的并发量，虽然维护事件队列也需要成本，再由于NodeJS是单线程，事件队列越长，得到响应的时间就越长，并发量上去还是会力不从心  
**非阻塞I/O**:NodeJS遇到I/O事件会创建一个线程去执行，然后主线程会继续往下执行的
**适合高并发、I/O密集型应用，不适合CPU密集型应用，由于JavaScript单线程的原因，如果有长时间运行的计算(比如大循环)，将会导致CPU时间片不能释放，使得后续I/O无法发起**