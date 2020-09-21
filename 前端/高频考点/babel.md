* babel 让es6/7/8的新语法转化为 es5语法，能在低端环境运行  
* babel 总共分为三个阶段：解析，转换，生成  
* babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面
* babel 内部使用的解析类库叫做 babylon，并非 babel 自行开发
* babel-plugin-transform-runtime 的时候必须把 babel-runtime 当做依赖
##  state-x都是当年最新规范的草案
* Stage 0 - 稻草人: 只是一个想法，经过 TC39 成员提出即可。
* Stage 1 - 提案: 初步尝试。
* Stage 2 - 初稿: 完成初步规范。
* Stage 3 - 候选: 完成规范和浏览器初步实现。
* Stage 4 - 完成: 将被添加到下一年度发布
## babel-polyfill
babel 默认只转换 js 语法，而不转换新的 API，比如 Iterator、Generator、Set、Maps、Proxy、Reflect、SymbolPromise 等全局对象，以及一些定义在全局对象上的方法(比如 Object.assign)都不会转码  
缺点：
* 打包出来体积太大
* 污染全局变量