---
title: 创建对象 
permalink: createObject
tags: 
- js
categories: 
- js基础
---


JS高程中介绍的几种创建对象的模式。

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

prototype就是通过调用构造函数而创建的那个对象实例的原型对象。使用原型对象，不需要再构造函数中定义对象实例的信息，而是可以将这些信息直接添加到到原型对象中。**原型模式最大的问题在于，原型中所有属性是被很多实例共享的**。

#### 例1 将信息直接添加到原型对象中

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

将sayName()方法和所有属性添加到Person的prototype属性，构造函数变成空函数。由构造函数创建新对象，新对象的属性和方法是由所有实例**共享**。  

#### 例2 实例属性屏蔽原型中同名属性

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
	var person2 = new Person();

	person1.name = "Greg";
	alert(person1.name); // "Greg" 来自实例
	alert(person2.name); // "Nicholas" 来自原型
```

给对象实例添加的属性会阻止我们访问原型中的那个属性，但不会修改那个属性。即使将这个属性设置为null，也只会在实例中设置这个属性，不会恢复其指向原型的连接。使用**delete**操作符可以完全删除实例属性。  

#### 例3 更简单的原型语法

```
	function Person(){
	}

	Person.prototype = {
		name : "Nicholas",
		age : 29,
		job : "Software Engineer",
		sayName : function(){
			alert(this.name);
		}
	};
```

用包含所有属性和方法的**对象字面量**来重写原型对象，最终结果一样，但是constructor属性不在指向Person。所以需要指定constructor，确保通过该属性能访问到适当的值，指定以后会导致它的[[Enumerable]特性设置为 true ，变成可枚举。


```
	function Person(){
	}

	Person.prototype = {
		constructor : Person, // 指定constructor
		name : "Nicholas",
		age : 29,
		job : "Software Engineer",
		sayName : function(){
			alert(this.name);
		}
	};
```

#### 例4 

```
	var friend = new Person();

	Person.prototype.sayHi = function(){
		alert("hi");
	};

	friend.sayHi(); // "hi" (No Problem!)
```

创建一个Person实例，保存在friend中。调用sayHi的时候，先搜索实例，然后再搜索原型。

```
	function Person(){
	}

	var friend = new Person();

	Person.prototype = function(){
		constructor : Person.
		name : "Nicholas",
		age : 29,
		job : "Software Engineer",
		sayName : function(){
			alert(this.name);
		}
	};

	friend.sayName(); // error！
```

出错是因为，friend指向的原型中不包含以这个名字命名的属性。已经创建实例的情况下，重写原型对象会切断现有实例与新原型之间的联系。

### 组合构造函数模式+原型模式

```
	function Person(name, age, job){
		this.name = name;
		this.age = age;
		this.job = job;
		this.friends = ["Shelby", "Court"];
	}

	Person.prototype = {
		constructor : Person,
		sayName : function(){
			alert(this.name);
		}
	}

	var person1 = new Person("Nicholas", 29, "Software Engineer");
	var person2 = new Person("Greg", 27, "Doctor");

	person1.friends.push("Van");
	alert(person1.friends);  // "Shelby,Court,Van";
	alert(person2.friends);  // "Shelby,Court"
	alert(person1.friends === person2.friends);  // false
	alert(person1.sayName === person2.sayName);  // true
```

组合模式中，所有实例属性都写在构造函数中，所有的共享属性和方法在原型中定义。

另外还有以下几种创建对象的模式

### 动态原型模式

```
	function Person(name, age, job){

		//属性
		this.name = name;
		this.age = age;
		this.job = job;

		//方法
		if(typeof this.sayName != "function"){

			Person.prototype.sayName = function(){
				alert(this.name);
			};
		}
	}

	var friend = new Person("Nicholas", 29, "Software Engineer");
	friend.sayName();

```

这里只有在sayName不存在的情况下才会添加到原型中，if代码只有在初次调用构造函数的时候才会执行。使用这个方法时，**不能使用**对象字面量重写原型。

### 寄生构造函数模式

```
	function Person(name, age, job){
		var o = new Object();
		o.name = name;
		o.age = age;
		o.job = job;
		o.sayName = function(){
			alert(this.name);
		};
		return o;
	}
```

建议不使用这种模式

### 稳妥构造函数模式

```
	function Person(name, age, job){

		// 创建要返回的对象
		var o = new Object();
		
		// 可以在这里定义私有变量和函数

		//添加方法
		o.sayName = function(){
			alert(this.name);
		};

		//返回对象
		return o;
	}
```

