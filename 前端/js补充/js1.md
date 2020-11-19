## js的严格模式
"use strict":严格模式,js代码运行时会自动实行更加严格的解析和错误处理的方法  
注：IE6、7、8、9均不支持严格模式
一些常见的限制:https://blog.csdn.net/weixin_40387601/article/details/80514358  
## NaN是什么？
**NaN是一种特殊的数值**，用于表示一个本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误了），像其他语言，任何数值除以0都会抛出异常，代码会停止运行；而在ECMAScript中，任何数值除以0会返回NaN，不会影响其他代码运行
````
typeof(NaN); // 'number'
````
NaN的特点非常特殊，首先，任何涉及NaN的操作(NaN/10)都会返回 NaN；其次，**NaN与任何值都不相等，包括 NaN 本身**
````
console.log(NaN == NaN) //false
````
ECMAScript定义了**isNaN()** 函数,来判断参数是不是数值
````
alert(isNaN(NaN)); //true
alert(isNaN(10)); //false（10是一个数值）
alert(isNaN("10")); //false（可以被转换成数值10）
alert(isNaN("blue")); //true（不能转换成数值）
alert(isNaN(true)); //false（可以被转换成数值1）
````
## console.log(1+"2") 和 console.log(1-"2") 的打印结果？

````
console.log(1+"2") // 12
console.log(1-"2")  //-1
````
## 以下代码输出的结果是?
````
 var a = 1;  
  a+++a;  // 1 + 2 = 3
 typeof a+2; //number2
````
````
var d = (a = 3, b = 4)
console.log(d) //4，"a++"意思先参与运算，因为"a++"后无number，所以a++还是a，之后再+b
````
````
console.log(1+1);      //2，两个number，加号“+”起运算作用
console.log("2"+"4");  //24,两个string，加号“+”起连接作用
console.log(2+"4");    //24，存在一个string，加号“+”起连接作用
console.log(+"4");     //4，一个string，加号“+”前为空，加号“+”起连接作用
````
## instanceOf有什么作用？内部逻辑是如何实现的？
instanceOf 用于判断当前引用类型对象是不是某个构造函数的实例  
其实 instanceOf 内部逻辑就是判断 当前引用类型对象的proto 与 目标对象的prototype是否为同一个
## break与continue有什么区别？
break表示跳出大循环，continue表示结束本次循环
````
for(let i = 1;i<=3;i++){
    if(i == 2)
      continue;
    console.log(i); //1,3
}
````
## 写一个函数，返回参数的平方和？
````
    function sumOfSquares() {
      let sum = 0;
      for(let i of arguments){
          sum+=i*i;
      }
      return sum;
    }
    var result = sumOfSquares(2, 3, 4)
    var result2 = sumOfSquares(1, 3)
    console.log(result) // 29
    console.log(result2) // 10
````
## 如下代码的输出
关于作用域和作用域链的常见例子判断输出：  
https://www.jianshu.com/p/fafa281e6d84
````
var x = 10;
bar() 
function bar() {
    var x = 30;
   function foo() {
      console.log(x) 
   }
   foo();
}  //30
````
````
var x = 10
bar() 
function foo() {
  console.log(x)
}
function bar() {
  var x = 30
  foo()
}  //10,调用foo,进入foo的执行上下文，foo没有活动对象，只能按照作用域链去找上一级，即全局作用域，x=10
````
**总结经验**：
````
比较上面的两个例子，它们各自所谓的执行上下文是什么情况？
首先，明确一点：什么时候声明一个function,什么时候创建作用域，注意是声明，而不是调用的时候
第一个例子：
在全局作用域中声明了bar()函数，而foo()的声明是在bar()作用域里面的
第二个例子，bar()和foo()都是声明在全局里的，所以foo()里面没有x，它会沿着作用域链向上级搜索，foo()的上级就是全局，全局里的x等于10
````
## 如下代码的输出
````
var a = 1
function fn1() {
    function fn3() {
       function fn2() {
          console.log(a)
        }
       fn2()
       var a = 4
    }
    var a = 2
    return fn3
}
var fn = fn1()
fn() //undefined
````
````
var a = 1
function fn1() {
   function fn2() {
      console.log(a)
   }
   function fn3() {
      var a = 4
      fn2()
    }
   var a = 2
   return fn3
}
var fn = fn1() //2,fn2()里没a,上级fn1()里找，调用fn2()的时候，fn1作用域下a已经声明为2
````
````
var a = 1
function fn1() {
   function fn3() {
      var a = 4
      fn2()
   }
      var a = 2
      return fn3
}
   
function fn2() {
    console.log(a)
}
    
var fn = fn1()
fn() // 1,fn1()的上级是全局作用域,a=1
````
## 如下代码的输出
````
var a = 1
var c = {name: "oli", age: 2}

function f1(n) {
   ++n
}
function f2(obj) {
   ++obj.age
}

f1(a) 
f2(c) 
f1(c.age) 
console.log(a) //1
console.log(c) //{name: "oli", age: 3}
````
````
var obj1 = {a:1, b:2};
var obj2 = {a:1, b:2};
console.log(obj1 == obj2); //false
console.log(obj1 = obj2);
console.log(obj1 == obj2); //true
````
