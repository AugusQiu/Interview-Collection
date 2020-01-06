## 为什么要使用REST API?
大牛精辟总结：  
看url就知道要什么  
看http method就知道干什么  
看http status code就知道结果如何  
## 简要介绍如何使用
对于资源的具体操作，常用的HTTP动词(括号里是对于的SQL命令)
````
GET（SELECT）：从服务器取出资源（一项或多项）
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）
DELETE（DELETE）：从服务器删除资源
````
**具体设计使用**：
````
取得所有的dog
GET /api/dogs

取得一个特定的dog
GET /api/dogs/{id}

取得一个特定名字的dogs
GET /api/dogs?name=xxx

创建一个dog
POST /api/dogs

更改一个dog
PUT /api/dogs/{id}

删除一个dog
DELETE /api/dogs/{id}
````