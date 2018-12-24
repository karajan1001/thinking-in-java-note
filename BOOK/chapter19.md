# 第十九章　枚举类型

## 19.1 基本枚举类型
枚举类型的定义方法取值取名方法为
```java
enum Shrubbery {GROUND, CRAWLING, HANGING}
Shrubbery s = Shrubbery.CRAWLING;
println(s.name()); // CRAWLING
println(s.ordinal());  // 1
```
除此外还实现了`equals`和`compareTo`方法。
### 19.1.1 将静态导入用于enum
```java
import static enumerated.Shrubbery.*;
Shrubbery s = CRAWLING;
```

可以将标识符带入当前命名空间。


## 19.2 向enum中添加新方法

enum 中可以添加新的方法,比如返回自身的描述。枚举类型中的值都是通过枚举类型构造器生成的。

### 19.2.1 覆盖enmu的方法
和普通类没有什么不同。

## 19.3 switch语句中的enum
枚举类型可以直接用于switch语句。

```java
switch(s){
    case GROUND: s = Shrubbery.CRAWLING;
                 break;
    case CRAWLING: s = Shrubbery.HANGING;
                 break;
    case HANGING: s = Shrubbery.GROUND;
                 break;
}
```

## 19.4 values()的神秘之处

所有enum都继承自`Enum`类，但是此类并没有`values`方法，`values`是编译器添加的静态方法。

## 19.5 实现，而非继承
所有enum都继承自`Enum`所以不能再继承其他类了。但是可以实现多个接口。

## 19.6 随机选取
使用泛型可以表示enum实例。
```java
<T extends Enum<T>> 表示任意继承自`Enum`的枚举类型。
```

## 19.7 使用接口组织枚举
