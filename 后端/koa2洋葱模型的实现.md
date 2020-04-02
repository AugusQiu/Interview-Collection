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
## 简单实现
````
const app = {
    middlewares:[],
    //use方法模拟使用中间件
    use(fn){
      this.middlewares.push(fn)
    }
}

//compose：串联每个函数，使用下标递增，并递归至最后一个函数结束
app.compose = function(middlewares){
    return async function(){
      await dispath(0);
      async function dispath(idx){
         if (idx === app.middlewares.length) return;
         const fn = middlewares[idx];
         await fn(function next() {
                
                dispath(idx + 1);
         });
      }
    }
}


app.use(function (next) {
    console.log(1);
    next();
    console.log(1.1)
})
app.use(function (next) {
    console.log(2);
    next();
    console.log(2.2);

})
app.use(function (next) {
    console.log(3);
    next();
    console.log(3.3);
});
app.compose()(); 
````

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