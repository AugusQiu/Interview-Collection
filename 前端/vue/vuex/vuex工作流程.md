## 前言
> Vuex的状态存储也是响应式的，Vue组件从store中读取状态，若store中状态发生改变，那么组件也会高效更新  
“你不能直接改变store中的状态，改变状态的唯一途径就是显式地提交(commit) mutation”,**然而事实确是，可以直接使用 this.$store.state.变量=xxx 来修改，并且这也是响应式的**  
当然，可以设置严格模式避免直接修改，**使用commit提交到mutation的优点：vuex能够记录每一次state的变化记录，保存状态快照，实现时间漫游／回滚之类的操作**
## vuex相关属性
* state：数据源存放地
* getters：从源数据派生出来的数据
* mutations：提交更改数据的方法, **同步**
* actions：像一个装饰器，包裹mutations，使其可以**异步**
* modules：模块化Vuex
## 触发流程
1. vue组件里，通过dispatch来触发actions提交修改数据的操作
2. actions的commit来触发mutations来修改数据
3. mutations接收到commit的请求，就会自动通过Mutate来修改state
4. 最后由store触发每一个调用它的组件的更新