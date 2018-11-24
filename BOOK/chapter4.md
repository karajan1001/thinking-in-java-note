# 控制执行流程
总体上和`c`或者`cpp`很像。除了两个地方:

## 4.4 foreach 语法
允许类似Python 对一个可迭代对象进行迭代。使用方法为

```java
for (foat x:y)
    System.out.println(x);
```

## 4.7 goto
Java没有goto，虽然goto在java中作为了保留关键词存在。在多重迭代时，使用标签可以让你直接跳出多重迭代回到标签所在的地方。

```java
outer{
  inner{
    break; // 结束inner 循环
    continue; // 回到inner 循环开头
    continue label1; // 回到outer循环开头，继续outer循环。
    break label; // 结束外部循环。
  }
}
