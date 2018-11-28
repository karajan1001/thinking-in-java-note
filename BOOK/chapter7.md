# 第七章 复用类

## 7.1 组合语法
讲对象的引用置于新类中即可。

## 7.2 继承语法

Java 继承使用`extends`关键词。

```java
class OldClass{
    int a = 1;
}
class NewClass extends OldClass{
    public void main(Strings[] args){
        System.out.print(a);
}
```

每个包都包含`main`方法可以方便单元测试。为了方便继承一般将类的方法定为`public`而数据成员则定为`private`。`super`可以调用基类中的方法防止递归调用。
```java
super.method()
```

### 7.2.1 初始化基类
对基类的初始化唯一方法就是调用基类的构造器方法。所以java中会自动调用基类的默认构造器。但是对于带参数的就必须用`super`显示调用基类的构造器了。
```java
super(args)
```

## 7.3 代理
直接将所有接口参数传给内部数据对象。

## 7.4 结合使用组合和继承
### 7.4.1 确保正确清理 

### 7.4.2 名称屏蔽
java 中继承类重载并不会屏蔽 基类中的同名类。关键词`@override`可以防止错误的重载而不是覆写基类中的方法。

## 7.5 在组合和继承之间选择
老生常谈，`is-a`和`has-a`的区别。

## 7.6 protectd 关键词

## 7.7 向上转型

## 7.8 final 关键字

## 7.9 初始化和类的加载

## 7.10 总结
