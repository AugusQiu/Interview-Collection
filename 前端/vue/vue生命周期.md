## vue的生命周期
* beforeCreate：组件实例被创建之初，组件属性生效之前。也就是说**组件实例还没有完全被创建，data和methods中的数据也都没有初始化**  
* created：组件实例已经完全创建，属性也绑定，但真实dom还没有生成，$el还不可用。这里**可以调用data中的数据和method中的方法**  
* beforeMount：准备调用render函数。模板在内存中编辑完成了，但**尚未渲染**到页面中
* mounted：内存中的模板，已经被真实的挂载到页面中，用户可以看到渲染好的页面。mounted是实例创建期间的左后一个生命周期函数

运行中的两个事件:  
* beforeUpdate：要改变的数据更新了，但是页面显示却还没有更新，页面尚未与最新的数据保持同步
* updated：页面和data数据已经保持同步了

其它事件：
* beforeDestory：组件销毁前调用
* destoryed：组件销毁后调用
* activited：keep-alive 专属，组件被激活时调用
* deactivated：keep-alive 专属，组件被销毁时调用

## Vue的父子组件各自生命周期钩子函数的执行顺序？
* 加载渲染过程  
父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted

* 子组件更新过程  
父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated

* 父组件更新过程  
父 beforeUpdate -> 父 updated

* 销毁过程  
父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

## 在哪个生命周期内调用异步请求？
钩子函数created、beforeMount、mounted都可以调用。因为在这三个钩子函数中，data已经创建，可以将服务端端返回的数据进行赋值   
但是还是推荐created中调用：  
优点：
* 能更快获取到服务端数据，减少页面 loading 时间
* ssr不支持 beforeMount 、mounted 钩子函数，所以放在created中有助于一致性

## 什么阶段可以访问操作DOM.
beforeMount之后、mounted之前，其实Vue已经将编译好的模板挂载到页面上，所以在只能mounted中可以访问操作DOM

## 父组件可以监听子组件的生命周期吗？
有父组件Parent和子组件Child,如果父组件监听到子组件mounted，就做一些逻辑处理  
需要手动通过$emit触发父组件的事件，更简单的方式可以在父组件引用子组件时通过 @hook 来监听即可，如下所示：
````
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>

doSomething() {
   console.log('父组件监听到 mounted 钩子函数 ...');
},
    
//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
},    
    
// 以上输出顺序为：
// 子组件触发 mounted 钩子函数 ...
// 父组件监听到 mounted 钩子函数 ...     
````
## 谈谈你对keep-alive的理解
页面缓存需求：跳转到详情页面时，需要保持列表页的滚动条的深度，等返回的时候依然在这个位置  
keep-alive是个抽象组件（或称为功能型组件），实际上不会被渲染在DOM树中。它的作用是在内存中缓存组件（不让组件销毁），等到下次再渲染的时候，还会保持其中的所有状态，避免重新渲染 ，其有以下特性：
* 一般结合路由和动态组件一起使用，用于缓存组件；
* 提供 include 和 exclude 属性，两者都支持字符串或正则表达式， include 表示只有名称匹配的组件会被缓存，exclude 表示任何名称匹配的组件都不会被缓存 ，其中 exclude 的优先级比 include 高
* 对应两个钩子函数 activated 和 deactivated ，当组件被激活时，触发钩子函数 activated，当组件被移除时，触发钩子函数 deactivated