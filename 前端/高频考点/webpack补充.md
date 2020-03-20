## webpack原理
https://juejin.im/entry/5b0e3eba5188251534379615
## webpack plugin原理
* webpack插件上本质上是一个函数，它的原型上存在一个名为apply的函数，webpack在初始化的时候(在触发environment事件之前)会执行这个函数，并将一个包含了webpack所有配置信息的compiler作为参数传递给apply函数
* 插件可以通过监听webpack不同阶段触发的hook,在不同时间段介入进行你想要做的操作
* 在插件中监听一些特定的事件(thisCompilation到afterEmit这个阶段的事件)，你可以拿到一个compilation对象，里面包含了各种编译资源，你可以通过操作这个对象对生成的资源进行添加和修改等操作
## Compiler 和 Compilation的区别：
* Compiler 代表了整个 Webpack 从启动到关闭的生命周期，它包含 Webpack 环境所有的的配置信息，包括options，loaders，plugins ，这个对象在 Webpack 启动时候就被实例化，它是全局唯一的
* Compilation 只是代表了一次新的编译，当 Webpack 以开发模式运行时，每当检测到一个文件变化，一次新的 Compilation 就会被创建
## webpack的打包流程
* 初始化配置对象，创建compiler对象
* 实例化插件，调用插件的apply方法，挂载插件的监听
* 从入口文件执行编译，按照文件类型调用相应的loader，在合适的时间调用plugin去执行
* 将编译后的代码组装成一个个代码快(chunk),并按依赖和配置确定输出内容
* 根据output把文件输出到对象的目录下
## webpack loader原理
本质上是一个node的模块，loader 导出一个函数，loader 会在转换源模块（resource）的时候调用该函数。在这个函数内部，我们可以通过传入 this 上下文给 Loader API 来使用它们
## webpack拆包(代码分割)
它允许将代码拆分成多个捆绑的包(bundle)，然后对这些捆绑的包进行按需加载或者并行加载，使用得当可以达到首次加载速度的提升  
使用方法：**DllPlugin插件**、**SplitChunksPlugin插件**
https://juejin.im/post/5dc6340ce51d4573042fb812