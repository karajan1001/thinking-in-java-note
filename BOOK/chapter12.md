# 第十二章 通过异常处理错误

>  JAVA的基本理念: "结构不佳的代码不能运行"

## 12.1 概念 
c中错误都是用错误代码进行，但是错误代码每个人定义不同。难以维护协调。Java中使用异常来进行处理，将问题提交给更高级别的环境。使用异常可以将异常逻辑集中处理。与业务逻辑分离，而且可以节省代码。

## 12.2 基本异常
- 异常情形: 组织当前方法或者作用域继续执行的问题。
- 普通问题：当前环境下可以直接处理, 处理这个错误。

异常处理的路径：
1. 遇到问题，新建`new`一个异常对象。
2. 执行路径终止，从当前环境中弹出异常对象的引用。
3. 异常处理机制接管程序，寻找一个**异常处理程序**处理。
4. 异常处理机制的作用是将程序从错误中回复，使其继续运行下去。

比如:
```java
if (t==null)
    throw new NullPointerException();
```
### 12.2.1 异常参数

所有标准异常类都有两个构造体:
1. 默认构造体。
2. 接受字符串参数的构造体，用来保存异常信息。

## 12.3 捕获异常

监控区域: 可能产生异常的区域。
### 12.3.1 try块

可以捕获异常。
```java
try{
// Code that might generate exceptions
}
```

### 12.3.2 异常处理程序
用来处理异常程序用`catch`表示:
```java
try{
    // Code that might generate exceptions
} catch(Type1 id1) {
    // Handle exception 1
} catch(Type2 id2) {
    // Handle exception 2
} catch(Type3 id3) {
    // Handle exception 3
}
```
`catch`类似一个接受特殊类型的方法。

### 12.3.3 终止与恢复

Java支持终止模型，程序无法再返回异常发生的地点继续执行。

## 12.4 创建自定义异常

自定义异常类必须从已有的异常类中继承，比如: 
```java
class SimpleException extends Exception{
    public SimpleException(){}
    public SimpleException(String msg){ super(msg);}
}
```
然后可以通过
```java
e.printStackTrace(System.out);
e.printStackTrace(); // 默认输出System.err 不会被重定向
```

打印异常信息，也可以通过`logging`模块打印异常。

## 12.5 异常说明
java强制使用异常说明,属于声明的一部分。
```java
void f() throws TooBig, TooSmall, DivZero { //...}
```
这个工作被编译器完成。

## 12.6 捕获所有异常



