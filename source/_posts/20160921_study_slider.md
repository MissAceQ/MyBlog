---
title: 轮播图
permalink: slider
tags: 
- js
categories: 
- js小程序
---

### 轮播图

--------------------------------------------------

### 思路

将图片设为左浮动，利用jQuery的**animate**函数，实现向左滑动，再用**setInterval**定时循环执行。

### 要点
1. 给容器设置overflow:hidden十分重要
2. 点击小圆点时，一定要清除上一次的定时器，重新开始定时器时，要给intervalID重新赋值
3. imagebox的宽度要和image的宽度相等，否则循环显示时，就会出现box中有一大一小两张图片


### 实现

#### html

```html
<div class="showbox">
    <div class="imagebox"> //放置图片
        <img src="image/1.jpg" alt="1.jpg">
        <img src="image/2.jpg" alt="2.jpg">
        <img src="image/3.jpg" alt="3.jpg">
        <img src="image/4.jpg" alt="4.jpg">
    </div>
    <div class="icobox"> //放置图标
        <pan rel="0" class="active">0</span>
        <span rel="1">1</span>
        <span rel="2">2</span>
        <span rel="3">3</span>
    </div>
</div>
```

#### css

```css
.showbox {
	position: relative;
	height: 300px;
	width: 570px;
	overflow: hiden;   // 关键
	border: 10px solid #eee;
	background-color: #eee;
	border-radius: 10px;
	-webkit-border-radius:10px;
    -moz-border-radius:10px;
}

.imagebox {
	position: relative;
	height: 270px;
	width: 570px;   //宽度与img的一致
	top: 0;
	left: 5px;
	overflow: hiden;   // 关键
}

.imagebox img {
	display: block;
	width: 570px;   // 宽度与imagebox的一致
	height: 270px;
	float: left;   //设置为左浮动
}

.iconbox {
	position: absolute;
	border: none;
	width: 120px;
	height: 12px;
	left: 220px;
	bottom: 10px;
	text-align: center;
	line-height: 40px;
	overflow: hiden;   // 关键
}

.iconbox span {
	background: url("../imag/.png") 0 0 no-repeat; //css sprite实现变色
	width: 12px;
	height: 12px;
	cursor: pointer;
	float: left;
	margin-left: 3px;
}

.icobox span.active{
    background: url("../image/ico.png") 0px -12px no-repeat; //css sprite实现变色
    cursor: default;
}
```

#### JavaScript

```JavaScript
$(document).ready(function() {
    var imagebox=$(".showbox").children('.imagebox')[0],//获得图片容器
        icobox=$(".showbox").children('.icobox')[0],//获得图标容器
        ico=$(icobox).children('span'),//获得图标数组
        imagenum=$(imagebox).children().size(),//获得图片数量
        imageboxWidth=$(imagebox).width(),//获得图片容器的宽度
        imagewidth=imageboxWidth*imagenum,//获得图片的总宽度
        activeID = parseInt($($(icobox).children(".active")[0] ).attr("rel")),//获得激活的图标ID
        nextID=0,//下一个图标的ID
        intervalID,//setInterval()函数的ID
        delaytime=4000,//延迟的时间
        speed=700;//执行速度
        $(imagebox).css({'width' : imagewidth + "px"});//将图片容器的宽度设置为图片总宽度

        var rotate=function(clickID) { //图片滑动函数
            if (clickID+1){ //有下一张图片
                nextID=clickID;
            }else{ //没有下一张则从头开始
                nextID=(activeID+1)%4;
            };
            $(ico[activeID]).removeClass('active');
            $(ico[nextID]).addClass('active');
            $(imagebox).animate({left:"-"+nextID*imageboxWidth+"px"}, speed);//jQuery中的animate函数
            activeID=nextID;
        }

        intervalID=setInterval(rotate,delaytime);//循环函数

        $.each(ico, function(index, val) {
            $(this).click(function(event) {
                clearInterval(intervalID);//清楚之前的定时任务（关键！！！）
                 var clickID = index;   // （关键！！！）
                 rotate(clickID);//运行一次带参数的rotate函数
                 intervalID = setInterval(rotate,delaytime);
            });
        });
});
```