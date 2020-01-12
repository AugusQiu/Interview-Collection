# 一，代码层面的优化
## 1.区分v-if和v-show的使用场景
v-if适用于很少改变条件，不需要频繁切换的场景；v-show则适用于需要非常频繁切换条件的场景
## 2.区分computed和watch的使用场景
computed适用于数值计算，并且依赖其他数据，有用到缓存的特性；watch用于开销较大的异步操作
## 3.v-for遍历必须为item添加key，且避免同时使用 v-if
* 列表数据进行遍历渲染，需要为每一项item设置唯一key值，方便diff算法精确定位
* v-for遍历避免同时使用v-if,必要时可用computed
````
//不推荐
<ul>
   <li v-for="user in users"
       v-if ="user.isActive"
       :key ="user.id">
       {{ user.name }}
   </li>
</ul>

//推荐
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id">
    {{ user.name }}
  </li>
</ul>

computed: {
  activeUsers: function () {
    return this.users.filter(function (user) {
	 return user.isActive
    })
  }
}
````
## 4.freeze冻结对象，用于长列表数据展示
我们都知道Vue会通过Object.defineProperty对数据进行劫持，来实现视图响应数据的变化，但有时候**组件就是单纯的数据展示，不需要任何改变**，在大量数据展示的情况下，这能够很明显的减少组件初始化的时间  
通过Object.freeze来冻结一个对象，禁止被劫持，一旦被冻结就不能再被修改
````
export default{
    data:()=>({
      users:{}
    }),
    async created(){
        const users = await axios.get("/api/users");
        this.users  = Object.freeze(users);
    }
}
````
## 5.事件消耗，手动移除js监听器
Vue 组件销毁时，会自动清理它与其它实例的连接，解绑它的全部指令及事件监听器，但是仅限于组件本身的事件。如果在js内使用 addEventListener等方式是不会自动销毁的，我们需要在组件销毁时手动移除这些事件的监听，以免造成内存泄露
````
created() {
  addEventListener('click', this.click, false)
},
beforeDestroy() {
  removeEventListener('click', this.click, false)
}
````
## 6.图片资源懒加载
对于图片过多的页面，为了加速页面加载速度，所以很多时候我们需要**将页面内未出现在可视区域内的图片先不做加载， 等到滚动到可视区域**后再去加载。这样对于页面加载性能上会有很大的提升，也提高了用户体验。我们在项目中使用 Vue 的 vue-lazyload 插件
````
//安装插件
npm install vue-lazyload --save-dev

//在入口文件 man.js 中引入并使用
import VueLazyload from 'vue-lazyload'
Vue.use(VueLazyload)

//在vue文件中将img标签的src属性直接改v-lazy 
<img v-lazy="/static/imgs/1.png">
````
## 7.路由懒加载
Vue是SPA单页面应用，可能会有很多的路由引入 ，这样使用 webpcak 打包后的文件很大，当进入首页时，加载的资源过多，页面会出现白屏的情况  
如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应的组件。这样会大大提高首屏显示的速度，但是可能其他的页面的速度就会降下来
````
const Foo = () => import('./Foo.vue')
const router = new VueRouter({
  routes: [
    { path: '/foo', component: Foo }
  ]
})
````
## 8.按需引入第三方插件
如果直接引入整个第三方插件，会导致项目的体积过大；那element-ui为例，我们可以借助 babel-plugin-component 按需引入  
(1):先安装babel-plugin-component
````
npm install babel-plugin-component -D
````
(2)修改.babelrc文件
````
{
  "presets": [["es2015", { "modules": false }]],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
````
(3)在main.js中引入部分组件：
````
import Vue from 'vue';
import { Button, Select } from 'element-ui';

 Vue.use(Button)
 Vue.use(Select)
````
## 9.优化无限列表性能
如果你的应用存在非常长或者无限滚动的列表，就需要采用**窗口化**的技术来优化性能，先渲染一部分区域的内容,减少重新渲染组件和创建dom节点的时间.可以参考开源项目**vue-virtual-scroll-list**和 **vue-virtual-scroller**

# 二,Webpack层面的优化
## 1.webpack对图片进行压缩
在vue项目中，可以在**webpack.base.conf.js**中的url-loader设置 limit 大小来对图片进行除了。小于limit的图片就转化为base64格式，其余不做操作  
那么对于有些较大的图片资源，在请求时，加载就会很慢，我们可以用 **image-webpack-loader**来压缩图片  
(1):安装image-webpack-loader
````
npm install image-webpack-loader --save-dev
````
(2):在webpack.base.conf.js中进行配置
````
{
  test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
  use:[
    {
    loader: 'url-loader',
    options: {
      limit: 10000,
      name: utils.assetsPath('img/[name].[hash:7].[ext]')
      }
    },
    {
      loader: 'image-webpack-loader',
      options: {
        bypassOnDebug: true,
      }
    }
  ]
}
````
## 2.减少es6转es5的代码冗余
babel插件会在将es6代码转换成es5代码时，会**注入一些辅助函数**，例如下面的es6代码：
````
class HelloWebpack extends Component{...}

//上面的这段ES6代码要转换成能正常运行的ES5代码，需要两个辅助函数
babel-runtime/helpers/createClass //用于实现class语法
babel-runtime/helpers/inherits //用于实现extends语法
````
默认情况下，babel会在每个输出文件中内嵌这些依赖的辅助函数代码，**如果多个源代码文件都依赖这些辅助函数，那么这些辅助函数的代码将会出现很多次**，造成代码冗余。  
为了不让这些辅助函数的代码重复出现，可以在依赖它们时通过 require('babel-runtime/helpers/createClass') 的方式导入，这样就能做到只让它们出现一次。**babel-plugin-transform-runtime**插件就是用来实现这个作用的，将相关辅助函数进行替换成导入语句，从而减小 babel 编译出来的代码的文件大小  
(1):安装babel-plugin-transform-runtime:
````
npm install babel-plugin-transform-runtime --save-dev
````
(2):修改.babelrc配置文件：
````
"plugins":[
  "transform-runtime"
]
````
## 3.提取公共代码
如果项目中没有去将每个页面的第三方库和公共模块提取出来，则项目会存在以下问题：
* 相同的资源被重复加载，浪费用户流量和服务器的成本
* 每个页面需要加载的资源过大，导致首屏加载慢，影响用户体验

所以我们要将多个页面的公共代码抽离成单独的文件，来优化。webpack内置了专门用于图区多个Chunk中公共部分的插件 **CommonsChunkPlugin**
````
//所有在package.json里面依赖的包，都会被打包进 vendor.js这个文件中
new webpack.optimize.CommonsChunkPlugin({
  name:'vendor',
  minChunks:function(module,count){
     return (
      module.resource &&
      /\.js$/.test(module.resource) &&
      module.resource.indexOf(
        path.join(__dirname, '../node_modules')
      ) === 0
    );
  }
}),

//抽取出代码模块的映射关系
new webpack.optimize.CommonsChunkPlugin({
   name: 'manifest',
   chunks: ['vendor']
})
````
## 4.模板预编译
预编译模板最简单的方式就是使用单文件组件(.vue)——相关的构建设置会自动把预编译处理好，所以构建好的代码已经包含了编译出来的渲染函数而不是原始的模板字符串  
如果你使用 webpack，并且喜欢分离 JavaScript 和模板文件，你可以使用 vue-template-loader，它也可以在构建过程中把模板文件转换成为 JavaScript 渲染函数

## 5.优化SourceMap
项目打包时，会将开发中的多个文件代码打包到一个文件中，并且经过压缩、去掉多余的空格、babel编译化后，最终得到的代码才会用于线上环境，但是这样的代码和源代码肯定会有很大的差别。当有bug,我们只能定位到压缩处理后的代码位置，无法定位到开发环境中的代码，对于开发来说很不友好，所以sourceMap出现了  
开发环境推荐：   cheap-module-eval-source-map  
生产环境推荐：   cheap-module-source-map

## 6.构建结果输出分析
Webpack输出的代码可读性非常差而且文件还大。为了更简单、直观地分析输出结果，社区中出现了许多**可视化分析工具**。这些工具以图形的方式将结果更直观地展示出来，让我们快速了解问题所在  
介绍一个分析工具:**webpack-bundle-analyzer**
````
//在项目中 webpack.prod.conf.js配置
if(config.build.bundleAnalyzerReport){
   var BundleAnalyzerPlugin =   require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
  webpackConfig.plugins.push(new BundleAnalyzerPlugin());
}

//执行npm run build --report
````
# 三.基础的Web技术优化
## 1.开启gzip压缩
gzip是GNUzip的缩写，最早用于UNIX系统的文件压缩。HTTP协议上的gizp编码是一种用来改进web应用程序性能的技术，**web服务器和客户端(浏览器)必须同时支持gzip**.  
目前主流的浏览器：chrome、firefox、IE等都支持该协议；常见的服务器如：Apache、Nginx、IIS同样支持，gzip压缩效率非常高，通常可以达到70%的压缩率，也就是说，如果你的网页有30K，压缩之后就变成了9K左右  
服务端以node的express框架为例，来开启gzip:
````
//安装compression
npm install compression --save

//添加代码逻辑
var compression = require('compression');
var app         = express();
app.use(compression());
````
重启服务，f12控制台查看服务端返回报文，**response header显示Content-Encoding:gzip**
## 2.浏览器缓存
为了提高用户加载页面的速度，对静态资源进行缓存是非常必要的，根据是否需要重新向服务器发起请求来分类，将HTTP缓存规则分为两大类（强制缓存，协商缓存）
## 3.CDN的使用
浏览器从服务器上下载css、js和图片等文件都要先连接服务器，而大部分服务器的带宽有限，如果超过限制，网页就半天反应不过来。
而 CDN 可以**通过不同的域名来加载文件，从而使下载文件的并发连接数大大增加**，且CDN 具有更好的可用性，更低的网络延迟和丢包率
## 4.使用Chrome Performance查看性能瓶颈
Chrome的Performance面板可以**录制一段时间内的js执行细节及时间**  
使用Chrome开发者工具分析页面性能的步骤如下:  
1.打开Chrome开发者工具，切换到Performance面板  
2.点击Record开始录制  
3.刷新页面或展开某个节点  
4.点击Stop停止录制