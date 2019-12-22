# js作用域
作用域，是指变量的生命周期(一个变量在哪些范围内保持一定值)  
1.全局作用域  
像我们声明的全局变量，它会直接被挂载到最顶层的window对象上  
````
<script>
 var val1 = 123; //显示声明,用了var关键字修饰
 console.log(window.val1)
 
 function foo(val){
   result = val +1; //函数内result变量没有用var修饰，它会被默认为全局变量
   return result;
 }
 foo(1);
 console.log(windows.result); //2
</script>
````
2.函数作用域  
函数内的部分，对外是封闭的，从外层的作用域无法直接访问函数内部
````
function bar(){
  var innerVal = 'inner';
}
console.log(innerVal); //报错:referenceError: innerVal is not defined
````
其实，我们可以**通过闭包来访问函数内部变量**，简单的了解一下
````
function bar(val){
    var innerVal = 'inner';
    var result = innerVal + val;
    return function out(){
        return result;
    }
}
console.log(bar('fun')) //"innerfun"
````
**立执行函数**:很多库都会用到它来分离全局作用域，形成一个单独的函数作用域,它会自动执行(function(){......})( )里面的内容
````
<script>
  (function(){
      var val = 123;
      var func= function(){ console.log('test');};
  })();
  console.log(window.val); //undefined
  console.log(window.func); //undefined
</script>
````
3.块级作用域  
在es6之前是没有块级作用域的，直到es6的let关键字出现
````
<script>
for(var i = 0; i < 5; i++) {
   ...
}
</script>
console.log(i)  	//5,用var关键字声明，在for循环后，for(){}没有起到像函数作用域一样的封闭效果

//换成let关键字(当然const也可以)
for(let i = 0; i < 5; i++) {
   ...
}
console.log(i)      // 报错：ReferenceError: i is not defined
````
举一个面试常见的例子:
````
for(var i = 0; i < 5; i++) {
  setTimeout(function() {
     console.log(i);			// 5 5 5 5 5
  }, 200);
};
````
上面的i是在全局作用域里面的，只存在一个值，等待回调函数执行时，用词法作用域捕获的i就只能是5，因为这个循环计算的i值在回调函数结束前就已经执行到5了  
解决办法1:调用函数，创建函数作用域
````
for(var i = 0; i < 5; i++) {
  plus(i);
};

function plus(i) {
  setTimeout(function() {
     console.log(i);			// 0 1 2 3 4 
  }, 200); 
}
````
这里相当于创建了5个函数作用域来保存，我们的 i 值  
解决办法2:采用立即执行函数，创建函数作用域
````
for(var i = 0; i < 5; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j);
    }, 200);
  })(i);
};
````
解决办法3:最简单的就是变var关键字为let关键字，从而创建块级作用域  
4.词法作用域  
````
testValue = 'outer';
function afun() {
  var testValue = 'middle';
  console.log(testValue);		// "middle"
  
  function innerFun() {
    var testValue = 'inner';
    console.log(testValue);		// "inner"
  }
  return innerFun();
}

afun();
console.log(testValue);			// "outer"
````
当我们要使用声明的变量时：JS引擎总会从最近的一个域开始向外层域查找  
5.动态作用域  
是个大坑，**目前在js仅存的引用动态作用域的地方:this引用**   
动态作用域:作用域是基于调用栈的，而不是代码中的作用域嵌套；  
作用域嵌套:有词法作用域一样的特性，查找变量时，总是寻找最近的作用域；
````
var testValue = 'outer';
function foo() {
  console.log(testValue);		// "inner"
}
function bar() {
  var testValue = 'inner'; 
  foo();
}
bar();
````