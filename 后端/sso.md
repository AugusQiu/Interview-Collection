## SSO(Single Sign On)
阿里云文章：https://developer.aliyun.com/article/636281  
在多个应用系统中，只需要登录一次，就可以访问其他相互信任的应用系统  
假设有三个系统，App1、App2、SSO。App1和App2没有登录模块，而SSO只有登录模块，没有其他的业务模块，当App1、App2需要登录时，将跳到SSO系统，SSO系统来完成登录，其他应用系统也就随之登录了
## 同域下的单点登录
问题1：
* Cookie是不能跨域的，如果Cookie的domain属性是sso.a.com, 在给app1.a.com和app2.a.com发送请求是带不上的  

解决：
* 设置Cookie**只能设置顶域和自己的域**，不能设置其他的域。所以可以将Cookie的域设置为顶域，即a.com  

问题2：
* sso、app1和app2是不同的应用，它们的session存在自己的应用内，是不共享的

解决
* 把3个系统的Session共享，共享Session的解决方案有很多，例如：Spring-Session

## 不同域下的单点登录（CAS官网标准流程）
1. 用户访问app系统，需要登录
2. 跳转到SSO登录系统，弹出用户登录页面
3. 填写用户名、密码，SSO系统认证后，将登录状态写入SSO的session, 浏览器中写入SSO域下的Cookie
4. SSO系统登录完成后会生成一个ST（Service Ticket），然后跳转到app系统，同时将ST作为参数传递给app系统
5. app系统拿到ST后，从后台向SSO发送请求，验证ST是否有效
6. 验证通过后，app系统将登录状态写入session并设置app域下的Cookie
