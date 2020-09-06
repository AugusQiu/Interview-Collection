相关点:
* prototype是函数才有的一个属性
* 函数的prototype属性指向了一个对象，这个对象就是**调用构造函数创建的实例**的原型
* 每个创建的实例对象有一个_proto_的属性，指向该对象的原型
* 每个原型都会有一个constructor属性，指向关联的构造函数
* 所有function实际上也是Function的实例
* Function.prototype._proto_ = Object.prototype
* function A(){} A.a = 1 这个A.a是专属于构造函数的静态属性，实例是访问不到的
## 几个经典面试题
````
 Function.prototype.a = 1;
 Object.prototype.b   = 2;
 function A() {}
 var a = new A();
 
 console.log(a.a, a.b); // undefined, 2
 console.log(A.a, A.b); // 1, 2
````
* A.a 先在A中找，因为没有直接定义静态属性A.a，所以A中找不到；但A也是Function的实例，就会找到Function.prototype上面，Function.prototype中有a
* a是A的实例了，跟Function这个内置的构造器就没什么关系了，A.prototype.a没有，就只能到顶层的Object.prototype去找，Object.prototype没a只有b

````
    function A() {}
    function B(a) {
        this.a = a;
    }
    function C(a) {
        if (a) {
            this.a = a;
        }
    }
    A.prototype.a = 1;
    B.prototype.a = 1;
    C.prototype.a = 1;
    
    console.log(new A().a); // 1
    console.log(new B().a); // undefined
    console.log(new C(2).a); // 2
````
* A里没有a,所以得到原型里去找，所以第一个打印1
* B、C里都有a属性，就不用去原型里找，B传了undefined，C传了2，于是第二个打印undefined，第三个打印2
## 构造函数内直接定义的方法和prototype属性上的方法的对比
https://blog.csdn.net/weixin_38098192/article/details/80582352
````
function A(){
    this.name = name
    this.showName = function(){
      ......
    }
}
A.prototype.showName = function(){

}
````
### 区别
> 直接定义在构造函数内部的方法，会在它的每一个实例上都克隆这个方法；定义在构造函数的 prototype 属性上的方法会让它的所有示例都共享这个方法

> 在函数prototype属性上定义方法，改变这个方法，所有实例对象都会改变