# 第十八章 Java I/O 系统

## 18.1 File类
既可以代表一个文件名称，又可以表示一个路径下所有文件。
```java
File path = new File(".")
```

### 18.1.1 目录列表器
可以过滤目录中不符合要求的文件，具体用法为实现`FilenameFilter`接口。
```java
static class DirFilter implements FilenameFilter{

	private Pattern pattern;

	public DirFilter(String regex) {
		pattern = Pattern.compile(regex);
	}

	@Override
	public boolean accept(File dir, String name) {
		// TODO Auto-generated method stub
		return pattern.matcher(name).matches();
	}


list = path.list(new DirFilter(args[0]));
```

#### 匿名内部类
`FIlenameFilter`也可以用匿名内部类实现。

### 18.1.2 目录实用工具
本节通过`listFiles`和`isDirectory`递归遍历整个目录结构。

### 18.1.3 目录的检查和创建
介绍了一些文件操作方法。
```java
	public static void fileData(File f) {
		System.out.println( "Absolute path: " + f.getAbsolutePath() +
			      "\n Can read: " + f.canRead() +
			      "\n Can write: " + f.canWrite() +
			      "\n getName: " + f.getName() +
			      "\n getParent: " + f.getParent() +
			      "\n getPath: " + f.getPath() +
			      "\n length: " + f.length() +
			      "\n lastModified: " + f.lastModified());
			    if(f.isFile())
			      System.out.println("It's a file");
			    else if(f.isDirectory())
			      System.out.println("It's a directory");
	}

```
还有文件的创建`mkdir`和删除`delete`(要先检查是否存在`exists`)和重命名`renameTo`等操作。

## 18.2 输入和输出
Java类库设计者设计所有输入相关类都应该从`InputStream`继承，所有输出相关都应该从`OutputStream`继承。

### 18.2.1 InputStream类型
Inputstream 的作用是用来表示那些从不同数据源产生输入的类。这些数据源包括：

- 字节数组
- String 对象
- 文件
- 管道
- 一个由其他种类的流组成的序列。以便我们可以将他们收集合并到一个流内。
- 其他数据源，比如网络上获取的。
- 装饰器过滤器。

### 18.2.2 OutpuStream类型

输出可以向字节，文件，管道，一个过滤用装饰器
