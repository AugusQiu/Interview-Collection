## vue.set原理？
对于对象和数组的响应式，是在初始化时期，利用Object.defineProperty()进行监听，实现双向绑定。但是之后再给对象新增属性、给数组增加元素，修改长度时，这些变化就不会反映到页面中  
>使用this.$set(target,key,value)，target为需要添加属性的对象，key为要添加的属性名，value为属性key对应的值

````
// src/core/observer/index.js
export function set (target: Array<any> | Object, key: any, val: any): any {
  if (process.env.NODE_ENV !== 'production' &&
    (isUndef(target) || isPrimitive(target))
  ) {
    warn(`Cannot set reactive property on undefined, null, or primitive value: ${(target: any)}`)
  }
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    target.length = Math.max(target.length, key)
    target.splice(key, 1, val)
    return val
  }
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  const ob = (target: any).__ob__
  if (target._isVue || (ob && ob.vmCount)) {
    process.env.NODE_ENV !== 'production' && warn(
      'Avoid adding reactive properties to a Vue instance or its root $data ' +
      'at runtime - declare it upfront in the data option.'
    )
    return val
  }
  if (!ob) {
    target[key] = val
    return val
  }
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}
````
源码执行逻辑：
* 如果是开发环境，且target未定义(null、undefined),或者target为基础数据类型(string、boolean、number、symbol),抛出警告
* 如果target为数组且key为有效，将数组的长度设置为target.length和key中的最大的那一个，然后调用数组的splice方法（vue中重写的splice方法）添加元素
* 如果属性key存在于target对象中, 且key不是Object.prototype上的属性，**表明是在修改target对象属性key的值**（不管target对象是否是响应式，只要key存在于target对象中，就执行这一步逻辑），此时就直接将value直接赋值给target[key]
* 判断target，当target为vue实例或根数据data对象时，在开发环境下抛错
* 当一个数据为响应式时，vue会给该数据添加一个__ob__属性, 因此**可以通过判断target对象是否存在__ob__属性来判断target是否为响应式数据**，当target是非响应式数据时，我们就按照普通对象添加属性的方式来处理；当target对象是响应式数据时，我们将target的属性key也设置为响应式并手动触发通知其属性值的更新
````
//这两行代码是将新增属性设置为响应式，并手动触发通知该属性值的更新
defineReactive(ob.value, key, val)
ob.dep.notify()
````
## vue.nextTick()原理?
https://cloud.tencent.com/developer/article/1633546  
https://www.cnblogs.com/chanwahfung/p/13296293.html
### MutationObserver
**MutationObserver是HTML5新增的属性，用于监听DOM修改事件，能够监听到节点的属性、文本内容、子节点等的改动**
````
//基本用法
let observer = new MutationObserver(()=>{
    console.log('DOM被修改了!')
})
let article = document.querySelector('article')
observer.observer(article)
````
### nextTick和MO
如果检测到浏览器支持MutationObserver，利用**document.createTextNode自创一个文本节点，之后再手动修改文本节点属性**，监听这个文本节点的改动，来触发DOM更新完毕的回调  
### Event Loop
<img src="https://upload-images.jianshu.io/upload_images/12665637-2b147a7117f769e6.png?imageMogr2/auto-orient/strip|imageView2/2/w/1024/format/webp" width="450" height="300">
平常用setTimeout来执行异步代码，就是在任务队列的末尾添加一个task，待前面的任务都执行完后再执行它

**每次event loop的最后，都会进行一次UI render,也就是更新DOM**
### microtask
每次event loop, 先取出一个宏任务执行，再检查microtask队列中是否存在微任务，存在，将所有的微任务都拿出来执行完