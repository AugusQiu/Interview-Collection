koa2是**用compose来串联中间件**，实现洋葱模型
## 经典案例
其实如果没有用Promise优雅地处理，这就是个嵌套的回调，外部等内部函数先执行完
````
const Koa = require("koa");
const app = new Koa();

app.use(async(ctx, next) => {
    console.log(1);
    await next();
    console.log(2);
});

app.use(async (ctx, next) => {
    console.log(3);
    await next();
    console.log(4);
});

app.use(async(ctx, next) => {
    console.log(5);
    await next();
    console.log(6);
});

app.listen(3000);

// 1
// 3
// 5
// 6
// 4
// 2
````
第一个中间件函数中如果执行了 next，则下一个中间件会被执行，依次类推
## 准备工作
先创建一个app对象暂时替代Koa的实例对象，并添加use方法和管理中间件的数组middlewares
````
/app.js

const app ={
    middlewares:[]
}

app.use = function(fn){
    app.middlewares.push(fn)
}

//app compose...
module.exports = app;
````
## compose 同步实现
Koa源码中是通过koa-compose中间件来实现的，这里我们将模块的核心逻辑抽取出来，由于重点compose的原理，所以ctx参数去掉，重点观察next
````
app.compose = function(){
    //递归函数
    function dispatch(index){
        //如果所有中间件都执行完了就跳出
        if(index===app.middlewares.length) return;
        
        //取出第index个中间件并执行
        const route = app.middlewares[index];
        return route(() => dispatch(index + 1));

    }
    // 取出第一个中间件函数执行
    dispatch(0);
}
````
上面是同步的实现，通过递归函数 dispatch 的执行取出了数组中的第一个中间件函数并执行，在执行时传入了一个函数，并递归执行了 dispatch，传入的参数 +1，这样就执行了下一个中间件函数，依次类推，直到所有中间件都执行完毕，不满足中间件执行条件时，会跳出
## 升级为异步支持
````
app.compose = function(){
    //递归函数
    function dispatch(index){
        //如果所有中间件都执行完就跳出，并返回一个Promise
        if(index === app.middlewares.length) return Promise.resolve();

        //取出第index个中间件并执行
        const route = app.middlewares[index];

        // 执行后返回成功态的 Promise
        return Promise.resolve(route(() => dispatch(index + 1)));
    }
    
    //取出第一个中间件函数执行
    dispatch(0);
}
````
async函数中await后面执行的异步代码要先阻塞等待，等异步执行完成后，才会继续向下执行，需要等待 Promise，所以我们将每一个中间件函数在调用时最后都返回了一个成功态的 Promise

## PS:Koa2源码分析
````
function compose (middleware) {
  return function (context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0)
    
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, function next () {
          return dispatch(i + 1)
        }))
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
````
比较关键的就是这个dispatch函数了，它将遍历整个middleware，然后将context和dispatch(i + 1)传给middleware中的方法
````
return Promise.resolve(fn(context, function next () {
      return dispatch(i + 1)
}))
````
这段代码就很巧妙的实现了两点:
* 将`context`一路传下去给中间件
* 将`middleware`中的下一个中间件`fn`作为未来`next`的返回值