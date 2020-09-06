三者都可以用来修改函数执行的上下文，即**修改函数运行时的this指向**
````
function Child(name){
    this.name = name
}
Child.prototype = {
    constructor: Child,
    showName: function(){
        console.log(this.name)
    }
}

var child = new Child('thomas')
child.showName() // thomas

let obj = {name: 'tony'}

child.showName.call(obj) 
child.showName.apply(obj)
let b = child.showName.bind(obj)
b() // tony
````
* call、apply和bind的差别
> call和apply改变了函数的this上下文后便执行该函数；而bind则是返回改变了上下文后的一个函数，并没有立即执行
## 用call和apply实现多继承
````
function Class1(a,b){
   this.showclass1 = function(a,b){
       console.log(`class1:${a},${b}`)
   }
}

function Class2(a,b){
   this.showclass2 = function(a,b){
       console.log(`class2:${a},${b}`)
   }
}

function Class3(a,b,c) {
   Class1.call(this);
   Class2.call(this);
}

let arr  = [2,2]
let demo = new Class3()
demo.showclass1.call(this,1)    //class1:1,undefined
demo.showclass1.call(this,1,2)  //class1:1,2
demo.showclass2.apply(this,arr) //class2:2,2
````