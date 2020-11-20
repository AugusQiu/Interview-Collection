## z-index
* z-index属性值并不是在任何元素上都有效果，**它仅在定位元素（定义了position，且属性值非static值的元素）上有效果**
* 判断元素在Z轴上的堆叠顺序，不仅仅是直接比较两个元素的z-index值的大小，这个堆叠属性实际由元素的**层叠上下文、层叠等级**共同决定
## 层叠上下文(stacking context)
层叠上下文，是HTML中一个三维的概念，在CSS2.1规范中，每个盒模型的位置是三维的，分别是平面画布上的X轴、Y轴以及表示层叠的Z轴  
>如果一个元素含有层叠上下文，(也就是说它是层叠上下文元素)，我们可以理解为这个元素在Z轴上就“高人一等”，最终表现就是它离屏幕观察者更近
## 层叠等级(stacking level)
* 普通元素的层叠等级优先由其所在的层叠上下文决定
* **层叠等级的比较只有在当前层叠上下文中才有意义，不同层叠上下文中比较层叠等级是没有意义的**
## 如何产生“层叠上下文”
层叠上下文也基本上是有一些特定的CSS属性创建的，一般有3种方法
* 文档html根元素，称为“根层叠上下文”
* position为absolute、relative且z-index值不为auto
* position为fixed
* flex盒子，且z-index值不为auto
* opacity<1
* css3的一些新属性：transform、filter、clip-path等

<font color="red">注意：！！！子级层叠上下文的 z-index 值只在父级中才有意义。子级层叠上下文被自动视为父级层叠上下文的一个独立单元</font>
<img src="https://developer.mozilla.org/@api/deki/files/913/=Understanding_zindex_04.png" width="500" height="400">
````js
//上图层叠上下文层级
Root
   DIV #1
   DIV #2
   DIV #3
       DIV #4
       DIV #5
       DIV #6
````
**诚然，div#4的z-index为6，但是它是div#3的子层叠上下文元素，父元素div#3被压在div#1下面，即使div#4的z-index值比div#1大，它也只能被压在div#1下面，完美诠释“一人得道，鸡犬升天”、“狗仗人势”**








