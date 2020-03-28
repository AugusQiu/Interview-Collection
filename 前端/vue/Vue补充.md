## Vue.js是什么，用来做什么？
Vue.js是一套构建用户界面的渐进式框架，Vue 的核心库只关注视图层，并且非常容易学习，易于与其他库整合，适合用来开发 表单项繁多、内容需要根据用户的操作进行修改的单页面应用
## 渐进式怎么理解？
渐进式框架的大概意思就是你可以只用我的一部分，而不是用了我这一点就必须用我的所有部分  
可整合其他，不一定全家桶
## v-on 可以监听多个方法吗？
可以
````
<input type="text" :value="name" @input="onInput" @focus="onFocus" @blur="onBlur" />
````
## 什么是 MVVM？比之 MVC 有什么区别？
MVC里，View是可以直接访问Model的，所以View里会包含Model信息，，在MVC模型里，Model不依赖于View，但是 View是依赖于Model的  
MVVM提供对View和ViewModel的双向绑定，View和Model之间并没有直接的联系，而是通过ViewModel进行交互
## View的优点
* 低耦合，视图(View)独立于Model变化和修改
* 可重用性，可以把一些视图逻辑放在一个 ViewModel 里面，让很多 view 重用这段视图逻辑
* 便于测试，界面素来是比较难测试的，而现在测试可以针对ViewModel来写
## 三大框架的对比
* 组织方式：React和Vue都是模块化，Angular是MVC
* 数据绑定：Vue、Angular双向绑定，**React单向绑定**(单向数据流：用户访问View，View发出用户交互的Action；在Action里对state进行相应更新；state更新后会触发View更新页面的过程)
* 路由：React、Vue动态路由，Angular静态路由
* 自由度：React自由度大
## vue第一次加载页面会触发哪几个钩子
beforeCreate, created, beforeMount, mounted
## Vue.set 视图更新？
https://zhuanlan.zhihu.com/p/67301402  
问题：数据变化，但是视图却没有实时渲染  
解决办法：**当操作数组和对象的时候，因为js有些方法不是响应式的，所以vue检测不到它的变动。但是我们不得不得到我们想要的操作，就需要使用vue提供的响应式方法：Vue.set(object,key,value)**，将响应属性添加到嵌套的对象上**   
ps:this.$set是全局Vue.set方法的别名  
ps:一个普通的 JavaScript 对象传给 Vue 实例的 data 选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter，Object.defineProperty 是 ES5 中一个无法 shim 的特性，这也就是为什么 Vue 不支持 IE8 以及更低版本浏览器
##  v-if 和 v-for 的优先级?
v-for优先级高
## Vue 检测数组变化有什么注意事项？
由于js的限制，Vue不能检测以下变动的数组
* 当你利用索引设置一个项时, 例如: vm.items[indexOfItem] = newValue;  
解决办法：Vue.set()
* 当你修改数组的长度时,例如: vm.items.length = newLength;  
解决办法：可以使用splice，example1.items.splice(newLength)
## Vue的两个核心
数据驱动、组件系统
## Vue如何获取DOM?
this.$refs
## 什么是可接受的 prop 类型？
````
prop:{
    type:String,
    required:true
}
````
## 什么是非 prop 属性？
data-title，class，style就是非prop属性，无需定义相应的prop，这些属性都会被添加到组件的根元素上
## props有哪些可用的验证
````
String
Number
Boolean
Symbol

Object
Function
Array
````
## slot
插槽，也就是slot，**是组件的一块HTML模板，这块模板显示不显示、以及怎样显示由父组件来决定**
````
//父组件
<template>
    <div class="father">
        <h3>这里是父组件</h3>
        <child>
            <div class="tmpl">
              <span>菜单1</span>
              <span>菜单2</span>
              <span>菜单3</span>
              <span>菜单4</span>
              <span>菜单5</span>
              <span>菜单6</span>
            </div>
        </child>
    </div>
</template>

//子组件
<template>
    <div class="child">
        <h3>这里是子组件</h3>
        <slot></slot>
    </div>
</template>

在这个例子里，因为父组件在里面写了html模板，那么子组件的匿名插槽会被使用
````
## 动态组件
在**Vue中有一个:is特性**， 组件可以用过 :is 来切换
````
<template>
  <div>
     <button @click="showWhat = 'A'">showA</button>
     <button @click="showWhat = 'B'">showB</button>
     
     <!--动态切换显隐，组件-->
     <component :is="showWhat"></component>
  </div>
</template>

<script>
//引入组件A以及组件B
import A from "./a";
import B from "./b";

export default {
  components: {
    A,
    B
  },
  data() {
    return {
      showWhat: "A"
    };
  }
};
</script>

<style>
</style>
````
:is 通过 keep-alive标签缓存。被该标签包裹的组件就会被缓存下来，每次点击都会重新渲染(即创造~挂载)。相比较于 v-show 和 v-if 来说 使用 :is 动态组件更加优雅
## 提供 transitions 有什么可能的方式？
vue过渡：
* v-enter-active:v-enter v-enter-to
* v-leave-active:v-leave v-leave-to
## 请说出vue.cli项目中src目录每个文件夹和文件的用法？
assets文件夹是放静态资源；components是放组件；router是定义路由相关的配置;view视图；app.vue是一个应用主组件；main.js是入口文件
## .vue的单文件组件解决了哪些问题？
字符串模板问题：不支持CSS和css预处理
## Vue-router 跳转和 location.href 有什么区别？
location.href是写死的，vue-router无论是 HTML5 history 模式还是 hash 模式,让浏览器不再重新加载页面
##  Vue 里面 router-link 在电脑上有用，在安卓上没反应怎么解决？
babel问题，安装babel polypill 插件解决
## Vue2中注册在router-link上事件无效解决方法
@click后面加上native
## 路由匹配优先级和嵌套路由
匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高  
嵌套路由: children字段
## 单页面应用和多页面应用区别及优缺点？
* 组成不同：SPA一个外壳页面加多个页面片段，MPA多个完整页面  
* 资源：SPA共用，只需要在外壳部分加载，MPA不共用
* 刷新：SPA局部刷新，MPA整页刷新
* url模式：SPA用hash
* SEO:SPA不利于SEO
* 转场动画:SPA同意实现

## 什么是 Asset URL 转换规则
https://vue-loader.vuejs.org/zh/guide/asset-url.html#%E8%BD%AC%E6%8D%A2%E8%A7%84%E5%88%99
## 如何使用 deep 选择器？
vue组件编译后，会将 template 中的每个元素加入 [data-v-xxxx] 属性来确保 style scoped 仅本组件的元素而不会污染全局，但如果你引用了第三方组件，默认只会对组件的最外层（div）加入这个 [data-v-xxxx] 属性，但第二层开始就没有效果了

## keep-alive 标签的目的是什么？
搭建 vue 项目时，某些组件没必要多次渲染，所以需要将组件在内存中进行‘持久化’，此时 keep-alive 便可以派上用场了。**keep-alive 可以使被包含的组件状态维持不变**，即便是组件切换了，其内的状态依旧维持在内存之中。在下一次显示时，也不会重现渲染     
ps：keep-alive 与 transition相似，只是一个**抽象组件，它不会在DOM树中渲染(真实或者虚拟都不会)**，也不在父组件链中存在，比如：你永远在 this.$parent 中找不到 keep-alive
## vue递归组件
只要咱们需要使用相同的模板结构，但需要使用分层输入数据，就可以使用递归。 像树状视图（用于显示文件夹结构），网站上的注释，嵌套菜单等组件等
## Vue 提供的事件修饰符是什么？
* .prevent = js的event.preventDefault(),用来阻止默认程序的运行
* .stop：阻止冒泡
* .capture：打乱冒泡顺序，用以下代码为例，发生click事件时会优先去找你可以传递到的所有父元素中最后一个有.capture的元素
* .self：不让子元素的事件触发自己绑定的事件，但是不会阻止冒泡
* .once：事件只会触发一次
## vue异步组件
异步组件则是在用到该组件时，异步通过请求去拉取对应组件的js（这里需要和webpack的import相结合）。当对应的js加载完成后，获取到异步组件的配置项，从而创建组件构造器。再通过forceRender，强制依赖它的vm实例重新触发渲染函数  
https://juejin.im/post/
5d52c1bce51d4562043f56de
## vue3.0和vue2.x的区别
vue3.0用typescript重构  
数据监听：vue2.x大家都知道使用的是es5的object.defineproperties中getter和setter实现的，而**vue3.0的版本，是基于Proxy进行监听的**，其实基于proxy监听就是所谓的lazy by default，什么意思呢，就是只要你用到了才会监听，可以理解为‘按需监听’，官方给出的诠释是：速度加倍，同时内存占用还减半  
new处来的组件实例，vue2.x都会挂载在Vue对象上，无论是否用到，而vue3.0中可以按需引入  
更好的多端渲染支持:
例如国内的weex，国外的native vue。在vue3.0中新增了custom Render Api，只需要import {curateRender} from '@vue/runtime-core'
## Vue首屏优化
* webpack splitchunks 拆包
* image-webpack-loader 图片压缩
* webpack4 的 mini-css-extract-plugin 压缩css
* uglifyjs压缩js代码
* 路由懒加载、图片懒加载
* 按需导入组件
* gzip压缩
* cdn加速
## Vue响应式关于Array的特别处理
https://cloud.tencent.com/developer/article/1461563
### 问题
Object.defineProperty()劫持数组为其设置getter和setter后，调用的数组的push、splice、pop等方法改变数组元素时并不会触发数组的setter，也就无法做到响应式更新  
根据数组索引改变元素，Vue无法响应，要用到Vue.set
### 解决
* **重写数组方法**：拦截了数组的原型，在实现时除了将数组方法名对应的原始方法调用一遍，然后将执行结果返回
* **手动派发更新**：执行ob.dep.notify()将当前数组的变更通知给其订阅者
## Vue优化SEO
首先，搜索引擎的基础爬虫的原理就是抓取你的url，然后获取你的html源代码并解析。 而你的页面通常用了vue等js的数据绑定机制来展示页面数据，**爬虫获取到的html是你的模型页面而不是最终数据的渲染页面**，所以说用js来渲染数据对seo并不友好
* 采用服务端渲染 SSR:后台的 Node.js 环境中完成渲染逻辑，然后将 HTML 视图直接返回给客户端
* prerender-spa-plugin页面预渲染: 利用了 Puppeteer的爬取页面的功能。 Puppeteer 是一个 Chrome官方出品的 **headlessChromenode**库。它提供了一系列的 API, 可以在无 UI 的情况下调用 Chrome 的功能, 适用于爬虫、自动化处理等各种场景,在 Webpack 构建阶段的最后，在本地启动一个 Puppeteer 的服务，访问配置了预渲染的路由，然后将 Puppeteer 中渲染的页面输出到 HTML 文件中，并建立路由对应的目录
## Vue如何解析template模板
* template先被编译成AST语法树
* 再转化为render函数
* 最终返回一个VNode(VNode就是Vue的虚拟DOM节点)