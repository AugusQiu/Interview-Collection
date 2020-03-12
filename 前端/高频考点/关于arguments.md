arguments保存着函数调用时传递过来的所有参数、下标从0开始顺序接收    
arguments对象不是一个Array、它类似于Array，但不具有除了length方法的任何方法，例如 sort方法、pop方法等
但它可以被转换成一个真正的Array
````
var arr = Array.prototype.slice.call(arguments);
````
## callee介绍
callee是arguments对象的一个属性、用来指向当前执行的函数
````
function fun() {
    console.log(arguments.callee);
}
fun();
````
## caller介绍
caller是function对象的一个属性用于返回一个function引用、**它返回调用它的function对象**、若直接在全局环境下调用fun1 则返回null、在fun2里调用之后返回fun2
````
function fun1(){
  console.log(arguments.callee.caller);
}
function fun2(){
    fun1();
}
fun1();
fun2();
````

