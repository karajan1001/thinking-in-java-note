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
不同的机器，可能采用不同的字节排序方式来存放数据。

### 18.10.4 用缓冲器操纵数据
`ByteBuffer`使用`wrap`包装数据然后通过`channel`将数据输出到文件中。

### 18.10.5 缓冲器的细节
`Buffer`由数据和可以高效的访问以及操作这些数据的四个索引组成，这四个索引是：mark (标记)、position (位置)、limit (界限) 和 capacity (容量)。
```java
while(buffer.hasRemaining()) {
	//将mark 设置为 position
  buffer.mark();
  char c1 = buffer.get();
  char c2 = buffer.get();
  buffer.reset(); // 将 position 设为 mark
  buffer.put(c2).put(c1);
}
```
### 18.10.6 内存映射文件
有了内存映射文件，我们就可以假定整个文件都放在内存中，而且可以完全把他当做非常大的数组来访问。这种方法极大的简化了修改文件的代码。这里需要使用`MappedByteBuffer`
```java
MappedByteBuffer out =new RandomAccessFile("test.dat", "rw").getChannel().map(FileChannel.MapMode.READ_WRITE, 0, length);
```
#### 性能
相比`io`,`nio`可以显著提升性能。
### 18.10.7 文件加锁
JDK 1.4 引入了文件加锁的机制，它允许我们同步访问某个文件作为共享资源的文件。通过对 FileChannel 调用 tryLock() 或者 lock()，就可以获得整个文件的 FileLock。tryLock() 是非阻塞式的，它设法获取锁，但是如果不能获取将直接从方法调用处返回。lock() 则是阻塞式的，他要阻塞进程直至锁可以获得，或调用 lock() 的线程中断，或调用 lock() 的通道关闭。使用 FileLock.release() 可以释放锁。具体使用为
```java
FileLock fLock = fOutputStream.getChannel().tryLock();
if (fLock != null) {
	System.out.println("加锁文件");
	TimeUnit.MILLISECONDS.sleep(100);
	fLock.release();
	System.out.println("解锁");
}
fOutputStream.close();
```
除了对整个文件加锁还能只对文件的一部分进行，这时候需要传入参数位置和大小。

## 18.11 压缩
压缩当然都是字节不是字符,最常用是Zip和Gzip。

### 18.11.1 利用 GZIP 进行简单压缩
使用方法比较简单，在`buffer`和`file`之间加上一层Gzip即可。

```java
BufferedOutputStream outputStream = new BufferedOutputStream(new GZIPOutputStream(new FileOutputStream("test.gz")));
BufferedReader ins = new BufferedReader(new InputStreamReader(new GZIPInputStream(new FileInputStream("test.gz"))));

```

### 18.11.2 用 ZIP 进行多文件保存
支持 ZIP 格式的 Java 库更加全面。利用该库可以方便的保存多个文件，他甚至有一个独立的类，使得读取 ZIP 文件更加方便。
```java
ZipFile zf = new ZipFile("test.zip");
ZipEntry ze;	
```
对于每一个要加入压缩文档的文件，都必须调用 putNextEntry()，并将其传递一个 ZipEntry 对象。ZipEntry 对象包含了一个很广泛的接口，允许你获取和设置 ZIP 文件内该特定项上所有可利用的数据：名字、压缩和未压缩的文件大小、日期、CRC 校验和、额外字段数据、注释、压缩方法以及它是否是一个目录入口等等。
### 18.11.3 Java 档案文件
Zip 格式也被广泛应用于 JAR 文件格式中。这种文件格式就像 Zip 一样，可以将一组文件压缩到一组文件中。同 Java 中其他文件一样， JAR 文件也是跨平台的。声音和图像文件可以像类文件一样被包含在其中。

## 18.12 对象序列化
Java 对象序列化将那些实现了 Serializable 接口的对象转换为一个字节序列，并能够在以后将这个字节序列完全恢复为原来的对象。这样对象可以跨网络，跨平台重构出来。利用对象的序列化可以实现轻量级持久性。只要对象实现了 Serializable 接口。序列化就实现了，这是非常简单的。要序列化一个对象首先要创建某些 OutputStream 对象，然后将其封装成一个 ObjectOutputStream 对象内。这时，调用 writeObject() 即可将对象序列化，并将其发送给 OutputStream。要反向进行该过程，需要将一个 InputStream 封装在 ObjectInputStream 内，然后调用 readObject()。和之前一样我们会获得一个引用，它指向一个向上转型的 Object，所以必须向下转型才能直接设置它们。Java对象的序列化还会自动追踪对象持有的其他对象并对他们进行序列化。最后形成一张对象网。
```java
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("worm.out"));
out.writeObject(w);
out.close();

ObjectInputStream in = new ObjectInputStream(new FileInputStream("worm.out"));
String string = (String)in.readObject();
Worm w2 = (Worm)in.readObject();
```

## 18.12.1 寻找类
要实现反序列化，除了得到序列化后的文件，远程机器还必须能得到这个类的定义。所以类的路径必须是 Java 虚拟机能够找到的路径。

## 18.12.2 序列化的控制
要精确控制序列化，可以通过实现 Externalizable 接口代替实现 Serializable 接口。 Externalizable 接口继承了 Serializable 接口，同时添加了两个方法：writeExternal() 和 readExternal()。这两个方法会在序列化和反序列化还原的过程中被自动调用，以便执行一些特殊的操作。。对于 Serializable 对象完全以它存储的二进制为基础来构造，而不调用构造器。而对于一个 Externalizable 对象，所有的普通的默认构造器都会被调用，然后调用 readExternal()。必须注意这一点：所有的默认构造器都会被调用，才能使 Externalizable 对象产生正确的行为。
```java
protected Blip3(int i, String s) {
	super();
	System.out.println("有参数的构造函数");
	this.i = i;
	this.s = s;
}

@Override
public void writeExternal(ObjectOutput out) throws IOException {
	System.out.println("序列化对象");
	out.writeObject(s);
	out.writeInt(i);

}

@Override
public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
	System.out.println("恢复对象");
	s = (String)in.readObject();
	i = in.readInt();
}

```

要完整恢复一个对象必须实现它的构造器，序列化时需要将参数手动写入，读取时需要手动读取，不然参数会是默认值而不会恢复。

#### transient (瞬时) 关键字

如果不想自己实现`Externalizable`又想在序列化时不保存某些对象，可以使用`transient`将其设为瞬时字段。


```java
public class Logon implements Serializable{

	  private Date date = new Date();
	  private String username;
	  //不需要保存
	  private transient String password;
	  public Logon(String name, String pwd) {
	    username = name;
	    password = pwd;
	  }
}
```

#### Externalizable 的替代方法
除了使用`Externalizable`,还有另外一种方法就是添加名为 writeObject() 和 readObject() 的方法。注意是添加既不是覆盖也不是实现。这样在序列化时会自动调用而不是默认的自动化机制。

```java
public class SerialCtl implements Serializable {
	  private String a;
	  private transient String b;
	  public SerialCtl(String aa, String bb) {
	    a = "Not Transient: " + aa;
	    b = "Transient: " + bb;
	  }
	  public String toString() { return a + "\n" + b; }
	  private void writeObject(ObjectOutputStream stream)throws IOException {
	    stream.defaultWriteObject(); // 保存a
	    stream.writeObject(b); //保存b
	  }

	  private void readObject(ObjectInputStream stream)throws IOException, ClassNotFoundException {
	    stream.defaultReadObject();
	    b = (String)stream.readObject();
	  }
}

```
#### 版本控制

有时可能想要改变可序列化的版本。虽然 Java 支持这种做法，但是却可能只在特殊情况下才这么做，此外，需要对它有相当深的了解。这是因为 Java 的版本控制机制过于简单，因而不能在任何场合可靠的运转。需要深入了解看 JDK 的文档。

### 18.12.3 使用持久化
如果我们将两个对象序列化，他们都具有指向第三个对象的引用。当我们从序列化状态恢复这两个对象时系统无法知道另外一个流内的对象别名，因此会产生不同的对象网。如果我们想保存系统的状态，最安全的办法是将其作为原子操作进行序列化。如果我们序列化了某些东西，再去做其他的一些工作，那么将无法安全的保存系统状态。尽管 Class 类是 Serializable 的，但是却不能序列化静态类型的数据。加入想要序列化 static 必须手动实现。比如 Line 类中的哪样。serializeStaticState() 和 deserializeStaticState() 作为存储和读取过程的一部分被显式的调用。注意:必须维护写入和读取的顺序。

## 18.13 XML
序列化和反序列化只有Java才可以，XML和json一样支持多种不同语言。包括随 jdk 发布的 Javax.xml.\* 类库。这里我们使用 Elliotte Rusty Harold 的开源 XOM 类库，因为这个看起来更简单，同时也是最直观的用 Java 产生和修改 XML 的方式。

## 18.14 Preferences
Preferences 是一个键值集合，存储在一个节点层次结构中。节点层次结构可以创建更为复杂的结构，但通常是创建以你的类名命名的单一节点，然后将信息存储于其中。Preferences API 用于存储和读取用户的偏好以及程序配置项的设置。

## 18.15 总结
有装饰器模式后的JavaI/O才是完整的。
