---
title:  闭包 
permalink: Closures
tags: 
- js
categories: 
- js基础
---

首先是闭包的概念，闭包是指有权访问另一个函数作用域中的变量的函数。

常见的创建闭包的方式，就是在一个函数里创建另一个函数。

```JavaScript
	function createComparison(propertyName) {
		return function(object1, object2) {
			var value1 = object1(propertyName); // 访问了外部变量propertyName
			var value2 = object2(propertyName); // 访问了外部变量propertyName

			if(value1 < value2) {
				return -1;
			} else if(value1 > value2) {
				return 1;
			} else {
				return 0;
			}
		};
	}
```

即使内部函数被返回了，而且在其他地方被调用，依然可以访问变量propertyName，这涉及到作用域链相关知识。

#### 关于变量

作用域链会引起一个副作用，就是闭包只能取得包含函数中任何变量的最后一个值，因为闭包保存的是整个变量对象，而不是特殊的变量。对比下面两段代码

```JavaScript
	function createFunction() {
		var result = new Array();

		for(var i=0;i<10;i++){
			result[i] = function(){
				return i;
			};
		}

		return result;
	} // 每个函数都返回10。
```

因为每个函数的作用域链都保存着createFunction()函数的活动对象，所以它们引用的都是同一个变量i，createFunction()返回以后i是10，所以以后的值都是10。

```JavaScript
	function createFunction() {
		var result = new Array();

		for(var i=0;i<10;i++) {
			result[i] = function(num){
				return function(){
					return num;
				};
			}(i); // 定义匿名函数并立即执行
		}

		return result;
} // 每个函数返回各自不同的索引值。
```

匿名函数有一个参数num，就是最终的函数要返回的值，在调用每个匿名函数的时候，传入变量i，因为函数参数是按值传递的，所以i的当前值就会复制给num，然后又创建并返回一个num的闭包。这样，result数组里的每个函数都有num变量的副本，就会返回各自不同的值。

#### 关于this对象

在全局函数中，this等于window，而当函数被作为某个对象的方法调用的时候，this就等于那个对象。但是，匿名函数的执行环境具有全局性，所以匿名函数this通常指向window。对比下面两段代码。

```JavaScript
	var name = "window";

	var object = {
		name: "My object",

		getNameFunc: function(){
			return function(){
				return this.name;
			};
		}
	};

	alert(object.getNameFunc()()); // "window"(在非严格模式下)
```

```JavaScript
	var name = "window";

	var object = {
		name: "My object",

		getNameFunc: function(){
			var that = this; // 把外部作用域中的this保存在一个闭包能访问的变量里
			return function(){
				return that.name; // that引用的是object
			};
		}
	};

	alert(object.getNameFunc()()); // "My object"
```

