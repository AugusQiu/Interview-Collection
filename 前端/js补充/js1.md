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
