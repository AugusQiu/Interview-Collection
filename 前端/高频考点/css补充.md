## link和@import的区别？
* link是XHTML标签，除了加载CSS外，还可以定义RSS等其他事务；@import属于CSS范畴，只能加载CSS
* link引用CSS时，在页面载入时同时加载；@import需要页面网页完全载入以后加载。
所以会出现一开始没有css样式，闪烁一下出现样式后的页面(网速慢的情况下)
* link支持使用Javascript控制DOM去改变样式；而@import不支持
## src与href的区别
* href指向**网络资源**所在位置，建立和当前元素（锚点）或当前文档（链接）之间的链接，用于超链接
* src指向外部的资源位置，指向的内容将会嵌入到文档中当前标签所在位置。在请求 src 资源时会将其指向的资源下载并应用到文档内，例如 js 脚本，img 图片和 frame 等元素
##  什么是 CSS 继承？哪些属性能继承，哪些不能？
子级不用设置css属性，会自动继承父级设置的，可以减少CSS代码，便于维护
### 自动继承的属性
字体系列属性(font)、文本系列(text)、列表相关(list)、color、visibility
## 空(void)元素
没有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的，也就是说空元素没有闭合标签的  
````
<area> <br> <hr> <img><input><meta><link>
````
## 在什么场景下会出现外边距合并？如何合并？如何不让相邻元素外边距合并？给个父子外边距合并的范例？
https://www.jianshu.com/p/141fbe7d78a5
## line-height: 2; 和 line-height: 200%; 有什么区别?
line-height: 2和line-height: 200%都表示行高是字体大小的2倍，但是它们是有区别的。当它们写在父容器中时，子元素的字体大小不一样的时候，区别就体现出来了
* line-height: 2 写在父容器中，那么子元素的行高都是自身高度的2倍，是相对大小。
**子元素的字体大小不同，行高也会不同**
* line-height: 200% 写在父容器中，那么浏览器会立刻计算出行高的具体值，假如父容器的默认字体大小是16px，那么计算得到的行高就是2×16px=32px，子元素的行高都会继承这个32px，是固定大小。
**子元素的字体大小不同，行高都是固定某个值**
## 一个页面有一排高度不一样的产品图，这时如果我们用 inline-block ，怎样使他们“顶端对齐”？
vertical-align: top;
## 可以通过哪些方法优化 CSS3 Animation 渲染？
减少回流和重绘，优先选择transform，尽量不要使用height，width，margin和padding
## 如何让块级元素水平居中？如何让行内元素水平居中？如何让 inline-block 元素水平居中？
块级：margin:0 auto;  
行内：text-align:center;
inline-block:父元素设text-align:center;  
flex:justify-content:center;
## 单行文本垂直居中
line-height=height；
## 多行文本垂直居中
通过设置父元素table，子元素table-cell和vertical-align:middle
## 响应式布局
媒体查询、rem、百分比布局、视口单位(vw/vh)
https://juejin.im/post/5caaa230e51d452b672f9703
## css3新特性
新添加了很多选择器、transition过渡、Animation动画、border-radius、box-shadow、background-size、background-image、text-shadow、
## 渐进增强和优雅降级分别是什么意思？
* 渐进增强（Progressive Enhancement）：一开始就针对低版本浏览器进行构建页面，完成基本的功能，然后再针对高级浏览器进行效果、交互、追加功能达到更好的体验  
* 优雅降级（Graceful Degradation）：一开始就构建站点的完整功能，然后针对浏览器测试和修复。比如一开始使用 CSS3 的特性构建了一个应用，然后逐步针对各大浏览器进行 hack 使其可以在低版本浏览器上正常浏览
## 什么是 CSS hack?
CSS hack就是运用一些小技巧，利用不同浏览器的特性（或者BUG）来使我们的代码兼容不同的浏览器
````
<!--[if IE 6]>
<p>You are using Internet Explorer 6.</p>
<![endif]-->
<!--[if !IE]><!-->
<script>alert(1);</script>
<!--<![endif]-->
<!--[if IE 8]>
<link href="ie8only.css" rel="stylesheet">
<![endif]-->
````
## CSS Reset 是什么？CSS 预编译器是什么？后编译器（PostCSS）是什么？
* css reset 即**重置浏览器标签的样式表**:现在所使用的主流浏览器对一些标签的默认属性上并没有做到统一，所以我们偶尔会发现，某个页面在chrome浏览器上很正常，到了firefox上面却有意想不到的偏差。当然编程人员不喜欢这样的兼容性问题的，而reset.css就是解决默认样式不兼容问题的办法之一
* CSS 预处理器定义了一种新的语言，其基本思想是，用一种专门的编程语言，为 CSS 增加了一些编程的特性，将 CSS 作为目标生成文件，然后开发者就只要使用这种语言进行编码工作
* PostCSS是一个使用JavaScript插件来转换CSS的工具。它将CSS转换成抽象语法树(AST)，也就是JavaScript可以操作的一种数据形式。基于JavaScript的PostCSS插件可以执行不同的代码操作。PostCSS本身并没有改变你的CSS，它请允许插件执行和转换你的代码
## CSS Reset 和 Normalize.css 有什么区别？
相比于传统的CSS reset，Normalize.css是一种现代的、为HTML5准备的优质替代方案  
https://www.cnblogs.com/webpush/p/4974063.html
## 如何让 Chrome 浏览器显示小于 12px 的文字？
transform:scale()
## CSS 预处理器的比较：Less、Sass？
* Sass和Less语法严谨、Stylus相对自由。因为Less长得更像 css，所以它可能学习起来更容易
* Sass 和 Stylus 都具有类语言的逻辑方式处理：条件、循环等，而 Less 需要通过When等关键词模拟这些功能，这方面 Less 比不上 Sass 和 Stylus
* Less 在丰富性以及特色上都不及 Sass 和 Stylus
## 常见浏览器兼容性问题？
* 浏览器默认样式差异
* chrome下会让小于12px的文本字体默认为12px显示
* 不同浏览器默认的margin、padding不同
* 图片默认有间距
https://zhuanlan.zhihu.com/p/58515064
## CSS编码规范？
https://juejin.im/entry/5967040451882568af7f426f
## CSS3过渡和动画
````
//过渡
div{
  width: 100px;
  height: 100px;
  background-color: orange;
  margin: 200px auto;
  transition: all 1000ms linear 500ms;
}

div:hover{
   transform: rotate(60deg);
}

//动画
div
{
	width:100px;
	height:100px;
	background:red;
	animation:myfirst 5s;
}
@keyframes myfirst
{
	0%   {background:red;}
	25%  {background:yellow;}
	50%  {background:blue;}
	100% {background:green;}
}
````
## @import和link导入样式表的区别
* @import是 CSS 提供的语法规则，只有导入样式表的作用；link是HTML提供的标签，不仅可以加载 CSS 文件，还可以定义 RSS、rel 连接属性等
* 加载页面时，link标签引入的 CSS 被同时加载；@import引入的 CSS 将在页面加载完毕后被加载
