# js的数据类型分类和判断
最新的ECMAScript标准其实定义了8种数据类型:  
Number(数字)、String(字符串)、Boolean(布尔值)、undefined、null、Object(对象)、*BigInt(任意精度的整数)、*Symbol(es6新增加的一种类型)，其中BigInt和Symbol不常见，就不做讨论，另外**对象类型是包括Array(数组)和Function(函数)**，还有两个特殊对象RegExp(正则)和Date(日期)  
6种常见数据类型归类:  
1.按存储类型分  
基本类型:Number、String、Boolean、undefined、null  
引用类型:Object，包括Array、Function、RegExp、Date  
2.按是否可变  
可变:Object，包括Array、Function、RegExp、Date   
不可变:Number、String、Boolean、undefined、null  
3.按是否可拥有方法分类:  
可拥有方法:Number、String、Boolean、Object  
不可拥有方法:undefined、null   
## 基本类型和引用类型的区别
1.基本类型的值不可变，引用类型的值可变

````
var a = 'a';
var b = 1;
    a = 'b';
    b = 2;
console.log(a); //b
console.log(b); //2  
````
看到上面这个例子，会困惑不是说基本类型的值不可变吗?其实这里的可变与不可变不是相当然的那样理解的:声明了一个变量a,并赋值'a'，此时栈内存中会为a变量开辟一块内存空间用来存储'a'这个值，当重新给a变量赋值，新的值会将之前栈内存中变量a对应的值整体给**覆盖**掉，然后存入新值'b'。所以**不可变的意思是无法直接对原始值进行操作，而是只能先将原始值覆盖后在重新赋新值**，而引用类型是可以利用操作指针的方式来修改存储在堆内存中的对象的属性值  
2.基本类型不能添加属性和方法，引用类型可以添加  
````
var base   = 'base';
base.attr  = 'attr';
base.method= function(){...}
console.log(base.attr); //undefined
console.log(base.method); //undefined
````
3.基本类型的比较是值的比较，引用类型的比较是引用地址的比较  
````
var a =1;
var b =true;
var c ='1';
console.log(a==b); //true
console.log(a==c); //true
console.log(a===b); //false
console.log(a===c); //false
````
==符号会自动进行类型的转换，===不会，===除了比较值还会判断类型是否相等
````
var obj1={};
var obj2={};
console.log(obj1==obj2); //false,比较的是引用地址
````
**基本类型的值存储在栈内存，引用类型存储在栈内存和堆内存(栈内存存储声明变量的标识符和指向堆内存中存储值的指针，堆内存存储实际的值(对象)，外部访问其实是通过指针来间接访问数据对象的)**
## 类型判断
1.typeof:typeof运算符可以准确判断number、string、boolean、undefined、function，但是对于null和除function之外的引用类型就不行了，而只会都返回object
````
 typeof 1; // number
 typeof '';//string
 typeof true; //boolean
 typeof undefined; //undefined
 typeof new Function(); //function
 typeof null; //object 无效
 typeof [] ; //object 无效
 typeof new Date(); //object 无效
 typeof new RegExp(); //object 无效

````
2.instanceof:instanceof运算符只能用于引用类型的判断，他会判断运算符左操作数对象的原型链上是否有右边这个构造函数的prototype属性，也就是说指定对象是否是某个构造函数的实例,当然原型链的顶端就是Object
````
[] instanceof Array; //true
[] instanceof Object; //true
{} instanceof Array; //false
new Date() instanceof Date;//true
new Date() instanceof Object;//true
function Person(){};
new Person() instanceof Person;//true
new Person() instanceof Object;//true
````
3.Object.prototype.toString  
使用Object.prototype.toString.call(obj)或者Object.prototype.toString.apply(obj)会默认返回其调用者的具体类型，也就是toString运行时this指向的对象类型，返回类型格式为[object,xxx]  
````
Object.prototype.toString.call('123');      //"[object String]"
Object.prototype.toString.call(1);          //"[object Number]"
Object.prototype.toString.call(new Date()); //"[object Date]"
Object.prototype.toString.call(undefined);  //"[object Undefined]"
Object.prototype.toString.call(null);       //"[object Null]"
````
