## 一.什么是Vuex?
Vuex是一个专为Vue.js应用程序开发的**状态管理插件**，它采用**集中式存储管理**应用的所有组件的状态，而更改状态的唯一方法是提交mutation  
## 二.Vuex解决了什么问题?
* 多个组件依赖同一个状态，多层嵌套组件的传参会非常繁琐，且兄弟组件间的状态传递无能为力
* 来子不同组件的行为需要变更同一状态
## 三.怎么引用Vuex?
* 先安装依赖:npm install vuex --save
* 在项目目录src中建立store文件夹
* 在store文件夹中新建index.js
````
import Vue  from 'vue';
import Vuex from 'vuex';
Vue.use(Vuex)

//开发环境debug为true,生产环境为false
const debug = process.env.NODE_ENV !=='production'
const store = new Vuex.Store({
    strict:debug,//在开发环境下都开启严格模式
    state:{
    },
    getters:{
    },
    mutations:{
    },
    actions:{
    }
})
export default store;
````
* main.js文件再引入Vuex
````
import Vue from 'vue';
import App from './App.vue';
import store from './store';
const vm = new Vue({
    store:store,
    render: h => h(App)
}).$mount('#app')
````
## 四.Vuex的5个核心属性是什么?
state、getters、mutations、actions、modules
## 五.Vuex中状态储存在哪里，怎么改变它？
存储在state中，改变Vuex中状态的唯一途径就是显示地提交(commit)mutation
## 六.Vuex中状态是对象时，使用时要注意什么？
因为对象是引用类型，复制后改变属性还是会影响原始数据，这样会改变state里面的状态，这是不被允许的，所以先用深度克隆复制对象，再修改
## 七.怎么在组件中批量使用Vuex的state状态?
使用mapState辅助函数，利用对象展开运算符将state混入computed对象中
````
import {mapState} from 'vuex'
export default{
    computed:{
        ...mapState(['price','number'])
    }
}
````
## 八.Vuex中要从state派生一些状态出来，且多个组件使用它，该怎么做？
使用getter属性，相当于Vue中的计算属性computed,只有原状态改变派生状态才会改变
````
const state = new Vuex.Store({
    state:{
        price:10,
        number:10,
        discount:0.7
    },
    getters:{
        total:state =>{
            return state.price * state.number
        },
        discountTotal:(state,getters)=>{
            return state.discount * getters.total
        }
    }
})
````
然后在组件中可以用计算属性computed，通过this.$store.getters.total来访问这些派生状态
````
computed:{
    total(){
      return this.$store.getters.total
    },
    discountTotal(){
     return this.$store.getters.discountTotal
    }
}
````
