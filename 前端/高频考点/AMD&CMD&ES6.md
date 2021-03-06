[AMD CMD ES6](https://juejin.im/post/6844903983987834888)
[CommonJS VS ES6](https://www.cnblogs.com/unclekeith/archive/2017/10/17/7679503.html)
## AMD(RequireJS)
AMD是CommonJS规范的一个草案，**它异步模块加载，CommonJS是同步的**，特点**依赖前置、提前执行，在define方法里传入的依赖模块(数组)，会在一开始就下载并执行**
## CMD(Seajs)
特点**依赖就近，延迟执行**，只有require的时候，依赖模块才执行
## CommonJS
node.js、webpack都是基于该规范来实现的
* 基本数据类型，导出的是拷贝的值，会被缓存，重新赋值，原模块变量不变(两不相干)
* 引用类型，浅拷贝，两个模块引用的对象还是指向同一个内存空间，因此一个修改会影响到另一个模块
* 当使用require命令加载某个模块，就会运行整个模块的代码
* 当使用require命令加载同一个模块时，不会再执行该模块，而是取到缓存之中的值。也就是说，CommonJS模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存
* 循环加载时，属于加载时执行。即脚本代码在require的时候，就会全部执行。一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出
## ES6
* ES6模块中的值属于**动态只读引用**
* 对于只读来说，即不允许修改引入变量的值，import的变量是只读的，不论是基本数据类型还是复杂数据类型。当模块遇到import命令时，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值
* 对于动态来说，原始值发生变化，import加载的值也会发生变化。不论是基本数据类型还是复杂数据类型
* 循环加载时，ES6模块是动态引用。只要两个模块之间存在某个引用，代码就能够执行


### 对比CommonJS和ES6模块
````js
// CommonJS
let { start, exists } = require('fs')
/*
  let _fs = require('fs')
  let start = _fs.start，exists = _fs.exists
*/

// ES6
import { start, exists, readFile } from 'fs'
````
上述例子，CommonJS的实质是**整体加载**fs模块生成一个_fs对象，之后再从对象中分别读取3个方法，称为“运行时加载”，而ES6模块是加载3个方法，称为“编译时加载”
## Webpack Tree Shaking不会清除IIFE(立即调用函数表达式)
````js
// App.js
import { cube } from './utils.js'
console.log(cube(2))

// utils.js
var square = function(x){
   console.log('square')
}()

export function cube(x){
   console.log('cube')
   return x * x * x
}


// 打包之后的结果，square和cube都在
function(e,t,,n){
    "use strict"
    n.r(t)
    console.log("square")
    console.log(function(e){
        return console.log("cube"), e * e * e
    }(2))
}
````
js解释型语言，边解释边执行，IIFE立即执行很特殊  
### Webpack Tree shaking对于IIFE的返回函数，如果未使用会被清除
````js
//App.js
import { cube } from './utils.js';
console.log(cube(2));

//utils.js
var square = function(x) {
  console.log('square');
  return x * x; //这行返回，会被删除
}();

function getSquare() {
  console.log('getSquare');
  square();
}

export function cube(x) {
  console.log('cube');
  return x * x * x;
}

function(e, t, n) {
  "use strict";
  n.r(t);
  console.log("square");   <= square这个IIFE内部的代码还在
  console.log(function(e) {
    return console.log("cube"), e * e * e  <= square这个IIFEreturn的方法因为getSquare未被调用而被删除
  }(2))
}
````
### lodash-es 举例
很有趣的一件事
````js
// lodash只引入使用一个方法，webpack会将整个库打包进去，lodash-es是具备es6模块化的版本，就不存在这个问题了
import { cloneDeep } from 'lodash'
````

