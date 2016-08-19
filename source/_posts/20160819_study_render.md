---
title: 浏览器渲染过程 
permalink: createObject
tags: 
- 浏览器
categories: 
- js基础
---

面试常常被问到的一个问题就是，从输入网址到出现页面的过程。在这个过程里，就前端而言，最重要的部分就是渲染。那么不同浏览器使用的渲染引擎是不一样的，不同渲染引擎的渲染过程也会有一定差异，但大致过程是一致的。

Firefox, Safari两种浏览器构建于两种渲染引擎之上：

	1. Firefox使用Gecko——Mozilla自家的渲染引擎。  
	2. Safari和 Chrome 都使用Webkit。

渲染的主要流程如下：

解析html以构建dom树 -> 构建render树 -> 布局render树 -> 绘制render树

1. 计算CSS样式
2. 构建Render Tree
3. Layout – 定位坐标和大小，是否换行，各种position, overflow, z-index属性 ……
4. 正式开画

### 解析

首先，浏览器会解析三个东西：

1. HTML/SVG/XHTML，生成**DOM Tree**；
2. CSS，产生规则树，**CSS Rule Tree**；
3. JS，通过DOM API和CSSOM API操作DOM Tree和CSS Rule Tree。

#### DOM解析

一个常规的HTML会有html、head、title、body、div等标签，将它们解析会生成DOM Tree，由dom元素及属性节点组成，树的根是document对象。解析出的DOM Tree是下图的样子：

![DOM-Tree](http://o9j22gj41.bkt.clouddn.com/20160819_study_render_DOMTree.png)

#### CSS解析

CSS解析将css文件解析为样式表对象,每个对象包含css规则，css规则对象包含选择器和声明对象，以及其他一些符合css语法的对象。Webkit使用Flex和Bison解析生成器从CSS语法文件中自动生成解析器。Bison创建一个自底向上的解析器，Firefox使用自顶向下解析器。

##### Webkit

Webkit解析CSS如下图：

![Webkit-CSS-Tree](http://o9j22gj41.bkt.clouddn.com/20160819_study_render_WebkitCSSTree.png)

##### Firefox

Firefox解析CSS的过程是，比照着DOM Tree和CSS Rule Tree，得到一个叫Style Context Tree。

	注意：

	CSS匹配DOM Tree主要是从右到左解析CSS的Selector。CSS匹配HTML元素是一个相当复杂和有性能问题的事情。

	所以，DOM树要小，CSS尽量用id和class，千万不要过渡层叠下去。

Style Context Tree如下图。

![Firefox-CSS-Tree](http://o9j22gj41.bkt.clouddn.com/20160819_study_render_FirefoxCSSTree.png)

### 构造Rendering Tree

在解析完成以后，浏览器引擎会通过DOM Tree和CSS Rule Tree，构造Rendering Tree。

Firefox基本上来说是通过CSS解析生成CSS Rule Tree，然后，通过比对DOM Tree生成Style Context Tree，之后把Style Context Tree和Render Tree（Frame Tree）关联上，就完成了。

	注意：

	1. Rendering Tree 渲染树并不等同于DOM树，因为一些像Header或display:none的东西就没必要放在渲染树中了；

	2. CSS 的 Rule Tree主要是为了完成匹配并把CSS Rule附加上Rendering Tree上的每个Element，也就是DOM结点。也就是所谓的Frame；

	3. 计算每个Frame（也就是每个Element）的位置，这又叫layout和reflow过程

### 绘制

最后，根据定位，调用操作系统的Native GUI的API可以绘制页面。

### Reflow和Repaint

渲染过程有两个重要概念，**Reflow**和**Repaint**。

1. Repaint：屏幕的一部分要重画，比如某个CSS的背景色变了。但是元素的几何尺寸没有变。  
2. Reflow(Layout)：元件的几何尺寸变了，我们需要重新验证并计算Render Tree。Render Tree的一部分或全部发生了变化。

	(1) HTML使用的是flow based layout，也就是流式布局，所以，如果某元件的几何尺寸发生了变化，需要重新布局(reflow)。  

	(2) reflow 会从<html>这个root frame开始递归往下，依次计算所有的结点几何尺寸和位置，在reflow过程中，可能会增加一些frame，比如一个文本字符串必需被包装起来。

所以，Reflow的成本比Repaint的成本高得多的多。

#### 增加成本的动作：

1. 增加、删除、修改DOM结点时，会导致Reflow或Repaint；  
2. 移动DOM的位置，或是做一个动画的时候；  
3. 修改CSS样式的时候；  
4. Resize窗口（移动端没有这个问题），或是滚动的时候；  
5. 修改网页的默认字体时。

需要注意的是

	注意：

	display:none会触发reflow，而visibility:hidden只会触发repaint，因为没有发现位置变化。

#### reflow原因：

1. Initial。网页初始化的时候。
2. Incremental。一些Javascript在操作DOM Tree时。
3. Resize。其些元件的尺寸变了。
4. StyleChange。如果CSS的属性发生变化了。
5. Dirty。几个Incremental的reflow发生在同一个frame的子树上。

### 减少reflow/repaint

1. 不要一条一条地修改DOM的样式。与其这样，还不如预先定义好css的class，然后修改DOM的className。
2. 把DOM离线后修改。
	(1) 使用documentFragment对象在内存里操作DOM。  
	(2) 先把DOM给display:none(有一次reflow)，然后再做修改。比如修改100次，然后再把他显示出来。  
	(3) clone一个DOM结点到内存里，再做修改，改完以后，和在线的那个的交换一下。   
3. 不要把DOM结点的属性值放在一个循环里当成循环里的变量，不然这会导致大量地读写这个结点的属性。    
4. 尽可能的修改层级比较低的DOM。  
	改变层级比较底的DOM有可能会造成大面积的reflow，但是也可能影响范围很小。
5. 为动画的HTML元件使用fixed或absoult的position，那么修改他们的CSS是不会reflow的。  
6. 千万不要使用table布局。因为可能很小的一个小改动会造成整个table的重新布局。