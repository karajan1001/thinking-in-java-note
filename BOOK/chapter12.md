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
7jjclass SimpleException extends Exception{
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

如果捕获类型为异常基类`Exception`则会捕获所有异常。`printStackTrace`方法打印出详细的调用栈轨迹。

### 12.6.1 栈轨迹
`getStackTrance`方法可以直接返回一个由栈轨迹中元素组成的数组。第一个元素是调用序列中`Throwable`被创建和抛出支出。
```java
for (StackTraceElement ste: e.getStackTrace())
    System.out.println(ste.getMethodName())
```

### 12.6.2 重新抛出异常
可以将捕获到的异常重新抛出，下次捕获将获得本次异常完全信息。调用栈也是保持原有环境。如果要将调用栈改变则需要使用方法`fillInStackTrace`

```java
catch(Exception e) {
    System.out.println("An exception was thrown");
    throw e;
```

### 12.6.3  异常链

可以在捕获一个异常后，抛出另一个异常，同时希望原始信息保存，此时需要异常链。

```java
AException ae = new AException()
ae.inCause(new BException());
throw ae;
```

## 12.7 Java标准异常
Java可被抛出的类`Throwable`分为两种：
- Error: 编译时和系统错误(一般不用关心)。
- Exception: 库类，用户方法以及运行时候可能抛出的错误。

### 12.7.1 特例: RuntimeException
比如空指针异常，等等会被JAVA自动抛出，叫做RuntimeException。它们会被JVM自动抛出，而不需要手动进行。如果不进行捕获，异常会直接到达main函数报告给`System.err,我们运行程序报错。

## 12.8 使用finally进行清理

`finally`无论异常是否抛出,都会执行。和python一样。

### 12.8.1 finally可以用来干啥

比如将资源恢复到初始状态，比如已经打开的文件或者网络链接，屏幕上画的图形。

### 12.8.3 在return中使用finally
多点返回的程序，某些重要清理工作必须进行。
```java
try{
if (i == 1) reutrn;
if (i == 2) reutrn;
if (i == 3) reutrn;
if (i == 4) reutrn;
}
finally{
  print("clean up")
}

```

### 12.8.4 缺憾，异常丢失
finally 中如果使用`return`或者`throw Exception`可能会导致异常信息丢失。

## 12.9 异常的限制
派生类中的方法能抛出的异常种类，只能是基类中方法的子集，使用接口也不能进行扩展。但是如果使用接口中的方法基类没有，则可以抛出接口中可抛出的异常。构造器与异常关系则参考下一节。

## 12.10 构造器
构造器异常和其他地方不同，因为构造器可能成功也可能失败。两种不同情况无法直接用finally解决,只能分情况讨论。而对于需要清理的对象应该用一个`try: finally`语句。形成类似python中`with as`中类似的效果。
```java
try{
    // open a file
    try {
        // read fileline
    }
    catch(Exception e){
    // deal with read error
    }
    finally{
        // close file
    }
} catch(Exception e){
    // file not exists   
}
```

## 12.11 异常匹配
异常匹配可以匹配派生类异常。被捕获处理后就不会继续。

## 12.12 其他可选方法
异常处理的原则，只有当你知道如何处理异常时才捕获该异常。类型检查并不是万能的。

### 12.12.1 历史

### 12.12.2 观点
健壮代码依赖类型检查，和一致的异常报告。不管是编译时类型检查还是运行时。

### 12.12.3 将异常传递给控制台
可以将异常传递给控制台这样就不用再去写`try: catch`语句了。
```java
public static void main(String[] args) throws Exception{

}
```

### 12.12.4 被检查的异常转换为不被检查的异常
可以将不知道如何处理的异常包装到`RuntimeException`中形成异常链。保留原始信息，让知道如何处理的人来进行处理。

## 12.13 异常使用指南

1. 在恰当的级别处理问题。
2. 解决问题并且重新调用产生异常的方法。
3. 进行少许修补，然后绕过异常发生的地方继续执行。
4. 用别的数据进行计算，以代替方法预计会返回的值。
5. 把当前运行环境能做的事情尽量昨晚，然后把`相同`的异常重新抛到更高层。
6. 把当前运行环境能做的事情尽量昨晚，然后把`不同`的异常新抛到更高层。
7. 终止程序。
8. 进行简化。
9. 让类库和程序更安全。

## 12.14 总结
异常的优势是将解决问题，和处理异常的代码分离。


