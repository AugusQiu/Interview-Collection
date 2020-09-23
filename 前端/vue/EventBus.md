## 关于Vue的EventBus
>vue父子组件通信，常见的就是props/$emit,如果两个组件不存在父子关系，那么可以考虑Vue中的事件总线:**EventBus**
## 使用全局EventBus
它的工作原理是发布/订阅方法，通常称为 Pub/Sub
### 创建全局EventBus
````
var EventBus = new Vue()
Object.defineProperties(Vue.prototype,{
    $bus:{
        get:function(){
            return EventBus
        }
    }
})
````
### A、B页面通信
A页面在按钮上绑定了点击事件，发送一则消息通知B页面
````
// A.vue
<template>
  <button @click="sendMsg()">点我</button>
</template>
<script>
  import { EventBus } from "../event-bus.js"
  export default{
     methods:{
         sendMsg(){
            this.$bus.$emit("sendMsg","来自A页面的消息")
         }
     }
  }
</script>
````
B页面接收消息
````
this.$bus.$on("sendMsg",(val)=>{
    console.log(val)
})
````
可以使用**this.$bus.$off('sendMsg')来移除事件监听**