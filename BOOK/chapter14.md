# 第十四章 类型信息
本章介绍Java如何在运行时识别对象和类的信息。主要两种方法，一种传统的`RTTI`。另一种是反射

## 14.1 为什么需要RTTI

向上转型会导致丢失派生类的类型信息。但是有些时候我们还是希望知道派生类的详细类型信息而不仅仅是基类的类型。此时就会需要`RTTI`

## 14.2 Class对象
这是Java的一种特殊对象，包含类有关的信息，可以用来创建类的所有常规对象，这个对象也可以用来执行`RTTI`。每个类都有一个对应的`Class`对象（类似元编程?)。所有类都是第一次使用时加载到JVM中。Class对象中有个方法`forName`可以获取一个类的`Class`对象。然后就能获得它实现的接口，是否接口类型，它的父类型等等方法。

```java
Class c = Class.forName("typeinfo.toys.FancyToy")
c.getName()
c.isInterface()
c.getSimpleName()
c.getCanonicalName()
c.getName()

```
### 14.2.1 类字面常量
除了`forName`还能使用`.class`获得对`Class`对象的引用。这个方法和`forName`不同不会触发类的初始化。
```java
Class initable = Initable.class
```

### 14.2.2 泛化的Class引用
Class引用除了指向某个`Class`对象，制造类的实例，还包含类的静态成员。

### 14.2.3 新的转型语法
`cast`方法可以接受参数对象，将其转换为Class引用的类型。
```java
Building b = new House();
Class<House> houseType = House.class;
House h = houseType.cast(b);
// 上下等价
h = (House)b;
```

## 14.3 类型转换前先做检查

三种`RTTI`方法：
1. 类型转换
2. 取得`Class`对象
3. 使用`instanceof`返回BOOL值。
如果程序包含大量`instanceof`则说明设计上可能存在问题。

```java
Class<? extends Pet> ; //表示任何Pet类型的子类型的`Class`对象。
```

### 14.3.2 动态的instanceof
使用Class对象的方法`.isInstance`可以替代`instanceof`。
```java
Dog.class.isInstance(pet)
pet instanceof Dog
```

### 14.3.3 递归计数

## 14.4 注册工厂
工厂方法将类的创建交给类自己完成，工厂方法可以被多态调用，从而创建恰当类型的对象。

## 14.5 instanceof与Class的等价性
`instanceof与`isInstance`完全等价都考虑继承。`Class`方法不考虑继承

## 14.6 反射：运行时的类信息
`Class`类与`java.lang.reflect`类库一起对<font color='red'>反射</font>进行了支持。RTTI和反射的区别在于`RTTI`在编译时会检查类的文件`class`是否存在于本地或者网络。而反射则是在JVM运行时检测。比如`Class.forName`方法只有在运行时才会获得类的`Class`对象。

### 14.6.1 类方法提取器

## 14.7 动态代理
参考<设计模式>中的代理模式

## 14.8 空对象
```java
public interface Null {} ///~
class Person{
    public static class NullPerson extends Person implements Null{
        ...
    }
    public static final Person Null = new NullPersion();
}
```
NULLPerson是一个不可改变的单例，可以使用`instanceof Null`来检测是否为空对象。

### 14.8.1 模拟对象和桩
空对象的逻辑变体为模拟对象和桩，一个是轻量级的自测用例。另一个是可以经常复用的，根据被调用方式，通过配置修改。

## 14.9 接口与类型信息
Java中对象可以强制类型转换从而跳过接口限制。
```java
public interface A{ void f()};
class B implements A {
    public void f(){}
    public void g(){}
}
A a = new B()
B b = (B)a
b.();

```
而反射更可以达到调用非公共访问权限。

## 14.10 总结
> 不要太早关注程序的效率问题，这时候诱人的陷阱。最好首先让程序跑起来，然后再考虑它的速度。
