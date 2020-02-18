## 一些问题
*  如果实现了dom拖拽功能，但是在绑定拖拽事件的时候发现每当元素稍微移动一点便触发了大量的回调函数，导致浏览器直接卡死，这个时候怎么办？
* 如果给一个按钮绑定了表单提交的post事件，但是用户有些时候在网络情况极差的情况下多次点击按钮造成表单重复提交，如何防止多次提交的发生？

为了控制函数的执行次数出现了js的防抖和节流
## 函数防抖(debounce)
概念:**在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时**
### 应用场景
* 给按钮加函数防抖防止表单多次提交
* 对于输入框连续输入进行AJAX验证时，用函数防抖能有效减少请求次数
* 判断scroll是否滑到底部，滚动事件+函数防抖

总之，适合**多次事件一次响应**的情况
````
function debounce(fn,wait){
    var timer = null;
    return function(){
        var context = this;
        var args    = arguments
        if(timer){
            clearTimeout(timer);
            timer = null;
        }

        timer = setTimeout(function(){
            fn.apply(context,args)
        },wait)
    }
}
var fn = function () {
  console.log('boom')
}

setInterval(debounce(fn,500),1000) // 第一次在1500ms后触发，之后每1000ms触发一次
````
之所以返回一个函数，因为防抖本身更像是一个函数修饰，所以就做了一次函数柯里化。里面也用到了闭包，闭包的变量是timer
## 函数节流(throttle)
概念： 规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，**如果在同一个单位时间内某事件被触发多次，只有一次能生效**，
### 适用场景
* 游戏中的刷新率
* DOM元素拖拽
* Canvas画笔功能
````
function throttle(fn, gapTime) {
  let _lastTime = null;

  return function () {
    let _nowTime = + new Date()
    if (_nowTime - _lastTime > gapTime || !_lastTime) {
      fn();
      _lastTime = _nowTime
    }
  }
}

let fn = ()=>{
  console.log('boom')
}

setInterval(throttle(fn,1000),10)
````
总的来说，适合**大量事件按时间做平均分配**触发，**控制它发生的频率**