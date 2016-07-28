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

原型链中，包含引用类型值的原型属性，会被所有实例共享。而且在创建子类实例时，不能向超类型的构造函数中传递参数。所以一般不会单独使用原型链实现继承。

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

有两种方法可以确定原型和实例的关系：

> * instanceof操作符 (instance instanceof SuperType)
> * isPrototypeOf()方法 (SuperType.prototype.isPrototypeOf(instance))

#### 给原型添加方法

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

	//继承了SuperType
	SubType.property = new SuperType();

	//添加新方法
	SubType.prototype.getSubValue = function(){
		return this.subproperty;
	};

	//重写超类型中的方法
	SubType.prototype.getSuperValue = function(){
		return false;
	};

	var instance = new SubType();
	alert(instance.getSuperValue()); // false
```

必须在用SuperType的实例替换原型之后，再定义新方法。通过原型链继承时，不能使用对象字面量创建原型方法，会重写原型链，原型被替换成字面量，变成一个Object实例，不再是SuperType实例，导致原型链被切断。

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

	//继承了SuperType
	SubType.property = new SuperType();

	//使用字面量添加新方法，导致上一行代码无效
	SubType.prototype = {
		getSubValue : function(){
			return this.subproperty;
		},

		someOtherMethod : function(){
			return false;
		}
	};

	//重写超类型中的方法
	SubType.prototype.getSuperValue = function(){
		return false;
	};

	var instance = new SubType();
	alert(instance.getSuperValue()); // false
```

### 借用构造函数

基本思想就是，在子类型的构造函数中调用超类型构造函数。

```
	function SubType(){
		this.colors = ["red", "blue", "green"];
	}

	function SubType(){
		//继承了SuperType
		SubType.call(this);
	}
```



