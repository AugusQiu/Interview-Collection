https://juejin.im/post/5be4f163f265da61483b1b08
# 手写jsonp
## 原理
1.利用script标签的src属性来实现跨域  
2.前端将函数方法作为参数传递到服务器端，然后由服务器端注入参数之后再返回，实现服务器端向客户端通信  
3.只支持get方法
## 代码示例
````
//前端代码
function jsonp(req){
    var script = document.createElement('script');
    var url = req.url+'?callback='+req.callback;
    document.getElementsByTagName('head')[0].appendChild(script);
}

function hello(res){
    alert('hello ' + res.data);
}
jsonp({
    url:'',
    callback:hello
})
````
````
//服务器端
var http = require('http');
var urllib = require('url');

var port = 8080;
var data = {'data':'world'};

http.createServer(function(req,res){
    var params = urllib.parse(req.url,true);
    if(params.query.callback){
        console.log(params.query.callback);
        //jsonp
        var str = params.query.callback + '(' + JSON.stringify(data) + ')';
        res.end(str);
    } else {
        res.end();
    }
    
}).listen(port,function(){
    console.log('jsonp server is on');
});
````
# 手写ajax
常见属性:
* status:响应的HTTP状态码，如：200、304、404等
* statusText：HTTP状态说明
* timeout：设置请求超时时间
* readyState：请求/响应过程的当前活动阶段
````
xhr.readyState==0 尚未调用 open 方法
xhr.readyState==1 已调用 open 但还未发送请求（未调用 send）
xhr.readyState==2 已发送请求（已调用 send）
xhr.readyState==3 已接收到请求返回的数据
xhr.readyState==4 请求已完成
````
ajax原生实现
````
//请求数据格式化
function formateData(data)
{
    let arr = [];
    for(let key in data){
        //避免有&,=,?字符，对这些字符进行序列化
        arr.push(encodeURIComponent(key) + '=' + data[key])
    }
    return arr.join('&');
}

function ajax(params){
    //先对params请求参数进行处理，防止为null
     params = params || {};
     params.data = params.data || {};
    
     params.type = (params.type || 'GET').toUpperCase();
     params.data = formateData(params.data);

     //如果是在ie6浏览器，那么XMLHttoRequest是不存在的，应该调用ActiveXObject
     let xhr = XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHTTP');

      if (params.type === 'GET') {
        xhr.open(params.type, params.url + '?' + params.data, true);
        xhr.send();
      }else {
        xhr.open(params.type, params.url, true);
        xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
        xhr.send(params.data);
     }
    
      //这里有两种写法，第一种写法：当xhr.readyState===4的时候，会触发onload事件，直接通过onload事件 进行回调函数处理
      xhr.onload = function () {
        if (xhr.status === 200 || xhr.status === 304 || xhr.status === 206) {
            var res;

            if (params.success && params.success instanceof Function) {
                res = JSON.parse(xhr.responseText);
                params.success.call(xhr, res);
            }
        } else {
            if (params.error && params.error instanceof Function) {
                res = xhr.responseText;
                params.error.call(xhr, res);
            }
        }

    }

}
````