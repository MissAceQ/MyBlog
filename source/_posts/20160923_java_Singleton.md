---
title: 单例模式
permalink: singleton
tags: 
- java
categories: 
- 设计模式
---

## 单例模式

-------------------------------------------------------

### 目的要点

1. 创建对象，确保对象的唯一性。在同一个系统里，对象只有一个实例。
2. 由类自己保存它的唯一实例。
3. 提供一个访问实例的方法

### 实现分类

- 饿汉式
- 懒汉式
- 双重检查锁
- 静态内部类实现（饿汉式+懒汉式）
- 枚举式

### 饿汉式

1. 在定义静态变量的时候就实例化了单例类。
2. 在类加载的时候，静态方法会先加载，所以单例对象在类加载的时候九被创建了，**静态变量只加载一次，可以保证单例对象的唯一性**，并且是线程安全的。
3. 但是在类加载的时候就创建，会影响程序的效率。

```java
	public class Singleton{
		//唯一实例
		private static Singleton instance = new Singleton();

		//只能由类自己创建实例
		private Singleton(){

		}

		//提供一个全局访问的方法
		public static getInstance(){
			return instance;
		}
	}
```

### 懒汉式

```java
	public class Singleton{
		//唯一实例
		private static Singleton instance = null;

		//只能由类自己创建实例
		private Singleton(){

		}

		//提供一个全局访问的方法
		public static getInstance(){
			if(instance == null){
				instance = new Singleton();
			}
			return instance;
		}
	}
```

1. 懒汉式在需要的时候才实例化，在类加载的时候不进行实例化，能起到懒加载的作用。
2. 但是如果在多线程的情况下，比如线程Ａ和线程Ｂ同时调用getInstance()，可能会导致**并发问题**。
3. 所以要在getInstance()前加上synchronized进行同步，但是这样每次都要判断，会降低访问速度。
4. 可以用双重检查锁。  

### 双重检查锁

双重检查锁的第一次检查是检查实例是否存在，如果不存在再进入下面的同步块，而不是直接进入同步块．第二重检查是进入同步块后，再检查实例是否存在，如果不存在，就在同步的情况下创景实例．

```java
	public class Singleton{
	  //对保存实例的变量添加volitile的修饰
	  //唯一实例
	  private volatile static Singleton instance = null;

	  //只能由类自己负责创建实例
	  private Singleton(){

	  }

	  //外部访问实例的方法
	  public static getInstance(){
	    //第一重检查，检查实例是否存在，如果不存在，再进入同步代码块.
	    //存在未初始化过，多个线程都发现实例不存在而进入if语句
	    if (interface  == null)
	        //锁定代码块，锁定代码块保证了，if语句中创建实例的语句只能由一个线程执行
	        synchronized (Singleton.class)｛
	          //第二重检查，避免第一重检查中同时进入的线程，在其中一个进入锁定代码块创建单例而退出时，其他依然会进入锁定代码块创建实例的情况

	          if (instance == null) {
	                instance = new Singleton();//创建单例实例
	          }
	        ｝
	    }
	    return instance;
	  }    
}
```

### 静态内部类实现（饿汉式+懒汉式）

饿汉单例模式在类加载的时候就会创建，不能实现延时加载，如果以后不用，还会占据内存，还会影响程序的加载效率．懒汉单例模式需确保线程安全，性能会受到影响．比较好的实现方式是结合二者的优点，使用静态内部类来实现单例模式

```java
	public class Singleton {

	    /**
	     * 类级的内部类，也就是静态类的成员式内部类，
	     * 该内部类的实例与外部类的实例没有绑定关系，而且只有被调用时才会装载，从而实现了延迟加载
	     */
	     private static class SingletonHolder{
	        /**
	         * 静态初始化器，由JVM来保证线程安全
	         */
	        private static Singleton instance = new Singleton();
	    }

	    /**
	     * 私有化构造方法
	     * 只能由类自己负责创建实例
	     */
	    private Singleton(){

	    }

	    //对外提供获取实例的静态方法
	    public static Singleton getInstance(){
	        return SingletonHolder.instance;
	    }
	}
```

### 枚举式

单元素的枚举类型已经成为实现Singleton的最佳方法。

```java
	public class Singleton {

    /**
     * 定义一个枚举的元素，它就代表了Singleton的一个实例
     */
    uniqueInstance;

    /**
     * 单例的方法
     */
    public void singletonOperation(){
        //功能树立
    }
}
```

### 使用场景

1. 任务管理器
2. 回收站
3. 网站的计数器
4. 数据库连接池的设计一般也采用单例模式
5. 多线程的连接池，为了方便线程池对池中线程进行控制
6. 操作系统的文件系统，一个操作系统只能有一个文件系统

总结：  
（1）资源共享的情况下，避免由于资源操作时导致的性能或损耗等。如上述中的日志文件，应用配置。  
（2）控制资源的情况下，方便资源之间的互相通信。如线程池等。