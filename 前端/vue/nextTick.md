## 什么是Vue.nextTick()??
获取更新后的DOM的Vue方法  
注意：Vue实现响应式并不是数据发生变化之后DOM立即变化，而是按一定的策略进行 DOM 的更新，$nextTick 是在下次 DOM 更新循环结束之后执行延迟回调，在修改数据之后使用 $nextTick，则可以在回调中获取更新后的 DOM  
Vue 异步执行 DOM 更新。只要观察到数据变化，**Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次**。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作
https://www.cnblogs.com/goloving/p/9404643.html
## 示例
>nextTick()回调函数中一般是执行操作DOM的代码，当数据更新了，在dom中渲染后，再执行该函数
````
<template>
  <div class="hello">
    <div>
      <button id="firstBtn" @click="testClick()" ref="btn">{{testMsg}}</button>
    </div>
  </div>
</template>
 
<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      testMsg:"原始值",
    }
  },
  methods:{
    testClick:function(){
        
      this.testMsg="修改后的值";
      console.log(that.$refs.btn.innerText);   //输出：原始值

      this.$nextTick(function(){  //不使用this.$nextTick()方法会报错
         console.log(that.$refs.btn.innerText);  //输出：修改后的值
      });
    },
  }
}
</script>
````
