# patch
patch也可以叫做patching算法，新DOM树并不是暴力覆盖旧DOM，而是比较新旧vnode之间的不同，根据对比结果找出需要更新的节点进行更新。其实际作用是在现有的DOM上进行修改来实现更新视图的目的
# Vue既然可以通过数据劫持可以精准探测数据变化,为什么还需要虚拟DOM进行diff检测差异?
现代前端框架有两种方式侦测变化，**一种是pull,一种是push**
* pull:React,用setStateAPI显式更新，然后React会进行一层一层的Virtual Dom Diff操作找出差异,然后Patch到DOM上,React从一开始就不知道到底是哪发生了变化,只是知道「有变化了」,然后再进行比较暴力的Diff操作查找「哪发生变化了」，另外一个代表就是Angular的脏检查操作
* push:Vue,vue程序初始化，会利用data的getter属性进行依赖收集，一旦数据发生变化,响应式系统就会立刻得知,因此Vue是一开始就知道是「在哪发生变化了」,通常一个绑定一个数据就需要一个Watcher,一但我们的绑定细粒度过高就会产生大量的Watcher,这会带来内存以及依赖追踪的开销,而细粒度过低会无法精准侦测变化,因此Vue的设计是选择中等细粒度的方案,在组件级别进行push侦测的方式,也就是那套响应式系统,通常我们会第一时间侦测到发生变化的组件,然后在组件内部进行Virtual Dom Diff获取更加具体的差异,而Virtual Dom Diff则是pull操作,Vue是push+pull结合的方式进行变化侦测的
# Vue中的key有什么用？
* key是vnode标记的唯一id,通过这个key,diff操作可以更准确、更快速
* 不加key,vue会选择复用节点，导致之前节点的状态会被保留下来
