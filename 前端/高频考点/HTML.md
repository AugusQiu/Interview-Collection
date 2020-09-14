## 离线web应用 HTML5的离线储存怎么使用，解释工作原理
H5新增了'应用程序缓存'，所谓的离线缓存可不是像localstorage那样保存web应用程序的相关数据，
**它是将应用程序所需运行的所有文件（HTML，CSS，JavaScript、图片）缓存起来**      
在用户没有与因特网连接时，可以正常访问站点或应用，在用户与因特网连接时，更新用户机器上的缓存文件  
**原理**：HTML5的离线存储是基于一个新建的**appcache文件的缓存机制**(不是存储技术)，通过这个文件上的解析清单离线存储资源，之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示
如何使用：
* 页面html标签中加入一个manifest的属性，指向配置文件；
* 在cache.manifest文件的编写离线存储的资源
````
CACHE MANIFEST
#v0.11
CACHE:
js/app.js
css/style.css
NETWORK:
resourse/logo.png
FALLBACK:
//offline.html
````
* 在离线状态时，操作window.applicationCache进行需求实现
## document.ready和window.onload的区别
首先，ready肯定先于load执行，ready事件等DOM结构绘制完成之后就会执行(不包含图片等非文字媒体文件)，onload就要包含图片等文件在内的所有元素都加载完成
## title 和 alt属性分别有什么作用
title属性：为设置元素提供建议性的信息，比如给一个链接添加描述性文字，访问者知道那些链接将会指向何方  
alt属性：为了给那些不能看到你文档中图像的浏览者提供文字说明
## 离线缓存和浏览器缓存、本地存储的区别
* 离线缓存断网还是可以打开页面，浏览器缓存不行；离线缓存可以主动通知浏览器更新资源
* 离线缓存存储的是网页；本地存储是数据

## HTML5 为什么只写 !DOCTYPE html？
HTML5 不基于 SGML，因此不需要对DTD进行引用，但是需要doctype来规范浏览器的行为（让浏览器按照它们应该的方式来运行）  
而HTML4.01基于SGML,所以需要对DTD进行引用，才能告知浏览器文档所使用的文档类型  
ps:
* **SGML是标准通用标记语言，HTML和XML都SGML延伸转变而来的**
* XML和HTML的最大区别就在于 XML的标签是可以自己创建的，数量无限多，而HTML的标签都是固定的而且数量有限
* 还有一个是XHTML也是现在基本上所有网页都在用的标记语言，他其实和HTML没什么本质的区别标签都一样，用法也都一样，**XHTML比HTML更严格**，比如标签必须都用小写，标签都必须有闭合标签等
## data- 属性的作用？
data-是h5新增自定义的属性，可**存储数据信息** 存储的数据可以根据dataset获取 若浏览器不支持则采用getAttribute获取数据
## Web 标准以及 W3C 标准是什么？
WEB标准 不是某一个标准，而是一系列标准的集合(html、css、EMCAScript等)  
W3C对web标准提出了更加规范化的要求:标签字母要小写、标签要闭合
## Doctype作用？严格模式与混杂模式如何区分？它们有何差异？
<!DOCTYPE>声明叫做文件类型定义（DTD），声明的作用为了告诉浏览器该文件的类型。让浏览器解析器知道应该用哪个规范来解析文档。<!DOCTYPE>声明必须在 HTML 文档的第一行，这并不是一个 HTML 标签  
严格模式：w3c标准模式  
混杂模式：又称怪异模式或兼容模式，是指浏览器用自己的方式解析代码
## HTML 全局属性（Global Attribute）有哪些？
全局属性是所有HTML元素共有的属性; 它们可以用于所有元素，即使属性可能对某些元素不起作用:
data-xxx、id、class、hidden、draggable
## meta元属性
https://www.jianshu.com/p/205db1cbf43a
## meta viewport 是做什么用的，怎么写？
https://segmentfault.com/a/1190000008517628  
响应式布局,适配移动端的屏幕,整个页面不会缩成一团
````
在添加<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0"> 之后，移动端的 viewport 宽度会从默认的 980px 变成各个设备的 device-width
````
## 如何在 HTML 页面上展示 <div></div> 这几个字符？
如果要在网页中显示一些特殊的字符，就要用**转义**符号
````
&lt;div&gt;&lt;div&gt;
````
## 前端需要注意哪些 SEO?
* 合理的title、description、keywords
* 使用H5的语义化：标签使用更有意义，不要 滥用div和span  
* 少用iframe：搜索引擎不会抓取iframe中的内容
* 图片加上alt属性
## 你对网页标准和 W3C 重要性的理解?
让web发展的更“健康”，约束浏览器开发者遵循统一的标准，这样降低开发难度和开发成本，SEO也会更好做，也不会因为滥用代码导致各种BUG、安全问题，最终提高网站易用性
## POST 和 GET 方式提交数据有什么区别？
* GET 查询字符串拼接在url中，有长度限制，不安全 ;POST 查询数据放在请求体中
* GET在浏览器回退时是无害的，而POST会再次提交请求
* GET请求只能进行url编码，而POST支持多种编码方式
* 对参数的数据类型，GET只接受ASCII字符，而POST没有限制
## 在 input 里，name 有什么作用？
* 元素标识，服务端可以根据其Name取得元素提交得值
* 多选框radio根据name属性分组
## label 有什么作用？如何使用？
label标签有两个属性：一个是for，一个是accesskey  
for:表示这个Lable是为哪个控件服务的，Label标签要绑定了for指定HTML元素的ID或name属性，你点击这个标签的时候，所绑定的元素将获取焦点 ，点击label所包裹内容，自动指向for指定的id或name  
**accesskey则定义了访问这个控件的热键**( 所设置的快捷键不能与浏览器的快捷键冲突，否则将优先激活浏览器的快捷键)
## type=hidden 隐藏域有什么作用？
````
<input type="hidden" name="#" value="#">
````
如上：input元素被隐藏起来  
作用：  
* 帮助表单收集和发送信息，便于后端处理数据。用户点击提交数据的时候，隐藏域的信息也被一起发送到了后端
* 后端接收前端传来的数据，需要确认前端的身份
* 有时候一个网页中有多个form，我们知道多个form是不能同时提交的，但有时这些form确实相互作用，我们就可以在form中添加隐藏域来使它们联系起来
