## 什么是Vue.nextTick()??
获取更新后的DOM的Vue方法  
注意：Vue实现响应式并不是数据发生变化之后DOM立即变化，而是按一定的策略进行 DOM 的更新，$nextTick 是在下次 DOM 更新循环结束之后执行延迟回调，在修改数据之后使用 $nextTick，则可以在回调中获取更新后的 DOM
https://www.cnblogs.com/goloving/p/9404643.html