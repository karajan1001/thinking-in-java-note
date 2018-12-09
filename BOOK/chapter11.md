# 第十一章　持有对象

Java使用类库提供了一套完整的容器类来解决存储动态数量对象的问题。包括`List`, `Set`, `Queue`和`Map`。

## 11.1 泛型和类型安全的容器
使用泛型可以制定容器可以保存的类型，可以再编译器组织错误类型的对象放入容器中。而且取出时候不再需要强制类型转换，更可以使用`foreach`语法。具体方法是使用`<>`将类型包含其中。

```java
ArrayList<Apple> apples = new ArrayList<Apple>()
for (int i = 0; i < 3; i++)
    apples.add(new Apple());
for (int i = 0; i < apples.size(); i++)
    System.out.println(apples.get(i).id());
for (Apple c : apples)
    System.out.println(c.id());
```

## 11.2 基本概念
Java容器是用来保存对象的，有两种
1. Collections: 独立元素的序列，`List`,`Set`,`Queue`按照排队规则确定对象顺序。
2. Map: `ArrayList`，允许用数字来查找值，`映射表`允许用另一个对象来查找对象。 

> Set 同 Python中 set, ArrayList同Python中list, Map同Python中dict

## 11.3 添加一组元素
`Array.asList`方法接受一个数组或者一个逗号分割的元素列表将其变成一个`List`对象。但是此时底层依然是数组，所以不接受`add`和delete`方法
使用`Colleciton.addAll`方法可以接受一组数组，以及一个数组或者是一个逗号分割列表将元素都加入到Collection中去。

## 11.4 容器的打印
容器直接打印就可以获得比较好的可读性。

## 11.5 List
`List`可以将元素维护在特定的序列中。有两种类型的`List`:
1. ArrayList 适合随机访问元素，插入删除比较慢。 （类似Array数组）
2. LinkedList 插入删除很简单，但是随机访问会比较慢。（类似链表）

支持的方法有
- contains 判断是否存在表中。 类似Python中的 `in`，也是一样用`id`判断是否相等。
- remove 接受引用然后删除它。 
- subList 方法可以从较大的列表中选取一个片段。类似Python中的[a:b]
- retainAll 保留交集。
- removeAll 移除所有元素
- set 赋值。类似Python中的List[a] = b
- isEmpty 是否为空。
- clear 清空。
- toArray　转换为数组可以使用`[]`来取索引，而不需要用`.get`方法了

## 11.6 迭代器
容器遍历采用迭代器设计模式。
1. 方法`iterator`返回一个迭代器，并准备好第一个元素。
2. `next`可以获取下一个元素。
3. `hasNext`判断是否遍历完全。
4. `remove`删除最近返回的元素。

### ListIterator
迭代器的子集，可以双向移动的迭代器，并且可以用`set`方法替换刚访问的最后一个元素。

## 11.7 LinkedList
- getFirst 读取第一个
- removeFirst 读取第一个并删除
- removeLast移除最后一个元素
- addFirst 插入元素头
- addLast 将元素插入列表的尾部

## 11.8 Stack
一种<font color="red">LIFO</font>的容器。主要使用`push`和`pop`压入和取出元素，已经过时。

## 11.9 Set
Set与`Collections`的接口完全一致，除了行为上有所不同。测试元素是否是集合的一部分，可以使用`HashSet`实现。`TreeSet`使用红黑树查询比`HashSet`的散列慢，但是有顺序。`LinkedHashSet`也使用了散列加快查询，但是使用链表来维护元素的插入顺序。

## 11.10 Map
可以很容进行组合，变成多维容器。主要支持
- put
- get

## 11.11 Queue
一种<font color="red">FIFO</font>的容器。主要使用`push`和`pop`压入和取出元素。支持的操作有
- offer　插入队尾
- peek/element 返回队头
- poll/remove 返回队头并移除队头

### 11.11.1 PriorityQueue
给定一组队列元素时确定下一个弹出元素，普通的Queue是等待时间最长的那个最有限。可以提供`Comparator`来修改队列返回优先规则。

## 11.12 Collection与Iterator
在Java中用迭代器而不是`Collection`来表示容器间的共性。

## 11.13 Foreach与迭代器
`Foreach` 可以用于任何`Collection`对象。任何实现了`Iterable`的类都可以用于foreach语句中。

### 11.13.1 适配器方法惯用法
要为更多类提供foreach语法支持可以使用适配器，添加一个能够产生`Iterable`的对象。

## 11.14 总结
见图![Java容器](http://p.blog.csdn.net/images/p_blog_csdn_net/icesnows/EntryImages/20090617/simpleContainerTaxonomy.JPG)
