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
## 手写实现
### call
````
/**
 * 自定义call实现
 * @param context   上下文this对象
 * @param args      动态参数
 */
Function.prototype.ownCall = function(context,...args){
    context = (typeof context === 'object' ? context : window)
    const key = Symbol()  // 防止覆盖掉原有属性
    context[key] = this   // 这里的this为需要执行的方法
    const result = context[key](...args)
    delete context[key]
    return result
}

//验证
function fun(arg1, arg2) {
  console.log(this.name)
  console.log(arg1 + arg2)
}
const _this = { name: 'YIYING' }
// 接受的是一个参数列表 方法立即执行
fun.ownCall(_this, 1, 2)  // YIYING  3
````
### apply
````
/**
 * 自定义Apply实现
 * @param context   上下文this对象
 * @param args      参数数组
 */
Function.prototype.ownApply = function(context, args) {
  context = (typeof context === 'object' ? context : window)

  const key = Symbol()
  context[key] = this
  const result = context[key](...args)
  delete context[key]
  return result
}

// 验证
function fun(arg1, arg2) {
  console.log(this.name)
  console.log(arg1 + arg2)
}
const _this = { name: 'YIYING' }
fun.ownApply(_this, [1, 2])
````
### bind
````
Function.prototype.ownBind = function(context) {
  context = (typeof context === 'object' ? context : window)
  return (...args)=>{
    this.call(context, ...args)
  }
}

// 验证
function fun(arg1, arg2) {
  console.log(this.name)
  console.log(arg1 + arg2)
}

const _this = { name: 'YIYING' }
const newFun = fun.ownBind(_this) // 只变更fun中的this指向，返回新function对象
newFun(1, 2)
````