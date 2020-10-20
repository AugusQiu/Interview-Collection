## webpack配置代理解决cookie跨域失效
[掘金原文](https://juejin.im/post/6864588536633376776)  
ps:webpack-dev-server配置proxy主要用于开发环境，[生产环境上线的相关问题](https://segmentfault.com/q/1010000014182747)
### 要点
* 同源策略：同协议、同域名、同端口(子域名不同，也属于跨域)
* 跨域请求会**被浏览器拦截,请求被拦截是浏览器对跨域的处理，并非服务端对跨域的处理**

>同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安全功能,同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互

>webpack-dev-server插件原理：本地启动一个使用express的http服务器，并监听一个端口，这个本地服务器与客户端采用websocket通信。当原始文件发生改变，webpack-dev-server会实时编译。上线是请求实际的服务器，开发时是请求本地的服务器

* webpack-dev-server还可以充当代理服务器，在配置好proxy选项后，发送请求应该往本地地址+端口发，因为发送的请求和url同源，自然不会有跨域问题了，本质是本地服务器代理请求以达到欺骗浏览器的目的