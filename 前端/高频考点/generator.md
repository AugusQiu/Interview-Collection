http://www.ruanyifeng.com/blog/2015/04/generator.html  
*标识的function,最大特点：可以暂停执行  
返回内部指针(遍历器 g),这是Generator函数不同于普通函数的地方，执行它不会返回结构，而是指针对象  
g.next()移动内部指针，遇到yield暂停，调用next()方法，会返回一个对象，表示当前阶段的信息(value属性和done属性),**value属性是yield语句后面表达式的值，表示当前阶段的值，done属性是一个布尔值，表示Generator函数是否指向完毕，即是否还有下一阶段**  
Generator 函数可以**暂停执行**和**恢复执行**，这是它能封装异步任务的根本原因，除此之外，它还有两个特性：函数体内外的**数据交换**和**错误处理**机制  
next()方法不加参数，返回对象的value属性是函数向外输出数据；接受参数，向函数体内输入数据
````
function* gen(x){
  var y = yield x + 2;
  return y;
}

var g = gen(1);
g.next() // { value: 3, done: false }
g.next(2) // { value: 2, done: true }
````
上面代码中，第一个 next 方法的 value 属性，返回表达式 x + 2 的值（3）。第二个 next 方法带有参数2，这个参数可以传入 Generator 函数，作为上个阶段异步任务的返回结果，被函数体内的变量 y 接收。因此，这一步的 value 属性，返回的就是2（变量 y 的值）
````

function* gen(x){
  try {
    var y = yield x + 2;
  } catch (e){ 
    console.log(e);
  }
  return y;
}

var g = gen(1);
g.next();
g.throw（'出错了'）;
````
Generator 函数体外，使用指针对象的 throw 方法抛出的错误，可以被函数体内的 try ... catch 代码块捕获  
https://juejin.im/post/5b04c7db6fb9a07aa542a772