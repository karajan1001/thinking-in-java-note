# 第十三章 字符串

## 13.1 不可变String
和Python一样String都是不可变的。

## 13.2 重载 "+" 与StringBuilder
和Python类似，每个带有+的语句都会重新生成一个字符串。所以使用StringBuilder可以显著提高性能。

```java
StringBuilder result = new StringBuilder();
for (int i =0;ilen; i++)
    result.append(fields[i]);
return result.toString()
```

## 13.3 无意识的递归
覆写`toString`方法可能引起无意识的递归调用。使用`"some string" + this`可能导致this进行类型转换转换成`string`类型，引起无限递归。

## 13.4 String上的操作
主要有
- charAt: 取索引(返回char)
- getChars: 取范围索引（返回数组）
- equals: 比较内容
- contains: 是否包含
- startsWith: 是否以为开始
- endsWith: 是否以为结束
- substring: 取范围（返回String)
- concat: 拼接
- replace: 替换掉字符
- toLowerCase/toUpperCas: 变成全大写或者全小写
- trim: 去除两端的空白字符串

## 13.5 格式化输出
类似c中 `printf` 的格式化输出方式

### 13.5.1 printf()
c中输出方式

### 13.5.2 System.out.format()
此方法类似C中的`printf`与`System.out.printf`等价。

### 13.5.3 Formatter类
JAVA中标准输出方法是使用Formatter类。接受输出地址`System.out`等，和输出格式（类似format）。

### 13.5.4 格式化说明符
和C中类似，可以控制总长度和总精度。
### 13.5.5 Formatter转换
和C中类似，可以控制数据类型。

### 13.5.6 String.format
是一个`static`方法可以替代`System.out.format`。

## 13.6 正则表达式

### 13.6.1 基础
JAVA中的正则表达式不是单个`\`而是`\\`。如果要使用一个普通的`\`则需要输入`\\\\`。java中支持`regex`的方法有`matches`, `split`, `replace`

### 13.6.2 创建正则表达式
`Pattern`类似python中的`re`可以编译正则表达式。之后使用`matcher`方法输入需要匹配的字符串返回一个`Matcher`。再往后可以用`find`, `split`, `matches`等方法。详细内容参考通用`regex`

## 13.7 扫描输入
Scanner可以接受任何具有`read`的方法。然后使用`nextLine`,`nextInt`，`nextDouble`等方法扫描下一个匹配字符。

### 13.7.1 scanner界定符
`scanner.useDelimiter`可以接受正则表达式来自定义分词符

### 13.7.2 用正则表达式扫描
`scanner.next`可以接受正则表示式来扫描下一个匹配字符串。

## 13.8 StringTokenizer
旧时用的字符串切分工具，已经被scanner和正则表达式替代。

## 13.9 总结
