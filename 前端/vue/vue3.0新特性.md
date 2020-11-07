## Vue3.0 新特性
### proxy代替Object.defineProperty()
Object.defineProperty()存在诸多限制：无法监听属性的添加和删除、数组索引和长度的变更;Proxy可以支持Map、Set、WeakMap和WeakSet,**可以监听整个对象，不用再去遍历所有属性进行劫持**
````
let target = {
    a : 1,
    b : 2,
    c : 3
}

let proxy = new Proxy(target, {
   set(target, key, value, receiver){
      console.log('检测到了set的key为 -> ' + key);
      return Reflect.set(target, key, value, receiver);
   }
})

proxy.a = '1'; // 检测到了set的key为 -> a
proxy.b = '2'; // 检测到了set的key为 -> b
proxy.c = '3'; // 检测到了set的key为 -> c
````
>疑问:Reflect.set干什么用的？**通过Reflect的set方法去模拟真实的set方法**，也就是说，有了Reflect.set,原对象会改变，变成{a:'1',b:'2',c:'3'}; 没有，原对象还是{ a:1,b:2,c:3}
### 用Proxy实现Vue的数据劫持
````
function isArray(o){
   return Object.prototype.toString.call(o) === `[object Array]`
}
function isObject(o){
   return Object.prototype.toString.call(o) === `[object Object]`
}

class Observables{
   constructor(
      target,
      handler = {
         set(target,key,value,reveiver){
            return Reflect.set(target,key,value,receiver)
         }
      }
   )
   {
      if(!isObject(target) && !isArray(target)){
         throw new TypeError('target 不是数组或对象')
      }

      this._target  = JSON.parse(JSON.stringify(target)); //避免引用修改，数组不考虑
      this._handler = handler
      return new Proxy(this._observables(this._target),this._handler); 
   }

   //为子项为Array或者Object类型数据进行代理
   _observables(target){
      for(const key in target){
          if( isObject(target[key]) || isArray(target[key]) ){
               this._observables(target[key]);
               target[key] = new Proxy(target[key], this._handler);
          }
      }
      return target
   }
}
````
## Vue3.0的Composition API(组测API)
[Vue3 Composition API使用教程](https://juejin.im/post/6844904066103902215)
### setup
setup是vue新增的一个生命周期，它是组件内使用Composition API的入口，**它在beforeCreate之后、created之前执行**,用了setup，就不要使用data了，setup也获取不到data里面的值，setup()打印this是undefined
````
<template>
  <button @click="increment">
      {{ state.count }}
      {{ state.double}}
  </button>
</template>

<script>
  import { reactive, computed } from 'vue'
  export default{
     setup(props,context){
        const state = reactive({
           count: 0,
           double: computed(()=>state.count * 2)
        })

        function increment(){
           state.count++
        }

        return {
           state,
           increment
        }
     }
  }
</script>
````
### Options API vs Composition API
* Options API 相同逻辑写在不同的代码，各个逻辑的代码交叉错乱，开发维护时，跳来跳去
* Composition API 相同逻辑可以写在同一个地方，这些逻辑可以使用函数抽离处理，各个逻辑之间界限分明
### ref
Composition API有众多接口：reactive、computed、watch、ref、toRefs、readonly...  
ref函数接收一个用于初始化的值并返回一个响应式的和可修改的ref对象。该ref对象存在一个value属性，value保存着ref对象的值  
````
<template>
   <div class="tf">
     <p>{{count}}</p>
     <button @click="onClick(true)">+</button>
     <button @click="onClick(false)">-</button>
   </div>
</template>
<script>
  import {ref,reactive} from 'vue'
  export default{
     setup(){
        const count = ref(0)

        //在reactive中使用不需要count.value,会自动解包
        const state = reactive({count})
        const onClick = (isAdd) =>{
           isAdd ? count.value++ : count.value -- 
        }
        return {count,onClick}
     }
  }
</script>
````
### 对比vue2的生命周期钩子
* beforeCreate -> 使用 setup()
* created -> 使用 setup()
* beforeMount -> onBeforeMount
* mounted -> onMounted
* beforeUpdate -> onBeforeUpdate
* updated -> onUpdated
* beforeDestroy -> onBeforeUnmount
* destroyed -> onUnmounted
* errorCaptured -> onErrorCaptured