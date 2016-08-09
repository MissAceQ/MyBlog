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

```JavaScript
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

```JavaScript
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

```JavaScript
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

基本思想就是，在子类型的构造函数中调用超类型构造函数。用这种方法就存在构造函数模式的问题，在构造函数里定义的方法，无法复用。

```JavaScript
	function SuperType(){
		this.colors = ["red", "blue", "green"];
	}

	function SubType(){
		//继承了SuperType,"借调"了超类型的构造函数
		SuperType.call(this);
	}

	var instance1 = new SubType();
	instance1.colors.push("black");
	alert(instance1.colors); // "red,blue,green,black"

	var instance2 = new SubType();
	alert(instance2.colors); // "red,blue,green"
```

通过使用call()方法，在新创建的SubType实例的环境下调用了SuperType构造函数，这样就可以在新SubType对象上执行SuperType()函数里定义的所有对象初始化代码(this.colors)，SubType的每个实例都会有自己的colors属性的**副本**了。  
 
相对于原型链，构造函数的一个优势就是，可以在子类构造函数中向超类构造函数**传递参数**。  

```JavaScript
	function SuperType(name) {
		this.name = name;
	}

	function SubType() {
		// 继承了SuperType，同时还传递了参数
		SuperType.call(this, "Nicholas");

		// 实例属性
		this.age = 29;
	}

	var instance = new SubType();
	alert(instance.name); // "Nicholas"
	alert(instance.age); // 29
```

### 组合继承

将原型链和借用构造函数组合在一起，就是组合继承。基本思想：使用原型链实现对原型属性和方法的继承，然后通过构造函数来实现实例属性的继承。这样就可以实现函数复用，每个实例也能有自己的属性。  

组合继承也存在问题，会调用两次超类型构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。

```JavaScript
	function SuperType(name) {
		this.name = name;
		this.colors = ["red", "blue", "green"];
	}

	SuperType.prototype.sayName = function(){
		alert(this.name);
	};

	function SubType(name, age){
		// 继承属性
		SuperType.call(this, name); // 第二次调用SuperType()

		this.age = age;
	}

	// 继承方法
	SubType.prototype = new SuperType(); // 第一次调用SuperType()
	SubType.prototype.constructor = SubType;
	SubType.prototype.sayAge = function(){
		alert(this.age);
	};

	var instance1 = new SubType("Nicholas", 29);
	instance1.colors.push("black");
	alert(instance1.colors); // "red,blue,green,black"
	instance1.sayName(); // "Nicholas"
	instance1.sayAge(); // 29

	var instance2 = new SubType("Greg", 27);
	alert(instance12.colors); // "red,blue,green"
	instance2.sayName(); // "Greg"
	instance2.sayAge(); // 27
```

1. SuperType定义了两个属性：name、colors，SuperType的原型定义了一个方法sayName()。
2. SubType调用SuperType构造函数的时候传入参数name，又定义了自己的属性age。
3. SuperType的实例赋值给SubType的原型，在这个原型上定义方法sayAge()。

这样就实现了两个不同的SubType实例分别有自己的属性，又拥有相同的方法。instanceof和isPrototypeOf()可以识别基于组合继承创建的对象。

### 原型式继承

基本思想：借助原型可以基于已有的对象创建新对象，同事不需要创建自定义类型。ES5新增Object.create()方法规范了原型式继承。这个方法接收两个参数：一个用作新对象原型的对象和一个为新对象定义额外属性的对象(可选)。

```JavaScript
	var person = {
		name: "Nicholas",
		friends: ["Shelby", "Court", "Van"]
	};

	var anotherPerson1 = Object.create(person);
	anotherPerson1.name = "Greg";
	anotherPerson1.friends.push("Rob");

	var anotherPerson2 = Object.create(person);
	anotherPerson2.name = "Lily";
	anotherPerson2.friends.push("Barbie");

	alert(person.friends); // "Shelby,Court,Van,Rob,Barbie"
```

Object.create()方法的第二个参数格式是，每个属性通过自己的描述符定义。这种方式指定的属性会覆盖原型对象上的同名属性。例如：

```JavaScript
	var person = {
		name: "Nicholas",
		friends: ["Shelby", "Court", "Van"]
	};

	var anotherPerson = Object.create(person, {
		name: {
			value: "Greg"
		}
	});

	alert(anotherPerson.name); // "Greg"
```

### 寄生式继承

基本思想：创建一个只用来封装继承过程的函数，在函数内部以某种方式来增强对象，然后像真的做了所有工作一样返回对象。这种模式跟构造函数模式类似，不能函数复用。

```JavaScript
	function createAnother(original) {
		var clone = Object(original); // 通过调用函数创建一个新对象
		clone.sayHi = function(){ // 以某种方式增强这个对象
			alert("hi"); 
		};
		return clone; // 返回这个对象
	}

	var person = {
		name: "Nicholas",
		friends: ["Shelby", "Court", "Van"]
	};

	var anotherPerson = createAnother(person);
	anotherPerson.sayHi(); // "his"
```

1. createAnother接收了一个参数，作为新对象基础的对象。
2. 把这个对象传递给object()函数，将返回结果赋值给clone。
3. 再给clone对象添加一个新方法sayHi()。
4. 返回clone对象。

### 寄生组合式继承

基本思想：本质上，就是使用寄生式继承超类型的原型，然后再将结果指定给子类型的原型。

```JavaScript
	function inheritPrototype(subType， superType){
		var prototype = Object(superType.prototype); // 创建对象
		prototype.constructor = subType; // 创建对象
		subType.prototype = prototype; // 创建对象
	}

	function SuperType(name) {
		this.name = name;
		this.colors = ["red", "blue", "green"];
	}

	SuperType.prototype.sayName = function(){
		alert(this.name);
	};

	function SubType(name, age) {
		SuperType.call(this, name);

		this.age = age;
	}

	inheritPrototype(SubType, SuperType);

	SubType.prototype.sayAge = function() {
		alert(this.age);
	};
```

注：YUI的YAHOO.lang.extend()方法采用了寄生组合继承。
