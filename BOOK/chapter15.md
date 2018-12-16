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
数组对像互相赋值，其实做的是将一个数组赋值给另一个数组。而且前一个数组能保留有关它所包含对象类型的规则。而泛型容器则会在编译时直接报错。但是可以通过通配符`? extends`告诉容器泛型的信息。但是这样以后以后就无法向其中添加对象了。因为`add`方法的参数编程了`? extends`，不知道应该添加哪种具体类型。编译器无法检查类型安全所以直接报错。
```java

interface HasColor{} 
class Dimension{public int x,y,z}
class ColoredDimension extends Dimension impletments HasColor{} 
Colored[] colors = new ColoredDimension[10]; // OK
// colors[0] = new Dimension() 这样做编译没问题，运行会报错。 
// List<Diemnsion> colors = new ArrayList<ColoredDimension>(); // 编译报错
List<? extends Diemnsion> colors = new ArrayList<ColoredDimension>(); // 可以运行
colors.add(new Dimension()) // 会报错，使用通配符
colors.add(new ColoredDimension()) // 会报错，使用通配符无法添加对象
colors.add(null); //可以添加，但是毫无意义
Dimension a = colors.get(0); //可以这样

```

### 15.10.1 编译器有多聪明

但是有些方法比如`contains`和`indexOf`等接受参数是`Object`所以可以正常运行。

### 15.10.2 逆变
如果使用超类型通配符`? super MyClass`则可以正常使用`add`等方法。

### 15.10.3 无界通配符
<?>无界通配符，表示可以持有任何类型的对象。

### 15.10.4 捕获转换
有一种情况需要使用无界通配符，而不是原生类型。需要回转调用另一个使用确切类型的方法。

## 15.11 问题

### 15.11.1 任何基本类型都不能作为类型参数

不能创建一个`ArrayList<int>`类型的容器。解决方法是使用自动包装机制，`ArrayList<Integer>`　但是自动包装机制无法应用于数组。

### 15.11.2 实现参数化接口

因为擦除的原因，一个类不能实现同一个泛型接口两次。
```java
interface A<T> {}
class B implements A<B> {}
class C extents B implements A<C> {}; //　会报错，因为A<B>和A<C>擦除后是同一个接口。

```

### 15.11.3 转型和警告
为了消除在转型过程中的警告，可以使用泛型类转型。
```class
class Fixed<T>{
    private Object[] storage;
    public void push(T item) { storage[index++] = item;}
    public T pop() { return (T)storage[--index];} //　收到警告，内部存储为Object。
}
```
泛型类转型
```java
List<A> a2 = List.class.cast(List<B> a1)
```
### 15.11.4 重载
泛型类型不能用于重载，因为它们被当成同一个类型。
### 15.11.5 基类劫持了接口
这是由于15.11.2问题导致的。

## 15.12 自限定的类型

```java
class SelfBounded<T extends Selfbounded<T>>{//...}
```
主要作用是产生参数协变。
## 15.12.1 古怪的循环泛型
todo
## 15.12.2 自限定
## 15.12.3 参数协变
## 15.13 动态类型安全
可以使用动态类型安全检查保证动态类型相同。用来检查老版本代码。
```java
static void oldStylemethod(List probablyDogs){
    probablyDOgs.add(new Cat());
}
List<Dog> dogs1 = new ArrayList<Dog>();
List<Dog> dogs2 = Collections.checkedList(new ArrayList<Dog>(), Dog.class);
oldStylemethods(dogs1) // OK
oldStylemethods(dogs2) // Throw an exception

```

## 15.14 异常 
由于擦除的原因，泛型在异常那受限严重。
## 15.15 混型
todo
### 15.15.1 C++中的混型
### 15.15.2 于接口混合
### 15.15.3 使用装饰器模式
### 15.15.4 与动态代理混合
## 15.16 潜在类型机制
就是python里面所说的鸭子类型。只要实现了某些方法就能当成某种类型。而java是不支持潜在类型机制的。
## 15.17 对缺乏潜在类型机制的补偿
没有潜在类型机制Java依然可以创建真正的泛型代码
### 15.17.1 反射 
可以通过反射的方法，实现潜在类型机制。
```java
public static void perform(Object speaker){
    Class<?> spkr = speaker.getClass();
    try{
        try{
            Method speak = spkr.getMethod("speak");
            speak.invoke(speaker);
        } catch (NoSuchMethodException e){
            print(speaker + " cannot speak ");
        }
        
    } catch (Exception e){
        throw new RuntimeException(speaker.toString(), e);
    }
}
        
```
### 15.17.2 将一个方法用于序列
可以用反射实现，但是运行时候检查会比非反射实现慢很多。
### 15.17.3 当你并未碰巧拥有正确的接口时
如果类型不匹配，比如虽然`SimpleQueue`也有`add`方法，但是定义为
```java
public static <T> void fill(Collection<T> collection, Class<? extends T> classToken, int size){
    for (int i = 0; i<size;i++)
    try{
        collection.add(classToken.newInstance());
    }catch(exception e){
    }
}
```
的方法依然无法正常工作
### 15.17.4 用适配器仿真潜在类型机制
可以使用**适配器模式**实现。（当时看设计模式还有python中的设计模式代码就很奇怪，觉得多此一举，原来这是为没有鸭子类型的语言设计的）
todo
## 15.18 将函数对象用作策略

本例使用了**策略模式**。（Python中函数也是对象，比策略模式更为方便）
todo
## 15.19 总结: 转型真的如此糟糕吗
> 泛型类型机制最吸引人的地方就是可表达性。类型安全的容器是能够创建更为通用代码所带来的副作用。

