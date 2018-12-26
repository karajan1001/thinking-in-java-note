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

## 18.3 添加属性和有用的接口
`FilterInputstream`和`FilterOutputStream`两个类是用来提供装饰器接口控制特定输入输出的类。

### 18.3.1 通过FilterInputstream从InputStream读取数据
可以做两种不同事情，
1. 通过DataInputStream读取不同数据，迁移到其他地方。
2. FilterInputStream则可以修改`InputStream`的行为。

### 18.3.2 通过FilterOutputstream从OutputStream读取数据
与Input想对应。

## 18.4 Reader和Writer
Input和OutputStream在面向字节形式的I/O中继续工作，Reader和Writer则面向字符。之前很多Stream类已经被Reader类型取代。不过在某些特殊情比如`zip`文件还是必须用`Stream`类型读取。

## 18.5 自我独立的类: 自我独立的类，RandomAccessFile
`RandomAccessFile`适合操作大小已知的文件，可以通过`seek`跳转到相应位置做出修改。只支持文件类型，大多数功能都被`nio`取代。

## 18.6 I/O流的典型使用方法

### 18.6.1 缓冲输入文件
打开文件，一次读一行，传给`StringBuilder`。文件末尾`readline`会返回`null`
```java
BufferedReader bReader = new BufferedReader(new FileReader(filename));
String string;
builder = new StringBuilder();
while ((string = bReader.readLine())!= null) {
	builder.append(string+"\n");
}
bReader.close();
return builder.toString();
```
### 18.6.2 从内存输入
下面的示例中，从 BufferedInputFile.read() 读入的 String 结果被用来创建一个 StringReader。然后调用 read() 每次读取一个字符，并把它发送到控制台。

```java
StringReader sReader = new StringReader(BufferedInputFile.read("D:/eclipseWorkspace/ThreadTest/src/ioone/MemoryInput.java"));
int c;
while ((c = sReader.read()) != -1) {
	System.out.print((char)c);
}
```

### 18.6.3 格式化的内存输入
要读取格式化的数据，可以使用 DataInputStream，它是一个面向字节的 I/O 类。因此我们使用 InputStream 类而不是 Reader 类。当然，我们可以用 InputStream 以字节的形式读取任何数据，不过，我们在这里使用的是字符串而已。可以用`available`判断是否读取完毕。

### 18.6.4 基本的文件输出
FileWriter 对象可以向文件写入数据。首先，创建一个与指定文件链接的 FileWriter。实际上，我们通常会使用 BufferedWriter 将其包装起来用以缓冲输出。


```java
PrintWriter out = new PrintWriter(new BufferedWriter(new FileWriter(fiString)));
out.println(lineCount++ + ":" + string); // 输出
out.close(); // 关闭文件刷新缓冲
```

### 18.6.5 存储和恢复数据
这些方法可以在读取数据时进行格式化操作。
```java
readDouble();
readUTF();
readDouble();
readUTF();
```

### 18.6.6 读写随机访问文件
在使用 RandomAccessFile 你必须知道文件的排版，这样才能正确的操作它。RandomAccessFile 拥有读取基本类型和 UTF-8 字符串的各种具体方法。

### 18.6.7 管道流
PipedInputStream、PipedOutputStream、PipedReader、PipedWrited 的价值会在我们后边的多线程中体现。因为管道流用于任务之间的通讯。

## 18.7 文件读写的实用工具

本节是一个简单的可以读文件自动加上换行符，最后输出到其他文件的类。

### 18.7.1 读取一个二进制文件
同上文本文件，对读取写入做了一些封装。

## 18.8 标准I/O
参考的是 Unix 中 “程序所使用的单一信息流” 这个概念。程序的所有输入都可以来自于标准输入，它的所有输出都可以发送到标准输出，以及所有的错误信息都可以发送到标准错误。标准 I/O 的意义在于：我们可以很容易的把程序串联起来，一个程序的标准输出可以成为另一个程序的标准输入。

### 18.8.1 从标准输入中读取
`out`和`err`都被事先包装成了`PrintStream`，但是`System.in`没有包装过的`InputStream`。读取`System.in`之前必须对其进行包装。
```java
BufferedReader std = new BufferedReader(new InputStreamReader(System.in));
String string;
try {
	while ((string = std.readLine()) != null && string.length() != 0) {
		System.out.println(string);
	}
} catch (IOException e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
}
```
### 18.8.2 将System.out转为PrintWriter
可以通过构造器将`Syatem.out`转换成`PrintWriter`。
```java
PrintWriter out = new PrintWriter(System.out,true);
```

### 18.8.3 标准I/O重定向
可以用如下方法重定向输入输出。
- setIn(InputStream)
- setOut(PrintStream)
- setErr(PrintStream)
```java
BufferedInputStream inputStream = new BufferedInputStream(new FileInputStream("D:/eclipseWorkspace/ThreadTest/src/ioone/Redirecting.java"));
PrintStream out = new PrintStream(new BufferedOutputStream(new FileOutputStream("test.out")));
System.setIn(inputStream);
System.setOut(out);
```

## 18.9 进程控制
运行程序需要使用`OSExecute.command()`。而捕获标准获程序执行时产生的标准输出流，需要调用`getInputStream()`。

## 18.10 新I/O
新I/O类库在JKD1.4中引入，存在于`java.nio.*`中，目的是提高速度。主要是使用了缓冲区, 引入了`ByteBuffer`类用于存储未加工的比特流。`FileInputStream`、`FileOutputStream`以及`RandomAccessFile`被修改用以产生`FileChannel`。`java.nio.channels.Channels`类提供了实用方法，用以在通道中产生`Reader`和`Writer`。 
```java
// 写文件
FileChannel fChannel = new FileOutputStream("data.txt").getChannel();
fChannel.write(ByteBuffer.wrap("Some text".getBytes()));
fChannel.close();

// 读写文件
fChannel = new RandomAccessFile("data.txt","rw").getChannel();
fChannel.position(fChannel.size());
fChannel.write(ByteBuffer.wrap("Some more".getBytes()));
fChannel.close();

//读文件
fChannel = new FileInputStream("data.txt").getChannel();
ByteBuffer buffer = ByteBuffer.allocate(BSIZE);
fChannel.read(buffer);
buffer.flip();
while (buffer.hasRemaining()) {
	System.out.print((char)buffer.get());
}
```
读取数据到`ByteBuffer`可以使用`put`或者`wrap`。写入数据则要确定缓存大小使用`allocate`。缓存的大小影响`nio`模块的性能。

### 18.10.1 转换数据

读取和写入的`ByteBuffer`都是字节的形势，要将其变成字符需要编码和解码。解码可以使用`java.nio.CharBuffer`中的`toString`，编码使用`getBytes`方法后面带上需要编码的类型。
```java
buff = ByteBuffer.allocate(24); // More than needed
buff.asCharBuffer().put("Some text");
```

### 18.10.2 获取基本类型
可以使用:`asCharBuffer`,`asShortBuffer`,`asLongBuffer`,`asIntBuffer`,`asFloatBuffer`。写入基本类型数据

### 18.10.3 视图缓冲器
视图缓冲器可以让我们通过某种特定的基本数据类型的视窗查看器底层的 ByteBuffer。可以像C 一样控制每个基本类型占据的比特位。

#### 字节存放次序



