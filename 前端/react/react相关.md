https://juejin.im/post/5d5f44dae51d4561df7805b4
## setState只在合成事件和钩子函数中是“异步”，在原生事件和setTimeout 中都是同步的
### DOM事件流
* 事件捕获阶段:当某个事件触发时，文档根节点最小接收到事件，然后根据DOM树结构自顶向下向具体绑定事件的原生
* 目标阶段:具体元素已经捕获事件。之后事件开始向根节点冒泡
* 事件冒泡
### 合成事件
React并不是将click事件直接绑定在dom上面，而是采用事件冒泡的形式冒泡到document上面，然后React将事件封装给正式的函数处理运行和处理    
如果DOM上绑定了过多的事件处理函数，整个页面响应以及内存占用可能都会受到影响。React为了避免这类DOM事件滥用，同时屏蔽底层不同浏览器之间的事件系统差异，实现了一个中间层——SyntheticEvent  
1.当用户在为onClick添加函数时，React并没有将Click时间绑定在DOM上面  
2.而是在document处监听所有支持的事件，当事件发生并冒泡至document处时，React将事件内容封装交给中间层SyntheticEvent（负责所有事件合成）  
3.所以当事件触发的时候，对使用统一的分发函数dispatchEvent将指定函数执行
###  合成事件绑定方式
< div onClick={this.handleClick}>点我呀！</>
### 原生事件
addEventListener