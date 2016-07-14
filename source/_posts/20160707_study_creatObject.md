---
title: 创建对象 
tags: 
- js
categories: 
- js基础
---

# 创建对象

创建对象有很多模式，简单总结一下概念优缺点，方便对比学习。

### 工厂模式  

这种模式抽象了创建具体对象的过程，用函数来封装以特定接口创建对象的细节。  
解决了ES无法创建类的问题，可以创建多个相似对象，但是无法识别对象，无法判断一个对象的类型。工厂模式例子如下： 

```JavaScript
	function createPerson(name, age, job){
		var o = new Object();
		o.name = name;
		o.age = age;
		o.job = job;
		o.sayName = function(){
			alert(this.name);
		};
		return o;
	}

	var person1 = createPerson("Nicholas", 29, "Software Engineer");
	var person2 = createPerson("Greg", 27, "Doctor");

```

可以无数次调用createPerson函数，函数会根据接收的参数创建Person对象，并返回这个对象。  

### 构造函数模式  

构造函数可以用来创建特定类型的函数，**构造函数以大写字母开头，非构造函数以小写字母开头**。
可以创建自定义的构造函数，从而定义自定义的对象类型的属性和方法。创建自定义的构造函数意味着以后*可以将它的实例标识为一种特定的类型*。  
使用构造函数模式的主要问题是，每个方法都要在每个实例上重新创建一次。
将上面的例子用构造函数模式重写。


```JavaScript
	function Person(name, age, job){
		this.name = name;
		this.age = age;
		this.job = job;
		this,sayName = function(){
			alert(this.name);
		};
	}

	var person1 = new Person("Nicholas", 29, "Software Engineer");
	var person2 = new Person("Greg", 27, "Doctor");

```

上两段代码不同之处：  

> * 没有显示的创建对象
> * 直接将属性和方法附给了 this 
> * 没有 return 

构造函数与普通函数的区别就是调用方式不同，构造函数使用 **new** 操作符。上面定义的 Person() 函数可以用下面任意一种方式调用。

```JavaScript
	// 当做构造函数
	var person = new Person("Nicholas", 29, "Software Engineer"); //典型用法，使用 new 操作符
	person.sayName(); //"Nicholas"

	// 当做普通函数 
	Person("Greg", 27, "Doctor"); // 添加到window
	window.sayName(); // "Greg"

	// 在另一对象的作用域中调用
	var o = new Object();
	Person.call(o, "Kristen", 25, "Nurse");
	o.sayName(); // "Kristen"

```

**注**：不同实例上的同名函数是不相等的。

```
	alert( person1.sayName == person2.sayName ); // false
```

### 原型模式

prototype就是通过调用构造函数而创建的那个对象实例的原型对象。使用原型对象，不需要再构造函数中定义对象实例的信息，而是可以将这些信息直接添加到到原型对象中。

```JavaScript
	function Person(){
	}

	Person.prototype.name = "Nicholas";
	Person.prototype.age = 29;
	Person.prototype.job = "Software Engineer";
	Person.prototype.sayName = function(){
		alert(this.name);
	}

	var person1 = new Person();
	person1.sayName(); // "Nicholas"

	var person2 = new Person()
	person2.sayName(); //"Nicholas"

	alert( person1.sayName == person2.sayName ); // true
```






