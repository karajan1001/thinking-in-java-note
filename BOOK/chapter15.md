# 第十五章 泛型
泛型： 使用于多种类型,Java的泛型受到很多限制。

## 15.1 与C++的比较

> 了解Java泛型的局限在哪里，为什么会这样设计。只有知道局限才能成为真正的高手。

## 15.2 简单泛型

我们可以暂时指定类型为`Object`，等需要实际使用时候用`<>`来限制类型。

### 15.2.1 一个元组类库 
一次return返回多个对象。用泛型可以一次性解决问题,并且能在编译器就确保类型安全。这个概念成为`元组`。

### 15.2.2 一个堆栈类
```java
public class LinkedStack<T> {
    private static class Node<U> {
        U item;
        Node<U> next;
        Node() {item = null; next = null;}
        Node(U item, Node<U> next){
            this.item = item;
            this.next = next;
        }
        boolean end() { return item == null && next == null; }
    }
    private Node<T> top = new Node<T>();
    public T pop(){
        T result = top.item;
        if (!top.end())
            top = top.next;
        return result;
    }
}
```

### 15.2.3 RandomList 
同上例

## 15.3 泛型接口
泛型也可以用于接口比如生成器。生成器只有一个方法`next`，和工厂类型的区别在于工厂需要参数生成对象。而生成器无需参数就知道如何生成对象。
```java
public interface Generator<T> {T next();} ///:~
```

## 15.4 泛型方法
尽量使用泛型方法而不是创造一个泛型类。使用时讲泛型参数放置在返回值之前即可。
```java
public <T> void f(T x) {
    System.out.println(x.getClass().getName());
}

```

### 15.4.1 杠杆利用类型参数推断

类型推断只对赋值操作有用，可以通过赋值类型推断应该返回的类型。
```java
public static <K,V> Map<K,V> map(){
    return new HashMap<K,V>();
}

Map<String, List<string>> sls = map();
```

### 15.4.2 可变参数于泛型方法
泛型与可变参数列表`... args`也可以很好共存

### 15.4.3 用于Generator的泛型方法

### 15.4.4 一个通用的Generator

### 15.4.5 简化元组的使用
本节实现了一个可以重载的Set

### 15.4.6 一个Set使用工具
本节实现了一个集合操作的泛型Set

## 15.5 匿名内部类
泛型和匿名内部类可以共存

## 15.6 构建复杂模型
泛型一个重要的作用是简单安全的创建复杂的模型。
```java
class Product{}
class Shelf extends ArrayList<Product>{}
class Aisle extends ArrayList<Shelf>{}
class Store extends ArrayList<Aisle>{}
```

## 15.7 擦除神秘之处
在泛型代码内部，无法会偶的任何有关泛型参数类型的信息。`ArrayList<String>`和`ArrayList<Integer>`都被当成是`ArrayList`。它们在运行时都被擦除成原声类型`ArrayList`。

### 15.7.1 C++的方式
略
### 15.7.2 迁移兼容性
运行时泛型类型`List<T>`将被擦除成`List`，普通类型被转型为`Object`。泛型类型是第二类类型。这么做的主要目的是为了保持代码的迁移兼容性，兼容旧代码。

### 15.7.3 擦除的问题
擦除带来的问题是所有运行时类型操作都无法进行，例如转型，isntanceof, new等。
```java
class Foo<T>{
    T var;
}
Foo<Cat> f = new Foo<Cat>();
```
其中的`var`字段实际上只被当做是一个`Object`它不会知道自己的类型信息。在泛型中创新一个新对象是无法实现的`new T()`将会报错，因为 缺少类型信息，而且不知道相应的构造器是否存在。

### 15.7.4 边界处的动作

## 15.8 擦除的补偿
可以通过传入类型的`Class`对象的方式让泛型获得确信的类型信息。

```java
public class ClassTypeCapture<T>{
    Class<T> kind;
    public ClassTypeCapture(Class<T> kind){
        this.kind = kind;
    }
}

```

### 15.8.1 创建类型实例
Java解决, 在泛型中创新一个新对象是无法实现的方法是传递一个工厂对象，用其来创建新实例。
```java
Class<T> kind;
T = kind.newInstance()
```

### 15.8.2 泛型数组
缺少类型信息，我们无法创建泛型数组，替代方法是产生一个`ArrayList`
```java
T[] new = nwe T[size]; // 不能这样
List<T> array = new ArrayList<T> //可以这样

```

## 15.9 边界
因为泛型擦除了类型信息，所以我们只能调用`Object`可以调用的方法。为了让它能够调用某个基本类型的方法，我们需要将其限制在某个基本类型的子类型中。
```java
interface HasColor{} 
class Dimension{public int x,y,z}
class Colored<T extends HasColor>{}
class ColoredDimension<T extends Dimension & HasColor>{} //顺序必须是先类型后Iterface
```

## 15.10 通配符
