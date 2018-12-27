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
在接口内部创建实现该接口的枚举类型，可以实现元素分组，

```java
public interface Food{
    enum Appetize implements Food{
        SALAD, SOUP, SPRING_ROLLS;
    }
    enum MainCourse implements Food{
        LASAGNE, BURRITO, PAD_THAI;
    }
}
Food food = Appetizer.SALAD;
food = MainCouse.BURRITO;
```

更好的方法是创建新的枚举类型，包装Food中的每一个enmu类。更简洁的方法是将一个enmu 嵌套在另一个enum里面。

## 19.8 使用EnumSet替代表标志
可以用来代表比特位，使用方法为:
```java

public enum AlarmPoints{
    STAIR1, STAIR2, STAIR3, STAIR4  
}
EnumSet<AlarmPoints> points = EnmuSet.nonOf(AlarmPoints.class); // 设为空，全0
points.addAll(EnumSet.of(Stair1, Stair2)); // 1号,2号开关打开
points.allOf(AlarmPoints.class); // 1号,2号开关打开
points.RemoveAll(EnumSet.of(Stair1, Stair4)); // 1号,4号开关关闭
```

## 19.9 使用EnumMap
一种特殊的Map，Key来自一个enum。
```java
interface Command( void action();}
EnmuMap<AlarmPoints,Command> em = new EnumMap<AlarmPoints,Command>(AlarmPoints.class);
```

## 19.10 常量相关的方法
enum实例可以编写方法，从而为每个实例赋予不同行为。为了实现这个可以为`enum`实现一个或者多个不同的抽象或者普通方法，然后覆盖它们。看起来似乎每个实例都像一个继承的类，但是并不能将实例当做类来用。
```java
public enum ConstantSpecificMethod {
	 DATE_TIME {
	    String getInfo() {
	      return
	        DateFormat.getDateInstance().format(new Date());
	    }
	  },
	  VERSION {
	    String getInfo() {
	      return System.getProperty("java.version");
	    }
	  };
	  abstract String getInfo();
}
```

#### 19.10.1 使用enum职责链
可以使用职责链设计模式，处理问题。

#### 19.10.2 使用enum状态机
也可以用enum来创造状态机

## 19.11 多路分发

Java 只支持单路分发。也就是说如果要执行的操作包含不止一个类型未知的对象，那么 Java 的动态绑定机制只能处理其中的一个类型。这样就无法解决我们上面的问题。所以，你必须自己去判定其他的类型，从而实现自己的动态绑定行为。

### 19.11.1 使用 enum 分发
一种方式是使用构造器来初始化每个 enum 实例，并以 “一组” 结果作为参数。

### 19.11.2 使用常量相关的方法
不过，通过这种方式，enum 实例虽然可以执行具有不同的行为，但他仍然不是类型，不能将其作为方法的参数传递。

### 19.11.3 使用 EnumMap 分发
使用 EnumMap 可以实现 “真正的” 两路分发。EnumMap 是为 enum 专门设计的一种性能非常好的 map。由于我们的目的是摸索出两种未知的类型，所以可以用 EnumMap 来实现：

## 19.12 总结
Java最初设计没有枚举类型。优雅与清晰很重要，正是他们区分了成功的解决方案与失败的解决方案。而失败的解决方案是因为其他人无法理解。
