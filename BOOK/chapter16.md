# 第十六章 数组

## 16.1 数组为什么特殊
数组和其他容器三区别。现在只剩效率。
- 效率: 效率比容器高，大小固定，生命周期内不可变。
- 类型: 容器不用泛型则都是`Object`类型。数组则持有具体类型。(目前已经一样）
- 保存基本类型的能力: 数组可以持有基本类型，泛型之前的容器不可以。(目前已经一样）

## 16.2 数组是第一级对象
数组是一个引用。可以通过数组初始化或者`new`创建对象。length是对象的一部分（这点和c中不同，c中数组长度需要单独传入)。`[]`是访问数组元素的唯一方法。对象数组和基本类型数组唯一区别在于，对象数组是保存引用，而基本类型数组则保存基本类型值。
```java
Class A{}
A[] a; // 定义
a = new A[]{new A(), new A()}; // 初始化并赋值
A[] b = new A[10]; // 定义并赋值
for(int i = 0; i < b.length; i++)
    if(b[i] == null) // 初值都为null
        c[i] = new A(); // 赋值

```

如果是基本类型数组则自动初始化为`0`，否则为`null`。

## 16.3 返回一个数组

和c或者c++不同返回数组，不需要为清理数组的空间烦恼，而且数组本身也包含了数组的大小。

## 16.4 多维数组

创建多维数组和c中类似，赋值则使用了java特色。
```java
int[][] a = {{1,2,3},
             {4,5,6}};
System.out.println(Arrays.deepToString(a)); // 可以将多维数组转为正确的String。
```

数组中构成矩阵的每个向量可以为任意长度（粗糙数组）

## 16.5 数组和泛型
数组和泛型通常不能很好结合, 比如不能创造一个具有参数化类型的数组,因为数组必须知道确切类型来决定空间分配，一个容器占用的空间是无法确定的。但是可以参数化数组本身, 还有可以创造一个不带参数的数组然后将其转型。

```java
// List<String>[] ls = new List<String>[];  error
List<String>[] ls ; 
List[] la = new List[10];
ls = (List<String>[]) la; // 会报警但是可以转型成功
```

> 泛型容器总是比泛型数组更好的选择。

## 16.6 创建测试数据

在实验数组和程序的时候，可以用特殊方法和对象来填充。

### 16.6.1 Arrays.fill()
此条命令可以填充整个数组或者数组中某个区域。
```java

int[] a5 = new int[6];
Arrays.fill(a5, 19);
int[] a9 = new String[6];
Arrays.fill(a9, 'Hello');
Arrays.fill(a9, 3, 5, 'World');
print(" a5 = " + Arrays.toString(a5));
print(" a9 = " + Arrays.toString(a9));
```
比如结果为
```bash
> a = [19,19,19,19,19,19]
> a9 = [Hello, Hello, Hello, World, World, Hello]   
```

### 16.6.2 数据生成器

```java
public class CountingGenerator{
    public static class Boolean implements Generator<java.lang.Boolean> {
        private boolean value = false;
        public java.lang.Boolean next()P
            value = !value;
            reutrn value;
        }
}
```
然后一直调用next就能生成值。

### 16.6.3 从Generator中创建数组
可以使用`CollectionData`用生成器来填充数组。
```java
new CollectionData<T>(gen, length).toArray(a);
```

## 16.7 Arrays 实用功能o
###  16.7.1 赋值数组
```java
int[] i = new int[7];
int[] j = new int[10];
Array.fill(i, 47);
Array.fill(j, 99);

System.arraycopy(i, 0, j, 0, i.length); // 从 i的0开始往j的0复制i.length个元素

```

### 16.7.2 数组的比较
```java
Arrays.equals(a1,a2); // 每个元素都相等才相等
```

### 16.7.3 数组元素的比较
实现了`Comparable`接口的类可以使用标准库的`Arrays.sort()`比较并排序

### 16.7.4 数组的排序
排序使用的是`quicksort`算法

### 16.7.5 在已排序的数组中查找
对于已经排序的数组可以使用`Arrays.binarySearch()`进行快速查找


## 16.8 总结

本章是Java对固定的低级数组提供了适度支持。在所有的场合都应该优先使用容器而不是数组，除非容器的性能称为瓶颈。
