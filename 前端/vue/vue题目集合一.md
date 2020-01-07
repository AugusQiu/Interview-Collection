## 1.对于SPA单页面的理解？它的优缺点
SPA(single-page application) 仅在**web页面初始化**时加载相应的html、css、js.一旦页面加载完成，SPA**不会因为用户的操作而进行页面的重新加载或跳转**；而是用**路由机制**实现html内容的转换  
优点：
* 用户体验好、快，部分内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染
* 前后端职责分离，前端页面交互，后端数据处理
* 服务器压力变小  

缺点：
* 初次加载耗时长：初始化，js、css就得统一加载，部分页面按需加载
* 页面前进后退只能用路由管理：由于单页应用在一个页面中显示所有的内容，所以不能使用浏览器的前进后退功能，所有的页面切换需要自己建立堆栈管理 
* SEO(搜索引擎优化)难度大：所有的内容都在一个页面中动态替换显示，这使得在SEO上有天然的弱势  

## 2.v-if和v-show的区别  
v-if是真正的条件渲染，一个组件元素是真正的销毁，而不是不可见；是**惰性**的，初始渲染时条件为假，就什么都不做，直到条件第一次为真，才开始渲染  
v-show:不管初始条件是什么，元素总会被创建渲染，再根据条件，基于CSS的“display”属性进行是否可见的变化  
所以，**v-if适用于在运行时很少改变条件，不需要频繁切换条件的场景；v-show 则适用于需要非常频繁切换条件的场景**  

## 3.class与style如何动态绑定？
````
//对象语法
<div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>

data: {
  isActive: true,
  hasError: false
}

//数组语法
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
````
````
//对象语法
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
data: {
  activeColor: 'red',
  fontSize: 30
}

//数组语法
<div v-bind:style="[styleColor, styleSize]"></div>
data: {
  styleColor: {
     color: 'red'
   },
  styleSize:{
     fontSize:'23px'
  }
}
````
## 4.Vue的单向数据流？
prop使得父子组件之间形成了一个**单向下行绑定**：父级 prop的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态  
每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值    
这意味着你不应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。**子组件想修改时，只能通过 $emit 派发一个自定义事件，父组件接收到后，由父组件修改**
* 
````
//父组件
<template>
  <div>
    <child :msg="msg"></child>
  </div>
</template>

<script>
  import child from './child'
  expoer default{
      data(){
          return{
              msg:'传给子组件的信息'
          }
      },
      components:{
          child
      }
  }
</script>
````
````
//子组件
<template>
  <div>
    <p>{{msg}}</p>
  </div>
</template>
<script>
  export default{
      //接收父组件传给来的值
      props:{
          msg:String,
          required:true
      }
  }
</script>
````
## 5.computed和watch的区别和运用的场景？
computed:计算属性，**依赖其它属性值，并且计算出来的值有缓存**，只有它依赖的属性值发生改变，下一次获取computed的值时才会重新计算，且**必须return一个新计算好的值**  
methods方法表示一个具体的操作，主要书写业务逻辑  
watch:观察，用于监听特定数据变化，**每当监听的数据变化时都会执行回调进行后续操作**   
适用场景:
* 当我们需要进行数值计算，并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算
* 当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作 ( 访问一个 API )，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的
````
data:{
  msg:'hello'
},
computed:{
  reversedMsg: function () {
      return this.msg.split('').reverse().join('')
  }
}
````
````
//用watch结合computed来监听一个对象的变化
<template>
  <div>
    <el-input v-model="demo.name"></el-input>
    {{value}}
  </div>
</template>
<script>
  export default {
    name: 'index',
    data() {
      return {
        demo: {
          name: ''
        },
        value: ''
      };
    },
    computed: {
      newName() {
        return this.demo.name;
      }
    },
    watch: {
      newName(val) {
        this.value = val;
      }
    }
  };
</script>
````
## 6.组件中 data 为什么是一个函数？
为什么组件中的 data 必须是一个函数，然后 return 一个对象，而 new Vue 实例里，data 可以直接是一个对象？  
因为组件是用来复用的，js里对象是引用关系，如果组件中data是一个对象，作用域就没有隔离，子组件中的data属性值会相互影响；如果组件中data选项是一个函数，那么每个实例就可以维护一份被返回对象的独立拷贝，组件实例之间的data属性值不会相互影响，；而new Vue的实例，是不会被复用的，因此不存在引用对象的问题