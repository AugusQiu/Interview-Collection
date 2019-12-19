# 盒子模型
盒模型(box model):每个html标签都可以理解为一个方块，大方块里面又可以包含多个小方块，如同盒子层层包裹  
盒模型四大元素:边距(margin)、边框(border)、填充(padding)、实际内容(content)  
当我们指定一个css元素的宽度和高度属性时，它只是指定了content的宽度和高度，总宽度还要加上左右边距、左右边框、左右填充(总高度类同)  
css3新属性:box-sizing,当该属性设置为border-box时，此时再对元素指定宽度和高度，它就会包括 padding 和 border 。通过从已设定的宽度和高度分别减去边框和内边距才能得到content的宽度和高度  

# BFC
常见三种定位方案:  
1,普通流(normal flow):默认布局，元素从上而下布局，行内元素水平从左向右排列，直到行被占满换行，块级元素则被渲染为完整的一个新行  
2,浮动流(float):元素先按照普通流的位置出现，然后根据浮动的方向尽可能的向左或向右偏移  
3,绝对定位(absolute positioning):绝对定位布局中，元素会脱离文档流，因此绝对定位不会对其兄弟元素造成影响  
relative和absolute定位区别在于relative不会脱离文档流，在文档流中位置依然会存在，absolute会脱离文档流，位置不会存在；float**半脱离**文档流，其在文档流中会占有一定位置，但是其它盒子元素会无视这个位置，只有盒子元素里的文本会让出位置，环绕在周围，有点类似word"文字环绕"的效果;fixed根据浏览器窗口定位,脱离文档流，且原有占有空间不会被保留  

BFC(block formatting contexts):块级格式化上下文，属于普通流，具有BFC特性的元素可以看作是隔离了的独立容器，容器里面的元素无论如何都不会从布局上影响到外面的元素  
如何设置？满足下面条件即可:  
1,body根元素  
2,浮动元素:float除none以外的值  
3,绝对定位:position(absolute、fixed)  
4,display为inline-block、table-cells、flex  
5,overflow除了visible以外的值(hidden、auto、scroll)  

# 元素居中的几种方法
1，水平居中:margin:0 auto;  
2，绝对定位水平垂直居中:  
  父元素:{  
     &nbsp;&nbsp;width:400px;  
     &nbsp;&nbsp;height:400px;  
     &nbsp;&nbsp;position:relative;  
  }  
  子元素:{  
     &nbsp;&nbsp;width:200px;  
     &nbsp;&nbsp;height:150px;  
     &nbsp;&nbsp;position:absolute;  
     &nbsp;&nbsp;top:50%;  
     &nbsp;&nbsp;left:50%;  
     &nbsp;&nbsp;margin-left:-100px;  
     &nbsp;&nbsp;margin-top:-75px;  
  }  
  或者:{  
     &nbsp;&nbsp;width:300px;  
     &nbsp;&nbsp;height:200px;  
     &nbsp;&nbsp;top: 50%;  
     &nbsp;&nbsp;left: 50%;  
     &nbsp;&nbsp;position:absolute;    
     &nbsp;&nbsp;transform: translate(-50%, -50%);    
  }  
  3,flex布局居中  
  父元素:{  
     &nbsp;&nbsp;display:flex;  
     &nbsp;&nbsp;align-items:center;  
     &nbsp;&nbsp;justify-content:center;  
  }  
  子元素:{  
    &nbsp;&nbsp;width:100px;  
    &nbsp;&nbsp;height:100px;  
  }  

# flex布局
css3中弹性布局中的元素具有伸展和收缩自身的能力，只要给父容器设置display:flex,子元素就具有了伸缩性  
flex-direction:row/row-reverse/column/column-reverse  
justify-content:定义了元素在主轴上如何对齐  
align-items:交叉轴如何对齐  
align-content:stretch:交叉轴特有，适用于多行的flex容器，并且当交叉轴上有多余空间使flex容器内的元素沿交叉轴拉伸对齐



# Grid布局
网格布局与flex布局有相似性，但是flex是轴线布局，针对的是子元素在轴线上的排列，可以看作是一种一维布局，而Grid是将容器划分成行和列，产生各个单元格，再指定项目所在的单元格，可以看作是**二维布局**(注:容器container此处指采用网格布局的区域，项目item就是容器内部采用网格定位的子元素)  
.container{  
     &nbsp;&nbsp;display: grid;  
     &nbsp;&nbsp;grid-template-columns: 100px 100px 100px;  
     &nbsp;&nbsp;grid-template-rows: 100px 100px 100px;  
}  
具体使用可参考阮一峰的教程:
http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html

