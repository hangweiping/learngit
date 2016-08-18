# [CSS设计指南之浮动与清除](http://www.cnblogs.com/JustYong/p/4396098.html)

浮动意思就是把元素从常规文档流中拿出来，浮动元素脱离了常规文档流之后，原来紧跟在其后的元素就会在空间允许的情况下，向上提升到与浮动元素平起平坐。

一.浮动

CSS设计float属性的主要目的，是为了实现文本绕排图片的效果。然而，这个属性也成为了创建多栏布局最简单的方式。

1.文本绕排图片

![img](http://images.cnitblog.com/blog2015/366625/201504/061601219938962.png)

在浮动一张图片或其他元素时，你是在要求浏览器把它往上方推，直到它碰到父元素的内边界。后面的段落不再认为浮动元素在文档流中位于它的前面了，因为它会占据父元素左上角的位置。不过，它的内容会绕开浮动的元素。

 2.创建分栏

在此基础上设置段落也向上浮动，不过要设定段落宽度

![img](http://images.cnitblog.com/blog2015/366625/201504/061608030242380.png)

换句话说，如果几个相邻的元素都具有设定的宽度，都是浮动的，而且水平空间也足以容纳它们，它们就会并列排在一行。

 

二.围住浮动元素的三种方法

浮动元素脱离了文档流，其父元素也看不到它了，因而也不会包围它。从下图可以清楚的看到，section元素没有包围浮动的image,这样导致footer元素向上移动section元素的后面。

![img](http://images.cnitblog.com/blog2015/366625/201504/061619474618869.png)

以上并非我们想要的效果，我们想要的是让footer元素排列在Image元素的下方。那有什么方法解决此问题呢?

方法一:为父元素添加overflow:hidden

![img](http://images.cnitblog.com/blog2015/366625/201504/061623346967014.png)

实际上，overflow:hidden声明的真正用途是防止包含元素被超大内容撑大。应用overflow:hidden之后，包含元素依然保持其设定的宽度，而超大的子内容则会被窗口剪切掉。除此之外，overflow:hidden还有另一个用途即迫使父元素包含其浮动的子元素。

方法二:同时浮动父元素

![img](http://images.cnitblog.com/blog2015/366625/201504/061627347741138.png)

但有一点需要注意的是，因为浮动了section元素，所以即使section为块元素，它也会伸缩到足以包裹其内容的大小，需要显式设定width:100%保持与浏览器同宽。同时浮动了section，footer会努力向上挤到浮动元素的旁边，需要设定footer的clear属性，被清除的元素不会被提升到浮动元素的旁边。

方法三:添加非浮动的清除元素

第三种强制父元素包含其浮动子元素的方法，就是给父元素的最后添加一个非浮动的子元素，然后清除该元素。在包含元素最后添加子元素作为清除元素的方式有两种。

1.在HTML标记中添加一个子元素，然后清除该元素。

![img](http://images.cnitblog.com/blog2015/366625/201504/061642124619297.png)

2.应用clearfix规则

![img](http://images.cnitblog.com/blog2015/366625/201504/061645041337484.png)



下面这个可以在现代浏览器上工作。如果你想要支持IE6，你就需要再加入如下样式：

```
.clearfix {
  overflow: auto;
  zoom: 1;
}
```



很多同学都在使用下面的骨灰级解决办法：

```
.clear{clear:both;height:0;overflow:hidden;}
```

上诉办法是在需要清除浮动的地方加个div.clear或者br.clear，我们知道这样能解决基本清浮动问题。

但是这种方法的最大缺陷就是改变了html结构，虽然只是加个div。

## 最优浮动闭合方案（这是我们推荐的）：

```
.clearfix:after{content:".";display:block;height:0;clear:both;visibility:hidden}
.clearfix{*+height:1%;}
```

用法很简单，在浮动元素的父云素上添加class=”demo clearfix”。

你会发现这个办法也有个弊端，但的确是小问题。改变css写法就ok了：

```
.demo:after,.demo2:after{content:".";display:block;height:0;clear:both;visibility:hidden}
.demo,.demo2{*+height:1%;}
```

以上写法就避免了改变html结构，直接用css解决了。

## 很拉轰的浮动闭合办法：

```
.clearfix{overflow:auto;_height:1%}
```

这种办法是我看国外的一篇文章得到的方案，测试了，百试不爽，真的很简单，很给力。喜欢的同学也可以试试这个办法。