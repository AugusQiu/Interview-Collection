## webpack与grunt、gulp的不同？  
三者都是前端构建工具，grunt和gulp在早期比较流行，现在webpack相对来说比较主流，不过一些**轻量化**的任务还是会用gulp来处理，比如单独打包CSS文件等  
grunt和gulp是基于**任务和流(Task、Stream)**的,类似jquery,找到一个(或一类)文件，对其做一系列**链式操作**，更新流上的数据，整条链式操作构成一个任务，多个任务就构成了整个w构建流程  
webpack是**基于入口**的。webpack会自动地递归解析入口所需要加载地所有资源文件，然后用不同的Loader来处理不同的文件，用Plugin来扩展webpack功能  

## 与webpack类似的工具还有哪些？谈谈你为什么最终选择（或放弃）使用webpack？
基于入口的打包工具，主流的还有rollup和parcel  
从应用场景上来看:  
* webpack适用于大型复杂的前端站点的构建  
* rollup适用于基础库的打包，如:vue、react 
* parcel只适用于简单的实验性项目(推荐不使用)  

## 常见的Loader?解决的问题?
* file-loader：把文件输出到一个文件夹中，在代码中通过相对URL去引用输出的文件  
* url-loader：和file-loader类似，但是能在文件很小的情况下，以base64的方式把文件内容注入到代码中去  
* source-map-loader：加载额外的Source Map文件，以方便断点调试  
* image-loader：加载并且压缩图片文件  
* babel-loader： 把ES6转换成ES5  
* css-loader：加载CSS，支持模块化、压缩、文件导入等特性  
* style-loader：把CSS代码注入到js中，通过DOM操作去加载CSS  
* eslint-loader：通过ESlint检查js代码
## 常见的Plugin?解决的问题？
* define-plugin：定义环境变量
* commons-chunk-plugin：提取公共代码
* uglifyjs-webpack-plugin：通过UglifyES压缩ES6代码
## Loader和Plugin的不同?
不同的作用：  
* Loader直译为“加载器”。webpack将一切文件视为模块，但是**webpack原生只能解析js文件，如果想打包其它类型的文件，就会用到loader**。因此，loader的作用就是让webpack拥有加载和解析非javascript文件的能力  
* Plugin直译为“插件”。Plugin可以扩展webpack的功能，让webpack具有更多的灵活性。在webpack运行的生命周期中会广播出许多事件，Plugin可以监听这些事件，在合适的时机通过webpack提供的API改变输出结果。  

不同的用法：
* Loader在module.rules中配置。类型为数组，每一项都是一个Object,里面描述了对于什么类型的文件(test),使用什么加载(loader)和使用的参数(options)  
* Plugin在plugins中单独配置。类型为数组，每一项是一个plugin的实例，参数都通过构造函数传入  

## webpack的构建流程？从读取配置到输出文件
webpack的运行流程是一个**串行**(多个任务，各个任务按顺序执行，完成一个之后才能进行下一个)的过程，具体如下:  
1.初始化参数:从配置文件和Shell语句中**读取与合并参数**，得出最终参数  
2.开始编译:从上一步得到的参数**初始化Compiler(编译器)对象，加载所有配置的插件**，执行对象的run方法开始编译  
3.确定入口:根据配置中的entry找出所有的入口文件  
4.编译模块:从入口文件出发，**调用所有配置的Loader对模块进行翻译，再找出该模块依赖的模块**，再递归本步骤，直到所有入口依赖的文件都经过了该处理  
5.完成模块编译:在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系  
6.输出资源:根据入口和模块之间的依赖关系，**组织成一个个包含多个模块的Chunk,再把每个Chunk转换成一个单独的文件加入到输出列表**，这步是可以修改输出内容的最后机会  
7.输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。
## webpack的热更新是如何做到的？说明其原理
webpack的热更新又称为热替换(Hot Module Replacement).这个机制可以做到不用刷新浏览器，就将新变更的模块替换掉旧的模块  
server端和client端都做了处理工作:  
1.在webpack的watch模式下，文件系统中某一个文件发生修改，webpack监听到文件变化，根据配置文件对模块重新编译打包，并将打包后的代码通过简单的js对象保存在内存中  
2.webpack-dev-server与webpack进行接口交互。在这一步，主要是dev-server的中间件webpack-dev-middleware和webpack之间的交互，webpack-dev-middleware调用webpack暴露的API对代码进行监控，并且告诉webpack,将代码打包到内存中  
3.webpack-dev-server对文件变化的监控。不同于第一步，这里并不是监控代码变化而重新打包。当我们在配置文件中配置了 devServer.watchContentBase为 true 时，Server会监听配置文件夹中静态文件的变化，变化后通知浏览器端live reload(刷新)  
4.通过sockjs(webpack-dev-server的依赖)在浏览器端和服务端之间建立一个websocket长连接，将webpack编译打包的各个阶段的状态信息告知浏览器端，同时也包括第三步中Server监听静态文件变化的信息。  
浏览器根据这些socket信息进行不同的操作，当然服务端传递的最主要信息还是新模块的hash值，后面会根据这个hash值来进行模块热更替  
5.webpack-devserver/Client端不能够请求更新的代码，也不会执行热更新操作，而把这些工作交回webpack.webpack/hot/dev-server的工作就是根据webpack-dev-server/client传给他的信息一级dev-server的配置，来决定是刷新浏览器还是模块热更新
6.HotModuleReplacement.runtime是客户端HMR的中枢，他接收到上一步传递给它的新模块hash值，并通过 JsonpMainTemplate.runtime 向 server 端发送 Ajax 请求，服务端返回一个 json，该 json 包含了所有要更新的模块的 hash 值，获取到更新列表后，该模块再次通过 jsonp 请求，获取到最新的模块代码  
7.HotModulePlugin 将会对新旧模块进行对比，决定是否更新模块，在决定更新模块后，检查模块之间的依赖关系，更新模块的同时更新模块间的依赖引用  
8.当 HMR 失败后，回退到 live reload 操作，也就是进行浏览器刷新来获取最新打包代码
## 如何用webpack来优化前端性能？
用webpack来优化前端性能是指优化webpack的输出结果，让打包的最终结果在浏览器运行更快速高效  
* 压缩代码。删除多余的代码、注释、简化代码的写法等等。可以利用webpack的UglifyJsPlugin和ParallelUglifyPlugin来压缩JS文件， 利用cssnano（css-loader?minimize）来压缩css  
* 利用CDN加速。在构建过程中，将引用的静态资源路径修改为CDN上对应的路径。可以利用webpack对于output参数和各loader的publicPath参数来修改资源路径  
* 删除死代码（Tree Shaking）。将代码中永远不会走到的片段删除掉。可以通过在启动webpack时追加参数--optimize-minimize来实现  
* 提取公共代码