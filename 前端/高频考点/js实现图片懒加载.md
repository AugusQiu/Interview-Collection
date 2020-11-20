## js原生实现图片懒加载
### 要点
* 图片地址先存在data-src属性里，判断滚动距离，当图片出现在可视区域里，再从data-src取值赋值给图片的src属性
* 判断图片是否在可视区域：
1. offsetTop-scrollTop < clientHeight
2. 通过getBoundingClientRect().top获取图片到可视区顶部的距离，判断是否小于innerHeight
### 代码
````
var imgs = document.querySelectorAll('img')

// offsetTop是元素与其父元素顶部相比的偏移距离，循环获取直至页面顶部
function getTop(e){
    let T = e.offsetTop;
    while(e = e.offsetParent){
        T += e.offsetTop
    }
    return T
}
 
function lazyLoad(imgs){
    let H = document.documentElement.clientHeight;
    let S = document.documentElement.scrollTop || document.body.scrollTop
    for (var i = 0; i < imgs.length; i++) {
         if (getTop(imgs[i] - S < H)) {
                imgs[i].src = imgs[i].getAttribute('data-src');
          }
    }
}

window.onload = window.onscroll = function(){
    lazyLoad(imgs)
}
````