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
因为组件是用来复用的，js里对象是引用关系，如果组件中data是一个对象，作用域就没有隔离，子组件中的data属性值会相互影响；如果组件中data选项是一个函数，那么**每个实例就可以维护一份被返回对象的独立拷贝，组件实例之间的data属性值不会相互影响**；而new Vue的实例，是不会被复用的，因此不存在引用对象的问题
## 7.v-model的原理
在vue项目中，主要使用v-model指令在表单input、textarea、select等元素上创建双向数据绑定，v-model 本质上不过是语法糖，v-model 在内部为不同的输入元素使用不同的属性并抛出不同的事件：
* text和textarea元素使用value属性和input事件
* checkbox和radio使用checked属性和change事件
* select字段将value作为prop并将change作为事件
````
<input v-model='something'>
相当于
<input v-bind:value="something" v-on:input="something = $event.target.value">
````
## 8.Vue组件间通信有哪几种方式？
1.props/$emit 适用于父子组件通信 

2.ref 与 $parent/$children 适用父子组件通信
* ref:如果在普通的DOM元素上使用，引用指向的就是DOM元素；用在组件上，就是指向组件实例

3.EventBus ($emit/$on)适用于父子、隔代、兄弟组件通信  
这种方法通过一个空的 Vue 实例作为中央事件总线（事件中心），用它来触发事件和监听事件，从而实现任何组件间的通信

4.provide/inject适用于隔代组件通信

5.Vuex 适用于父子、隔代、兄弟组件通信
**Vuex就是一个专门为Vue.js应用程序开发的状态管理模式**，每一个Vuex应用的核心就是store(仓库)，“store”就是一个容器，包含应用中大部分的状态

## 9.使用过Vue SSR吗?说说SSR?
Vue.js是构建客户端应用程序的框架，默认情况下，可以向浏览器输出Vue组件，进行生成DOM和操作DOM,然后，也可以将用一个组件渲染为服务端的HTML字符串，将它们直接发送到浏览器  
SSR:**vue在客户端将标签渲染成的整个html片段的工作在服务端完成，服务端形成的html片段直接返回给客户端这个过程就叫做服务端渲染**  
优点：
* 更好的SEO：因为SPA页面的内容是通过ajax获取，而搜索引擎爬取工具并不会等待ajax异步完成后再抓取页面内容，即SPA中抓取不到页面通过ajax获取到的内容；SSR是直接由服务端返回已经渲染好的页面(数据已经再包含在页面中)，所以搜素引擎可以更好的抓取  
* 更快的内容到达时间(首屏加载更快)：SPA会等待所有Vue编译后的js文件都下载完成，才会开始页面渲染，文件下载要消耗时间；SSR服务端解析渲染好页面直接返回，无需等待下载js文件再去渲染  
缺点:
* 更多的开发条件限制： 例如服务端渲染只支持 beforCreate和created两个钩子函数，这会导致一些外部扩展库需要特殊处理，才能在服务端渲染应用程序中运行；并且与可以部署在任何静态文件服务器上的完全静态单页面应用程序SPA不同，服务端渲染应用程序，需要处于Node.js server运行环境；
* 更多的服务器负载：在Node.js中渲染完整的应用程序，显然会比仅仅提供静态文件的server更加大量占用CPU资源

## 10.Vue为什么没有类似于React中shouldComponentUpdate的生命周期？
React的shouldComponent的用法:  
````
class CounterButton extends React.Component{
  constructor(props){
    super(props);
    this.state = {count:1};
  }

  shouldComponentUpdate(nextProps,nextState){
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
  }

   render() {
    <button
      color={this.props.color}
      onClick={() => this.setState(state => ({count: state.count + 1}))}>
      Count: {this.state.count}
    </button>
  }

}
在上述代码中，组件仅仅会校验prop.color和state.count,如果这些值都不会改变，那么组件就不会有更新
````
根本原因是Vue与React的变化侦测方式有所不同  
React是pull的方式侦测变化,当React知道发生变化后,会使用Virtual Dom Diff进行差异检测,**父组件一个state的变化，就会导致以该组件的所有子组件都重写render，尽管绝大多数子组件的props没有变化**,这个时候需要用shouldComponentUpdate进行手动操作来减少diff,从而提高程序整体的性能.  
Vue是pull+push的方式侦测变化的,在一开始就知道那个组件发生了变化,因此在push的阶段并不需要手动控制diff  
## SPA单页面如何计算PV(页面访问量)
百度统计     
Vue的入口文件中全局定义Router.beforeEach