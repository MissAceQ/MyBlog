---
title: 数据类型 
permalink: dataType
tags: 
- js
categories: 
- js基础
- 面经
---

面试常遇到的问题之一，js基本数据类型有哪几种，每次都回答五种，但是其实ES6中新增了一种。。。还有关于Null和undefined的具体考察。

### 数据类型

1. Undefined
2. Null
3. Boolean
4. Number
5. String
6. **Symbol** (ES6新定义的)
7. Object

前六种是原始类型，就是基本类型，再加一种更复杂的Object类型。

### Null和Undefined的区别

#### Undefined

表示本来应该有，但是没有，代表了一个不存在的值(non-existence of a value)。

1. 声明变量时**没有定义初始值**，就有一个默认值Undefined。  
2. 当试图**访问一个不存在的对象**属性或数组项时，返回一个undefined值。  
3. 如果**省略了函数的返回语句**,返回undefined。  
4. **函数调用时未提供的值**结果将为undefined参数值。  
5. void操作符也可以返回一个undefined值。像Underscore的库使用它作为一个防御式的类型检查，因为它是不可变的，可以在任何上下文依赖返回undefined。  
6. undefined是一个预定义的全局变量(不像null关键字)初始化为undefined值。

#### Null

值Null是一个JS字面量，表示空值(null or an "empty" value)，没有对象被3呈现(no object is present)。通常用作一个空引用一个空对象的预期,就像一个占位符。

1. DOM，它是独立于语言的，不属于ECMAScript规范的范围。因为它是一个外部API，试图获取一个不存在的元素返回一个null值，而不是undefined。  
2. 如果需要给一个变量/属性指定一个不变值，将它传递给一个函数，或者从一个函数返回null(**函数返回null，也算有返回值**)，null几乎总是最好的选择。
3. 通过分配null值，有效地清除引用，并假设对象没有引用其他代码，指定垃圾收集，确保回收内存。  

```JavaScript
	function s(num) {
		this.num = num;
		sayNum: function(num) {
			alert(num);
		};

		return sayNum;
	}
```

那么执行以下代码以后，结果如何？

```JavaScript
	var s = new s(0); // 返回一个function
	var s1 = s(1); // undefined
```

实际上，因为s所代表的function并没有return，但是s1已经声明，所以s1的结果是undefined。

### Number

#### 整数

整数可以通过十进制、八进制、十六进制的字面值表示

```JavaScript
	var intNum = 55; // 十进制
	var octalNum = 070; // 八进制，第一位必须为0，解析成56
	var octalNum = 079; // 无效，9超过了8，解析成79
	var hexNum = 0xA; // 十六进制，前两位必须是0x，解析成10
```

#### 浮点数

1. 数值必须包含一个小数点，小数点后面必须有一个数字，如果小数点后面只有0，这个小数会被转化成整数。  
2. 浮点数占用的内存空间是整数的两倍。  
3. 对极大极小的浮点数用e表示法。  

#### NaN

1. 0/0返回NaN（其他数除以0返回Infinity）。
2. 任何涉及到NaN的操作都会返回NaN，比如 NaN==NaN 会返回false，但是ES6通过Object.is修正了这个问题，Object.is(NaN,NaN) 返回true。
3. isNaN()，这个函数可以用来判断参数是否“不是数值”，涉及到数值转换问题。
	
```JavaScript
	isNaN("10"); // 返回true，字符串“10”可以转换成数值10。  
	isNaN("blue"); // 返回false，字符串“blue”无法转换成数值，所以返回false。
```

### String

#### toString()

除了Null和Undefined，每个值都有这个方法。一般使用的时候不需要传递参数，调用数值的toString()方法的时候，可以传递基数，默认是10，代表十进制。

#### String()

这个函数可以将任何类型的值转换成字符串，包括Null转换成"null"，undefined转换成"undefined"。

### Boolean

Boolean类型只有两个值：true和false。

#### Boolean()

这个函数可以将其他类型的值转换成为布尔类型。

#### 转换规则

1. false：""(空字符串)、0、null、undefined
2. true：非空字符串、非零数字值、任何对象、(undefined不适用)

### Symbol

Symbol，表示独一无二的值。

Symbol值通过Symbol函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的Symbol类型。凡是属性名属于Symbol类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

```JavaScript
	let s = Symbol();
	typeof s; // "Symbol"
```

Symbol函数**不能使用new命令**，否则会报错。这是因为生成的Symbol是一个原始类型的值，不是对象。也就是说，由于Symbol值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。

Symbol函数可以接受一个字符串作为参数，Symbol函数的参数只是表示对当前Symbol值的描述，因此相同参数的Symbol函数的返回值是不相等的。

```JavaScript
	// 没有参数的情况
	var s1 = Symbol();
	var s2 = Symbol();

	s1 === s2; // false

	// 有参数的情况
	var s1 = Symbol("foo");
	var s2 = Symbol("foo");

	s1 === s2; // false
```

Symbol值不能与其他类型的值进行运算，但是，Symbol值可以显式转为字符串，也可以转为布尔值，但是不能转为数值。

### Object

1. 这个类型实例化的对象，实际上是一组数据和function的集合。  
2. 实例化对象的过程有两种，一种是通过new操作符，一种是通过对象字面量表示法。
3. 可以在Object类型基础上继承出更多的类型，Array、function、Date等。
4. 这些统称为引用类型，在声明一个引用类型的时候，将实例化对象地址存到栈内存中，然后通过地址访问堆内存。js中，函数的参数都是值传递的，而对于js内的引用类型，其值，也就默认为栈内存中的地址。(注意传值和传引用的区别)