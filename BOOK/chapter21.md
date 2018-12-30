# 第二十一章 并发

之前所说都是顺序编程，顺序编程虽然可以解决很多问题，但是有些问题如果可以并行执行则可以大大提升处理速度。学习并发编程就像进入一个全新的领域，有点类似于学习一门新的编程语言，或者是学习一整套新的语言概念。

## 21.1 并发的多面性
用并发解决的问题大体分为 "速度" 和 "设计可管理性" 两种。

### 21.1.1 更快的执行
并发通常为了提高单处理器上的程序性能。因为程序依赖外部环境会阻塞执行，在阻塞时程序可以切换执行其他任务。实现并发最简单的方式是使用进程，进程直接互相隔离不会彼此干涉，或者使用函数式编程可以让每个函数彼此互不干扰。Java采用传统方式，顺序语言之上使用线程，需要保证多个线程不同同时使用共享资源。

### 21.1.2 改进代码设计
仿真没有并发支持很难解决。如果仿真的任务非常大量需要使用主动放弃控制的协程来解决。

## 21.2 基本的线程机制
并发编程使得我们可以将程序划分为多个分离的、独立的任务。通过使用多线程机制，这些独立任务中的每一个都将由执行程序来驱动。一个线程就是进程中的一个单一的顺序控制流，因此，单个进程可以拥有多个并发执行的任务，但是你的程序使得每个任务都好像有其自己的 CPU 一样。其底层机制是切分 CPU 时间，但我们通常不需要考虑他。

### 21.2.1 定义任务
要想定义任务，只需要实现 Runnable 接口并编写 run() 方法，使得该任务可以执行你的命令。`Thread.yield`可以告诉调度器已经完成执行任务，可以将控制权返回。
```java
public class LiftOff implements Runnable{
	  protected int countDown = 10; // Default
	  private static int taskCount = 0;
	  private final int id = taskCount++;
	  public LiftOff(int countDown) {
	    this.countDown = countDown;
	  }
	  public String status() {
	    return "#" + id + "(" +
	      (countDown > 0 ? countDown : "Liftoff!") + "), ";
	  }
	  public void run() {
	    while(countDown-- > 0) {
	      System.out.print(status());
	      Thread.yield();
	    }
	  }

}

```

### 21.2.2 Thread类
将 Runnable 对象转变为一个工作任务的方式是把它提交给一个 Thread 构造器。然后使用`start`开始执行，之后新线程就会自动调用`run`方法。
```java
Thread thread = new Thread(new LiftOff());
thread.start();
```
因为每个线程都注册了一个自己的引用，所以和普通的Java对象不同，垃圾回收机制无法清理它们。

### 21.2.3 使用Executor
`java.util.concurrent`包中的执行器 (Executor) 将为你管理 Thread 对象，简化了并发编程。Executor 允许你管理异步任务的执行，而无需显示的管理线程和生命周期。我们可以使用 Executor 来替代在上个示例中显示的创建 Thread　对象。
```java
ExecutorService executorService = Executors.newCachedThreadPool();
for (int i = 0; i < 3; i++) {
	executorService.execute(new LiftOff());
}
executorService.shutdown();
```
`shutdown`后不会有新的任务提交给`Executor`，而当前线程将继续运行之前提交的任务。整个程序在所有任务完成后退出。`FixedThreadPool`可以构建固定大小线程池，节省新建线程的开销。`CachedThreadPool`会创建与任务数相同的线程数。`SingleThreadExecutor`等同于大小为1的`FixedThreadPool`。线程的创建管理销毁都由`Executor`处理。

### 21.2.4 从任务中返回返回值
如果你希望在任务执行完成时能够返回值，那么可以实现 Callable 接口。这是一个泛型接口，必须使用`ExecutorService.submit() `调用它。
```java
ExecutorService executorService = Executors.newCachedThreadPool();
ArrayList<Future<String>> result = new ArrayList<>();
for (int i = 0; i < 3; i++) {
	result.add(executorService.submit(new TaskWithResult(i)));
}

for (Future<String> future : result) {
	System.out.println(future.get());
}
```
submit 方法会产生 Future 对象，它用 Callable 返回结果的特定类型进行了参数化。可以使用 isDone() 方法查询 Future 对象是否完成。当任务完成时可以调用 get() 方法获取结果。

### 21.2.5 休眠
调用 sleep()，将使任务终止执行给定的时间。作为 TimeUnit 类的一部分，这个方法允许你指定 sleep 延迟的时间单元。
```java
TimeUnit.MILLISECONDS.sleep(100);
```

### 21.2.6 优先级
可以使用 getPriority() 来读取现有线程的优先级，可以通过 setPriority() 来修改它。虽然JVM提供10个优先级不过实际上使用只需要最大最小和正常即可。因为不同操作系统优先级无法和JVM一一对应。
```java
for(int i = 0; i < 5; i++)
    exec.execute(new SimplePriorities(Thread.MIN_PRIORITY));
exec.execute(new SimplePriorities(Thread.MAX_PRIORITY));

```

### 21.2.7 让步
当调用 yield() 时，是在建议具有相同优先级的其他线程可以运行。不过这无法被系统保证。

### 21.2.8 后台线程
后台线程用`setDaemon`实现，后台线程中创建的线程也是后台线程。所有非后台线程退出后，所有后台线程也会退出。
```java
Thread daemon = new Thread(new SimpleDaemons()); //比如在调用之前设置为后台线程才会生效
daemon.setDaemon(true); // Must call before start()
daemon.start();
```
> 后台线程在系统终止退出时不会执行`finally`语句。

### 21.2.9 编码的辩题
除了实现`callable`还可以直接继承`Thread`类。或者实现自管理的`Runnable`类。不过后者可能导致在构造结束前执行，所以还是使用`Executor`更为稳妥。

### 21.2.10 术语
要执行的内容是任务，线程只是驱动机制。
### 21.2.11 加入一个线程
一个线程可以在其他线程之上调用 join() 方法，其效果是等待一段时间直到第二个线程结束才继续执行。也可以在调用 join() 时带上一个超时参数，这样如果目标线程在这段时期没有完成结束，join() 方法总能返回。对 join() 方法的调用可以被中断，做法是在调用线程上调用 interrupt() 方法。
```java
public Joiner(String name, Sleeper sleeper) {
  super(name);
  this.sleeper = sleeper;
  start();
}
public void run() {
 try {
    sleeper.join();
  } catch(InterruptedException e) {
    Print.print("Interrupted");
  }
}
```

### 21.2.12 创建有响应的用户界面

### 21.2.13 线程组
一次失败的尝试，

> 继续错误的代价由别人来承担，而承认错误的代价由自己承担。  -- 一位诺贝尔经济学奖得主

### 21.2.14 捕获异常
普通方法无法捕获其他线程的异常。不过可以用`Executor`来解决。

1. 先定义一个异常处理器。
```java
public class MyUncaughtExceptionHandler implements UncaughtExceptionHandler{

	@Override
	public void uncaughtException(Thread t, Throwable e) {
		// TODO Auto-generated method stub
		System.out.println("自定义的异常捕获"+e);
	}
}

```

2. 然后定义一个异常处理的线程工厂

```java
public class HandlerThreadFactory implements ThreadFactory{

	@Override
	public Thread newThread(Runnable r) {
		Thread thread = new Thread(r);
		thread.setUncaughtExceptionHandler(new MyUncaughtExceptionHandler());
		return thread;
	}

}

```

3. 最后用执行器执行线程

```java
public class CaptureUncaughtException {

	public static void main(String[] args) {
		//添加进到构造方法
		ExecutorService executorService = Executors.newCachedThreadPool(new HandlerThreadFactory());
		executorService.execute(new ExceptionThread());
	}

}
```

## 21.3 共享受限资源
单个线程每次只能做一件事情。但是多线程会在任意位置退出，同时访问一个资源。

## 21.3.1 不正确的访问资源

```java
public int next(){
    ++ currentEvenNumber; //  可能前一个线程在这里退出。
    ++ currentEvenNumber;
    return currentEvenNumber;
}
```
一个失败的例子，多个线程共享`EvenNumber`如果有线程在第一步退出，第二个线程最后的结果会是奇数。

### 21.3.2 解决共享资源竞争
基本上所有的并发模式在解决线程冲突问题的时候，都是采用序列化访问共享资源的方案。这意味着在给定时刻只允许一个任务访问共享资源。通常这种是通过在代码前面加上一句锁语句来实现的，这就使得在一段时间内只有一个任务可以运行这段代码。因为锁语句产生一种相互排斥的效果，这种机制称为互斥量。Java 以提供关键字`synchronized`的形式，为防止资源冲突提供了内在支持。当任务要执行被`synchronized`关键字保护的代码片段的时候，它将检查锁是否可用，然后获取锁，执行代码，释放锁。共享资源一般是以对象像是存在于内存片段，可以是文件、输入输出端口。要控制对共享资源的访问，得先把它包装进一个对象。然后把所有要访问这个资源的方法标记为`synchronized`。对象的所有`synchronized`资源都共享同一把锁。
```java
synchronized void f(){};
synchronized void g(){};
```

> 使用并发时将对象设置为 private 是非常重要的，否则，synchronized 关键字就不能防止其他的任务直接访问域，这样就会产生冲突。 针对每个类也有一个锁，所以 synchronized static 方法可以在类的范围内防止对 static 数据的并发访问。

#### 同步控制 EvenGenerator
上述例子使用`synchronized`后就不会产生奇数了。
```java
public synchronized int next() {
	++currentEvenValue;
	Thread.yield(); // 提高产生奇数概率
	++currentEvenValue;

	return currentEvenValue;
}
```

#### 使用显示的Lock对象
在 java.util.concurrent.locks 中的显示的互斥机制。Lock 对象必须被显示地创建、锁定和释放。因此，它与内建的锁形式相比，更麻烦。但是对于解决某些类型的问题时更加的灵活。

```java
private Lock lock = new ReentrantLock();

public int next() {
	//锁定
	lock.lock();
	try {
		++currentEvenValue;
		Thread.yield();
		++currentEvenValue;

		return currentEvenValue;
	}finally {
		//计算完毕后释放锁
		lock.unlock();
	}
}


```

### 21.3.3 原子性与易变性
> 除非可以编写用于现代微处理器的高性能JVM，否则不要考虑是否可以避免同步而使用原子性替代。

原子性可以应用于除了 long 和 double 之外的所有基本类型之上的 “简单操作”。但是 jvm 会把 64 位的 long 和 double 操作当做两个分离的 32 位的操作来执行，这就产生了一个读取和写入操作之间产生上下文切换，从而导致了不同的任务产生不正确结果的可能性。

在多处理器上可视性问题比原子性更多，可视性保证在一处修改，其他地方都能强制在其他地方刷新这个修改。我们的第一选择应该是 synchronized 关键字，这是最安全的方式。
#### 什么是原子性操作？
在cpp中一些自增操作可能是原子操作，而Java中它们一般不是原子操作。对于基本类型的读取和赋值操作被认为是安全的原子性操作。但是当对象处于不稳定状态时，仍旧很有可能使用原子性操作得到访问。最明智的做法是遵循同步的规则。

### 21.3.4 原子类
Java SE5 中引入了诸如 AtomicInteger、AtomicLong、AtomicReference 等等特殊的原子性变量类，他们提供下面形式的原子性条件更新操作：

```java
boolean compareAndSet(expectedValue,updateValue);
```

这些类被调整为可以使用在现代处理器上，并且是机器级别的原子性。常规来说很少使用他们，但是对于性能调优来说，他们就大有用武之地了。

### 21.3.5 临界区
如果只是希望防止多个线程访问方法内部分代码而不是整个方法，可以使用临界区的方法。使用方法为:
```java
synchronized(syncObject){
    // 临界区
}
```
这个方法可以显著提高性能。
```java
synchronized(this) {
  p.incrementX();
  p.incrementY();
  temp = getPair();
}
```

### 21.3.6 在其他对象上同步
synchronized 块必须给定一个在其上同步的对象，并且合理的方式是，使用其方法正在被调用的当前对象：synchronized(this)，在这种方式中如果获得了 synchronized 块上的锁，那么该对象其他的 synchronized 方法和临界区就不能被调用了。在不同物品上同步的锁不是同一把锁。未标明的锁就是对象本身。
```java
class DualSynch {
  private Object syncObject = new Object();
  public synchronized void f() {
    for(int i = 0; i < 5; i++) {
      print("f()");
      Thread.yield();
    }
  }
  public void g() {
    synchronized(syncObject) {
      for(int i = 0; i < 5; i++) {
        print("g()");
        Thread.yield();
      }
    }
  }
}
```
其中`f`和`g`就不用同一把锁。

### 21.3.7 线程本地存储
防止任务在共享资源上产生冲突的第二种方式是根除对变量内存的共享。线程本地存储是一种自动化机制，可以使用相同变量的每个不同的线程创建不同的存储。它们使得你可以将状态和线程关联起来。创建和管理线程本地存储可以由`java.lang.ThreadLocal`类来实现： ThreadLocal 对象通常当做静态存储域。创建 ThreadLocal 方法时只能通过 get() 和 set() 方法来访问内容，其中，get() 方法返回与对象相关联的副本，而 set() 将会将参数插入到为其线程存储的对象中，并返回存储中原有对象。

## 21.4 终结任务

### 21.4.1 装饰性花园
一个花园多个门进入的例子，移除`synchronized`后每个门的人数和总人数可能无法对齐。

### 21.4.2 在阻塞时终结
#### 线程状态

一个线程可以处以一下四种状态：

- 新建 (new)：当线程被创建时，他只会短暂的处以这种状态。此时已经分配了必要的资源，并执行初始化。此刻线程已经有资格获得 cpu 时间了，之后调度器会把这个线程转变为可运行状态或阻塞状态。
- 就绪 (Runnable)：在这种状态下只要调度器把时间片分配给线程，线程就可以运行。在任意时刻线程可以运行也可以不运行，取决于调度去是否分配给线程时间片。
- 阻塞 (Blocked)：线程能够运行，但有个条件组织它运行。当线程处于阻塞状态时，调度器将忽略线程不会分配给他任何 cpu 时间。
- 死亡 (Dead)：处于死亡和终止状态的线程将不再可被调度，并且再也不会得到 cpu 时间，它的任务已经结束，或不再是可运行的。任务死亡的方式通常是从 run() 方法返回，但是任务的线程还可以被中断。

sleep时进入阻塞状态，还有等待网络返回结果等。

#### 进入阻塞状态
进入阻塞的原因有：
- 通过调用 sleep() 使任务进入休眠状态，在这种情况下任务在指定的时间内不会运行。
- 你通过调用 wait() 使线程挂起。直到线程得到了 notif() 或 notifall() 消息，线程才会进入就绪状态。
- 任务在等待某个输入或输出完成。
- 任务试图在某个对象上调用其同步控制方法，但是对象锁不可用，因为另一个任务已经获取了锁。


### 21.4.3 中断
Thread 类包含 interrupt() 方法，可以终止被阻塞的任务，这个方法将设置线程的中断状态。如果一个线程被阻塞或者试图执行一个阻塞操作，那么设置这个线程的阻塞状态将抛出 InterruptedException。当抛出该异常或者改任务调用 Thread.interrupt() 时，中断状态将被复位。Thread.interrupt() 提供了离开 run() 循环而不抛出异常的办法。
```java
 public void run() {
    try {
      print("Waiting for read():");
      in.read();
    } catch(IOException e) {
      if(Thread.currentThread().isInterrupted()) {
        print("Interrupted from blocked I/O");
      } else {
        throw new RuntimeException(e);
      }
    }
    print("Exiting IOBlocked.run()");
  }
```
新类库尽量避免我们直接操作 Thread 对象，而是尽量使用 Executor 来执行所有操作。如果你在 Executor 上调用 shutdownNow() ，那么将会发送一个 interrupt() 给由它启动的所有线程。然而有时我们需要的是中断其中的某一个任务。此时如果我们使用 Executor 调用 submit() 来启动任务，就可以持有该任务的上下文对象。submit() 将返回一个泛型的 Future<?>,其中有一个未修饰的参数，持有这个 Future 的关键是可以在其上调用 cancel()。那么就可以拥有在改线程上调用 interrupt() 以停止这个线程的权限。
```java
Future<?> f = exec.submit(r);
TimeUnit.MILLISECONDS.sleep(100);
print("Interrupting " + r.getClass().getName());
f.cancel(true); // Interrupts if running
```

#### 被互斥所阻塞

### 21.4.4 检查中断
当你在线程上调用 interrupt() 时，中断发生的唯一时刻是在任务要进入到阻塞操作中，或者已经在阻塞操作内部时。如果你的 run() 方法没有产生任何阻塞调用的情况下，调用 interrupt() 将无法停止某个任务。可以调用 interrupted() 来检查中断状态，这不仅可以告诉你 interrupt() 是否可以被调用过，而且还可以清楚中断状态。清楚状态可以确保并发结构不会就某个任务被中断这个问题通知你两次，你可以经由单一的 InterruptedException 来得到通知。如果想再次检查了解是否被中断，则可以调用 Thread.interrupted() 时将结果保存起来。
```java
try {
	TimeUnit.SECONDS.sleep(1);
} catch (InterruptedException e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
}finally {
	nCleanup.cleanup();
}
```






