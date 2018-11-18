#  第二章 一切都是对象

## 2.1 用引用操作对象
和Cpp不同，Java操作都是对象的引用(本质上是指针，使用起来更像cpp中的引用)，不用注意是对象引用还是对象的指针。

## 2.2 必须由你创建所有对象
创建一个引用需要有对应的对象与之关联。通常使用`new`关键词实现这一点。

### 2.2.1 存储到什么地方

1. 寄存器：最快（c和c++可以像编译器建议寄存器的分配）。
2. 堆栈：位于RAM先进后出，一些JAVA对象引用存储在其中。
3. 堆：创建的对象都在其中，速度较慢。
4. 常量：代码中的常量直接存放在代码内部。
5. 非RAM存储：存活于程序之外，不受程序控制包括流对象和持久化对象。流对象被转化为字节流，持久化对象存放在磁盘上。

### 2.2.2 基本类型 

| 基本类型| 大小 | 最小值 | 最大值 |  包装器类型 |
|:--:|:--:|:--:|:--:|:--:|
|boolean| - | - | - | Boolean|
|char| 16 bit | Unicode 0 | Unicode $2^{16} - 1$ | Character |
|byte| 8 bit | -128 | 127 | Character |
|short| 16 bit | $-2^{15}$ |$2^{15}-1$ | Character |
|int| 32 bit | $-2^{31}$| $2^{31}-1$| Character |
|long| 64 bit |$-2^{63}$|$2^{63} -1 $ | Character |
|float| 32 bit | IEEE754 | IEEE754 | Character |
|double| 64 bit | IEEE754 | IEEE754 | Character |
|void| - | - | - | Character |

还有两个任意精度的整数`BigInteger`和浮点数`BigDecimal`

### 2.2.3 JAVA 中的数组

JAVA在数组被初始化后不能在它的范围之外被访问，这是以内存开销和运行时下标检查为代价的。

## 2.3 永远不要销毁对象

### 2.3.1 作用域
**作用域**(scope)在C中可以用花括号来表示
```c
{
    int x = 12;
    {
        int q = 96;
    }
    // 这里的q是不能引用的。
}
```
但是JAVA中不能用这种方法。

### 2.3.2 对象的作用域

JAVA中new的对象，引用会随作用域消失，但是对象本身会一直存在下去。

## 2.4 创建新的数据类型：类

大多数面向对象的程序设计语言习惯用`class`表示之后跟的是新类型的名称。

```java
class ATyeName { /*class body*/}
```

之后可以用`new`来创建一个此类型的对象。
```java
AtypeName a = new ATypeName()
```

### 2.4.1 字段和方法

定义类后可以在类中设置两种基本类型**字段**和**方法**。类中的字段有默认值，而局部变量是没有默认值的。
```java
class DataOnly{
    int i;
    double d;
    boolean b;
}
```

## 2.5 方法,参数和返回值

JAVA的方法对应C和Cpp中的函数或者也叫子程序。方法的基本构成为
```java
ReturnType methodName( /* Argument list */){
    /* Method body */
}
```

Java的方法只能作为类的一部分来创建。方法只有通过对象才能被调用。

### 2.5.1 参数列表
对于不想返回任何数值的方法，可以采用返回`void`

## 2.6 构建一个Java程序
### 2.6.1 名字可见性
JAVA希望程序员反转自己的internet域名给库生成一个独一无二的名称，比如：`net.mindview.utitlliy.foibles`

### 2.6.2 运用其他构件
```java
import java.util.ArrayList //导入其他类库
import java.util.* //导入全部类库
```

### 2.6.3 static关键词
通过static 方法可以创建`类方法`或者`类数据`，这些方法可以不用创建对象就能调用。
```java
class StaticTest {
    static int i = 47;
    static void increment() {StaticTest.i++;}
}
```

调用类方法时最好使用类名直接调用，这样可以获得编译器更好的支持。

## 2.7 第一个JAVA程序
为了让程序是一个可以独立运行的程序。需要满足：
1. 文件名和类名相同。
2. 必须包含一个叫`main`的方法。
3. `main`方法必须是`public`方法，参数必须为`String[] args`

### 2.7.1 编译和运行
```bash
javac HelloData.java #  编译
java HelloData # 运行
```

## 2.8 注释和嵌入式文档

文档两种方法
```java
// 单行法
/* 多行
方法 */
```

### 2.8.1 注释
可以使用Javadoc

### 2.8.2 语法
使用
```java
/** */
```
来标识,案例为
```java
//: orj/Document.java
/** class comment */
public class Document{
    /** field comment */
    public int i;
    /** method comment */
    public void f(){
    }
}
```

### 2.8.3 嵌入式HTML
```html
<em> even</em>
<ol>
<li> Item one
<li> Item two
<li> Item three 
</ol>
```

### 2.8.4 一些标签示例

1. @see 引用其他类
2. @link 类似see
3. @dockRoot 文档根目录相对路径
3. @inheritDoc 文档继承
4. @version 版本
6. @author 作者
7. @since 指定代码最早使用JAVA版本
8. @param 方法文档参数。
9. @return 方法文档返回值。
10. @throws 方法文档异常处理。
11. @deprecated 方法文档建议不要使用的旧特性。

## 2.9 编码风格
全部使用驼峰命名，类名大写开头，其他小写开头

## 2.10 总结


