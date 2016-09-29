---
title: 简单工厂模式
permalink: singleton
tags: 
- java
categories: 
- 设计模式
---

--------------

## 简单工厂模式

### 基本流程

> 首先将需要创建的各种不同对象的相关代码封装到不同的类（**具体产品类**）中；  
> 将它们公共的代码进行抽象和提取后封装在一个**抽象产品类**中，每一个具体产品类都是抽象产品类的子类；  
> 然后提供一个工厂类用于创建各种产品，在工厂类中提供一个创建产品的工厂方法，该方法可以根据所传入的参数不同创建不同的具体产品对象；  
> 客户端只需调用工厂类的工厂方法并传入相应的参数即可得到一个产品对象。

### 定义

简单工厂模式(Simple Factory Pattern)：定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。因为在简单工厂模式中用于创建实例的方法是静态(static)方法，因此简单工厂模式又被称为静态工厂方法(Static Factory Method)模式，它属于类**创建型**模式。

### 要点

当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。简单工厂模式结构比较简单，其核心是工厂类的设计，结构图：

![简单工厂结构图](http://o9j22gj41.bkt.clouddn.com/20160928_java_simplefactory.png)

- Factory（工厂角色）：工厂角色即工厂类，它是简单工厂模式的核心，负责实现创建所有产品实例的内部逻辑；工厂类可以被外界直接调用，创建所需的产品对象；在工厂类中提供了静态的工厂方法factoryMethod()，它的返回类型为抽象产品类型Product。
- Product（抽象产品角色）：它是工厂类所创建的所有对象的父类，封装了各种产品对象的公有方法，它的引入将提高系统的灵活性，使得在工厂类中只需定义一个通用的工厂方法，因为所有创建的具体产品对象都是其子类对象。
- ConcreteProduct（具体产品角色）：它是简单工厂模式的创建目标，所有被创建的对象都充当这个角色的某个具体类的实例。每一个具体产品角色都继承了抽象产品角色，需要实现在抽象产品中声明的抽象方法。

在简单工厂模式中，通过工厂类来创建一个产品类的实例，而**无须使用new关键字**来创建对象，它是工厂模式家族中最简单的一员。

### 代码

#### 抽象产品类

```java
	abstract class Product {  
      
    	public void methodSame() {  //所有产品类的公共业务方法
        //公共方法的实现  
    	}  

    	public abstract void methodDiff();  //声明抽象业务方法 
	}
```

#### 具体产品类

```java
	class ConcreteProduct extends Product {  
     
    	public void methodDiff() {  //实现业务方法 
        //业务方法的实现  
    	}  
	}
```

#### 工厂类(核心)

在简单工厂模式中，工厂类提供了一个静态工厂方法供客户端使用，根据所传入的参数不同可以创建不同的产品对象。

```java
	class Factory {  
    
	    public static Product getProduct(String arg) {  //静态工厂方法  
	        Product product = null;  
	        if (arg.equalsIgnoreCase("A")) {  
	            product = new ConcreteProductA();  
	            //初始化设置product  
	        } else if (arg.equalsIgnoreCase("B")) {  
	            product = new ConcreteProductB();  
	            //初始化设置product  
	        }  
	        return product;  
	    }  
	}
```

#### 典型代码

在客户端代码中，通过调用工厂类的工厂方法即可得到产品对象

```java
	class Client {  
	    public static void main(String args[]) {  
	        Product product;   
	        product = Factory.getProduct("A"); //通过工厂类创建产品对象  
	        product.methodSame();  
	        product.methodDiff();  
	    }  
	}
```