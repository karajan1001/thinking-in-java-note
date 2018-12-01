# 第九章 接口

## 9.1 抽象类和抽象方法

```java
abstract void f(); //空的方法，抽象方法
```
包含抽象方法的为抽象类，必须加上`abstract`关键词。导出类如果想要不是抽象类，则必须实现所有抽象方法。否则必须用`abstract`来限制，不然编译器报错。

## 9.2 接口

`interface`关键词的抽象类，没有定义任何方法，只定义接口是个完全抽象类。使用时替换`class`关键词。

```java
interface Instrument{
    void play(Note n);
    Void adjust();
}
```

接口的方法都是public的。继承使用的是`implements`而不是`extends`

## 9.3 完全解耦

使用适配器模式可以将现有代码适配相应的`interface`

```java
class FilterAdapter implements Processor {
    Filter filter;
    public FilterAdapater(Filter filter){
        this.filter = filter;
    }
    public imp_1 {return fitler.imp_1()}
...
}
```
用一个接口包装一层。

## 9.4 JAVA中的多重继承

虽然实体类只能继承一个，但是可以继承多个接口。一般某事物如果是一个基类，第一选择应该是将其定义为一个接口。 

## 9.5 通过继承来扩展接口
### 9.5.1 组合接口时的名字冲突
组合不同接口引起的重载和方法名冲突可能会引起可读性混乱，应该避免这种情况。

## 9.6 适配接口
运行多种不同实现

## 9.7 接口中的域
接口中的任何域都是`static`和`final`的可以用来代替`enum`
### 9.7.1 初始化接口中的域

## 9.8 嵌套接口

## 9.9 接口和工厂方法

## 9.10 总结

