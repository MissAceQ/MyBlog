---
title:  继承 
permalink: inheritance
tags: 
- js
categories: 
- js基础
---

JS高程中介绍了很多继承方式。一般语言都支持两种继承方式：接口继承和实现继承，接口继承只继承方法签名，实现继承则继承实际的方法。因为函数没有签名，所以ES主要依靠**原型链**完成实现继承。

### 原型链

实现原型链有一个基本模式：

```
	function SuperType(){
		this.property = true;
	}

	SuperType.prototype.getSuperValue = function(){
		return this.property;
	};

	function SubType(){
		this.subproperty = false;
	}

	// 继承了SuperType
	SubType.prototype = new SuperType();

	SubType.prototype.getSubValue = function(){
		return this.subproperty;
	};

	var instance = new SubType();
	alert(instance.getSuperValue()); // true
```

通过创建SuperType的实例，把实例赋值给SubType.prototype实现SubType继承了SuperType，实现本质就是重写原型对象。实例、构造函数、原型对象之间的关系如下图。

![instance-function-prototypr](http://o9j22gj41.bkt.clouddn.com/inheritance-1-1.png)

instance指向SubType的原型，SubType的原型指向SuperType的原型。instance.constructor现在指向的是SuperType，因为原来的SubType.prototype被重写了。

上例中调用instance.getSuperValue()会经历三个搜索步骤：
(1) 实例
(2) SubType.prototype
(3) SuperType.prototype

#### 默认原型

所有函数的默认原型都是Object的实例，因此默认原型都会包含一个内部指针，指向Object.prototype。完整原型链如下图。

![instance-function-prototype](http://o9j22gj41.bkt.clouddn.com/inheritance-1-2.png)

SubType继承了SuperType，SuperType继承了Object。当调用instance.toString()时，实际上是调用了Object.prototype中的方法。

#### 原型和实例的关系

> * instanceof操作符
> * isPrototypeOf()方法

