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
