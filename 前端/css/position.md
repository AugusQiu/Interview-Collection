## position:relative
* 相对定位的元素并没有脱离文档流，只是在原有的位置上进行了视觉上的偏移
## position:absolute
* 绝对定位的元素会脱离文档流，绝对定位的元素会寻找有定位的父(祖先)元素作为参照物来偏移，**如果所有祖先元素都没有设置position为relative或者absolute，则该元素最终将对body进行偏移**
* 没添加绝对定位，宽度和高度对行内元素不起作用，添加之后，行内元素会变成块级元素进行偏移
* 父元素如果设置了padding和margin,子元素绝对定位，设置50%，**会把padding算进来，margin不会**
## float与相对定位共用
````js
<div class="main">
  <div class="box1"></div>
  <div class="box2"></div>
  <div class="box3"></div>
</div>

.main{
    width:400px;
    height:300px;
    position:relative;
}
.box1,.box2,.box3{
	width: 200px;
	height: 100px;
}
.box1{
	background: red;
}
.box2{
	background: orange;
	position: relative;
	left: 10px;
	top: 20px;
	float: left;
}
.box3{
	background: gray;
}
````
加上浮动与相对定位共用之后，box2脱离文档流，所以box3上移至原box2的位置，而box2依旧相对自己原来位置做偏移