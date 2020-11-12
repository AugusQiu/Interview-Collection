[掘金原文](https://juejin.im/post/6844903983987834888)
## AMD(RequireJS)
AMD是CommonJS规范的一个草案，异步模块加载，特点**依赖前置、提前执行，在define方法里传入的依赖模块(数组)，会在一开始就下载并执行**
## CMD(Seajs)
特点**依赖就近，延迟执行**，只有require的时候，依赖模块才执行
## CommonJS
node.js、webpack都是基于该规范来实现的
