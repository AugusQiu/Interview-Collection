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

# this指针
四种调用方式:  
1.直接函数调用，this指针指向全局环境，即Window对象,例如：
````
var name='vicky'
function sayName(name){
    console.log(this.name);
}
sayName();   //this指向window对象，而因为在全局环境中定义了var name='vicky'；所以this.name输出：vicky
window.sayName();  //sayName()效果等同于window.sayName()

````
2.对象函数调用，this指针指向调用函数的对象本身，例如：
````
var object={
    'name':"vicky",
    'sayName':function(){ console.log(this.name)}
};
object.sayName();   //this指针指向object对象，因此输出vicky
````
````
var name='Bob';
function sayName(){
    console.log(this.name);
}
var object={'name':'vicky'};
object.sayName=sayName;          //sayName没有写成sayName(),表示不是执行函数，而是将sayName的指针赋值给object.sayName
object.sayName();               //由于对象函数调用方法，this指向对象本身，所以输出:'vicky'
sayName();                     //由于全局环境调用sayName()等同于window.sayName();输出:'Bob'
````
3.构造函数调用，this指针指向新创建的对象,例如:
````
function object(name){
    this.name=name；
    console.log(this);      //由于this指向新创建的对象本身，输出：Object { name: "vikcy" }
    console.log(this.name);  //输出:"vicky"
}
var myObject=new Object('vicky');  //this指向新创建的对象本身
````
4.间接函数调用，例如call、apply方法，还有个特殊的bind方法  
4.1 **call（this指针要指向的对象，参数1，参数2,...）**
call方法可以动态的设置函数体内的this指向，例如：
````
function Cat(age,name){
    this.name='cat';
    console.log(this);
    console.log('cat: age:'+age+",name:"+name);
}
var cat=new Cat(4,'Bob');    //输出:Cat {name: "cat"}和cat: age:4,name:Bob
Cat.call(this,3,'Tom');     //由于调用了call方法，输出：this指向了Window和cat: age:3,name:Tom
````
4.2 **apply(this指针要指向的对象,参数数组或arguments对象)**
apply方法和call方法的作用相同，唯一不同的是call方法要将参数一一传入，而apply方法传入的是数组或者arguments对象。arguments对象包含了函数的所有参数。
````
function Cat(age,name){
    this.name='cat';
    console.log(this);
    console.log('cat: age:'+age+",name:"+name);
}
var cat=new Cat(4,'Bob');    //输出:Cat {name: "cat"}和cat: age:4,name:Bob
Cat.apply(this,[3,'Tom']);     //由于调用了apply方法，输出：this指向了Window和cat: age:3,name:Tom
function getCat(age,name){
    Cat.apply(this, arguments);    //arguments包含了函数的参数
}
getCat(5,"kitty");           //由于调用了apply方法，输出：this指向了Window和cat: age:5,name:kitty
````
4.3 **bind(this指针要指向的对象)**,bind这个方法会创建一个函数的实例，其this会被**绑定**到传给 bind()函数参数
````
window.color = "red"; 
var o = { color: "blue" }; 
function sayColor(){ 
    console.log(this.color); 
} 

var objectSayColor = sayColor.bind(o); 
objectSayColor(); //由于调用了 sayColor.bind(o),bind函数返回的函数实例中的this直接绑定了o这个对象,所以即使在全局环境调用函数objectSayColor，也会输出："blue"
window.objectSayColor();   //输出:"blue"
````
## new关键字拓展
为什么构造函数调用的时候，this指针为什么指向创建对象的本身，new关键字到底做了什么？  
其实在通过new关键词去调用构造函数创建对象的时候，经历了如下的过程：  
1、创建新对象,继承构造函数的prototype  
2、调用call()方法，接受参数并修改this的指向，指向这个新对象  
3、执行构造函数，如果构造函数有返回对象，则这个对象会取代步骤1创建的新对象，如果构造函数没有返回值，则返回步骤1创建的对象。  
用代码简单模拟上述过程:
````
var newObject=function(func){
    var o={};  //创建一个新对象
    o.__proto__=func.prototype;  //继承构造函数prototype
    var k=func.call(o);         //调用call方法，修改this指向,指向这个新创建的对象
    if(typeof k=== 'object'){
        //如果构造函数有返回对象，则取代步骤1创建的对象，返回构造函数所返回的对象
        return k;
    }else{
        //如果构造函数没有返回对象，则直接返回步骤1创建的对象
        return o;
    }
}
````
