垃圾回收两种实现方式:**标记清除**和**引用计数**
# 标记清除
当变量进入执行环境时标记为“进入环境”，当变量离开执行环境时则标记为“离开环境”，被标记为“进入环境”的变量是不能被回收的，因为它们正在被使用，而标记为“离开环境”的变量则可以被回收
````
function func3 () {
      const a = 1
      const b = 2
      // 函数执行时，a b 分别被标记 进入环境
}

func3() // 函数执行结束，a b 被标记 离开环境，被回收
````
# 引用计数
统计引用类型变量声明后被引用的次数，当次数为 0 时，该变量将被回收
````
function func4 () {
      const c = {} // 引用类型变量 c的引用计数为 0
      let d = c // c 被 d 引用 c的引用计数为 1
      let e = c // c 被 e 引用 c的引用计数为 2
      d = {} // d 不再引用c c的引用计数减为 1
      e = null // e 不再引用 c c的引用计数减为 0 将被回收
}
````
但是引用技术，有个明显的缺陷，就是**循环引用**
````
function func5 () {
      let f = {}
      let g = {}
      f.prop = g
      g.prop = f
      // 由于 f 和 g 互相引用，计数永远不可能为 0
}

所以需要手动释放
f.prop = null
g.prop = null
````
现代浏览器使用的是标记清除
# 内存泄漏
内存泄漏:不再被需要的内存, 由于某种原因, 无法被释放
## 常见的内存泄露案例
1.函数内的全局变量
````
function fn(){
    name ='ha';
}
console.log(name) //'ha'
````

2.未销毁的定时器和回调函数造成内存泄露
````
function fn() {
    return 2
}

var oTxt = fn();
setInterval(function() {
    var oHtml = document.getElementById("test")
	    if(oHtml) {
	        oHtml.innerHTML = oTxt;
	    }
}, 1000); // 每 1 秒调用一次
````

3.闭包造成内存泄露  
4.DOM引用造成内存泄露
````
var elements = {
    txt: document.getElementById("test")
}

function fn() {
    elements.txt.innerHTML = "1111"
}
function removeTxt() {
    document.body.removeChild(document.getElementById('test'));
}
fn();
removeTxt()
console.log(elements.txt)
````
上述案例中, 即使我们对于 test 元素进行了移除, 但是仍然有对 test 元素的引用, 依然无法对齐进行内存回收.

另外需要注意的一个点是, 对于一个 Dom 树的叶子节点的引用. 举个例子: 如果我们引用了一个表格中的 td 元素, 一旦在 Dom 中删除了整个表格, 我们直观的觉得内存回收应该回收除了被引用的 td 外的其他元素. 但是事实上, 这个 td 元素是整个表格的一个子元素, 并保留对于其父元素的引用. 这就会导致对于整个表格, 都无法进行内存回收. 所以我们要小心处理对于 Dom 元素的引用.

作者：李赫feixuan
链接：https://juejin.im/post/5b684f30f265da0f9f4e87cf
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。