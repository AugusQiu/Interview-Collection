## vue-router路由模式有哪几种？
**路由模式帮助前端单页面内更新视图，而不需要向后端发送请求**
三种：hash、history、abstract
````
switch (mode) {
  case 'history':
	this.history = new HTML5History(this, options.base)
	break
  case 'hash':
	this.history = new HashHistory(this, options.base, this.fallback)
	break
  case 'abstract':
	this.history = new AbstractHistory(this, options.base)
	break
  default:
	if (process.env.NODE_ENV !== 'production') {
	  assert(false, `invalid mode: ${mode}`)
	}
}
````
* hash:使用url的hash值来作为路由，支持所有浏览器，包括不支持HTML5 History Api的浏览器
* history:依赖HTML5 History API和服务器配置。具体可以查看HTML5 History模式
* abstract:支持所有JavaScript运行环境，Node.js 服务器端。如果发现没有浏览器的API，路由会自动强制进入这个模式

## vue-router中常用的hash和history路由模式的实现原理
1.hash路由模式的实现原理
早期前端路由的实现就是基于**location.hash**来实现的，location.hash的值就是#符号后面的内容
````
https://www.baidu.com#search  //hash值:search
````
hash路由模式的实现主要是基于下面几个特性：
* url中的hash只是客户端的一种状态，即**向服务端发送请求时，hash部分不会被发送**
* hash值的改变，都会在浏览器的访问历史中增加一个记录，根据记录，可以通过浏览器的回退、前进来控制hash的切换 
* 可以通过a标签，并设置href属性，当用户点击这个标签，url的hash值就会发生变化；或者用js来对location.hash进行赋值，来改变url的hash值
* 可以使用**hashchange事件**来监听hash值的变化，从而对页面进行跳转(渲染)  

2.history路由模式的实现原理  
h5提供了History API来实现路由的变化。其中，**history.pushState()**和**history.replaceState()**两个API可以在**不进行刷新**的情况下，操作浏览器的历史记录，前者是新增一个历史记录，后者是直接替换当前的历史记录  
history路由模式的实现主要基于存在下面几个特性：
* pushState和repalceState两个API来操作实现URL的变化 
* 可以使用**popstate事件**来监听url的变化，从而对页面进行跳转(渲染)
* history.pushState()或history.replaceState()不会触发popstate事件，这时我们需要手动触发