# 第二十章 注解

注解的语法很简单除了`@`以外基本和java保持一致。内置三种注解为：
- @Override: 当前方法将覆盖超类中的方法。如果拼错编译器错误。
- @Deprecaed: 如果有人使用，则会发出错误提示。
- @SuppressWarnings: 关闭编译器警告信息。

## 20.1 基本语法
```java
public @interface Test {

}

public class Testble {
	public void execute() {
		System.out.println("Executing..");
	}

	@Test
	void testExecute(){
		execute();
	}
}
```

### 20.1.1 定义注解
@Test 的定义很像一个空的接口。定义注解时会需要一些元注解，如 @Target 和 @Retention 。@Target 用来定义你的注解将应用于什么地方。@Deprecated 用来定义应该用于哪一个级别可用，在源代码中、类文件中或者运行时。

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface UseCase {
	public int id();
	public String description() default "没有描述";
}
public class PasswordUtils {
	@UseCase(id =47,description = "password 哈哈哈防止破解")
	public boolean validatePassword(String password) {
		return (password.matches("\\w*\\d\\w*"));
	}
}
``` 
id 和 description 类似方法的定义。description 元素有一个 default 值，如果在注解某个方法时没有给出 description 的值，则就会使用这个默认值。

### 20.1.2 元注解
Java 目前只内置了三种标准注解，以及四种元注解。元注解就是注解的注解：

|元注解 |功能 |
|:----:|:----:|
|@Target	|表示注解可以用在什么地方。ElementType 的参数包括： </br>CONSTRUCTOR:构造器的声明 </br>FIELD:域声明 </br>LOCAL_VARIABLE:局部变量声明 </br>METHOD:方法声明 </br>PACKAGE:包声明 </br>PARAMETER:参数声明 </br>TYPE:类、接口或enum声明 |
|@Retention |表示需要在什么级别保存注解信息。可选的RententionPolicy参数：</br> </br>SOURCE:注解将被编译器丢失 </br>CLASS:注解在class文件中可用，但会被 vm 丢失 </br>RUNTIME:vm 在运行期也保留注解，因此可以通过反射机制读取注解的信息。|
|@Documented|将此注解包含在 javaDoc 中|
|@Inherited|允许子类继承父类中的注解|

## 20.2 编写注解处理器
可以通过反射读取注解内容。
```java
for (Method method : cl.getDeclaredMethods()) {
		UseCase uCase = method.getAnnotation(UseCase.class);
		if (uCase != null) {
			System.out.println("方法上的注解信息："+uCase.id()+"  "+uCase.description());
		}
	}
```

### 20.2.1 注解元素
注解元素可以使用类型有
- 所有的基本类型
- String
- Class
- enum
- Annotation
- 以上类型的数组

除此以外还可以是其他注解，也就是说注解可以嵌套。

### 20.2.2 默认值限制
元素不能有不确定的值，要么具有默认值要么有注解时提供的值。而且因为都是基本类型，所以默认值不能为`null``

### 20.2.3 生成外部文件
注解可以辅助某些`framework`所需要的额外信息。比如下面注释了表名，字段类型，默认值，约束。

```java
@Retention(RUNTIME)
@Target(TYPE)
public @interface DBtable {
	public String name() default "";
}

@Retention(RUNTIME)
@Target(FIELD)
public @interface Constraints {
	boolean primaryKey() default false;
	boolean allowNull() default true;
	boolean unique() default false;
}

@Retention(RUNTIME)
@Target(FIELD)
public @interface SqlString {
	int value() default 0;
	String name() default "";
	Constraints constraints() default @Constraints;
}

@DBtable(name = "Member")
public class Member {
	@SqlString(30)
	String firstname;

	@SqlString(value = 30,constraints = @Constraints(primaryKey=true))
	String handle;

	static int menberCount;
}

```
#### 变通之道
注释可以用多种不同方式定义。

### 20.2.4 注解不支持继承
无法用`extends`来继承`@interface`

### 20.2.5 实现处理器

可以实现一个类文件读取上面定义的表注释内容，自动生成对应的建表语句。

## 20.3 使用apt处理注释 
注解处理工具 apt，与 Javac 一样，被设计为操作 Java 的源文件，而不是编译后的类。默认情况下 apt 会在处理完源文件后编译他们。当注解处理器生成一个新的源文件时，改文件会在新一轮的注解处理中接受检查。该工具会一轮一轮的处理，直到不再有新的源文件产生。我们定义的每一个注解都需要自己的处理器，而 apt 工具可以很容易的将多个处理器组合在一起。这样我们就可以指定多个要处理的类。通过使用 AnnotationProcessorFactory，apt 能够为每一个它发现的注解生成一个正确的注解处理器。使用 apt 生成注解处理器时，我们无法利用 Java 的反射机制，因为我们操作的是源代码，而不是编译后的类。使用 mirror API 能够解决这个问题，他使得我们能够在未经编译的源代码中查看方法、对象以及类型。

这个示例可以自动提取类中的public方法，然后将其保存到另一个java文件中作为接口。

## 20.4 将观察者模式用于apt
本示例依然是SQL表生成器，不过此次使用了访问者模式创建工厂和注解处理器。

## 20.5 基于注解的单元测试
Java中最常用的单元测试工具为JUnit。使用注解进行单元测试的好处有，可以直接在类里面写测试代码，可以测试`private`方法。这个框架叫做`@Unit`, 使用时先要引用`net.mindview.atunit`然后用`@Unit`为方法和域打上标记。然后对编译后的类运行`@Unit`

```java
import net.mindview.atunit.*;
import net.mindview.unit.*;

public class AtUnitExample1{
    public String methodOne(){
        return " MethodOne";
    }
    public String methodTwo(){
        return 2;
    }
    @Test boolean methodOneTest(){
        return methodOne().equals("This is methodOne");
    }
    @Test boolean m2(){
        return methodTwo() == 2;
    }
}
```
除了直接在类中写测试方法更合适的手段是继承或者组合这个类。
```java
public class AtUnitxternalTest extends AtUnitExample1 {
    @Test boolean methodOneTest(){
        return methodOne().equals("This is methodOne");
    }
    @Test boolean m2(){
        return methodTwo() == 2;
    }
}
```
判断测试成功失败还可以使用断言`assert`

```java
@Test void assertAndReturn(){
    assert methodTwo() == 2;
    return methodOne().equal("abc");
}
```
对于每个单元测试`@Unit`都会使用默认构造器，创造一个新实例避免其他测试产生副作用。创造时使用默认构造器，如果要使用非默认构造器则需要创建一个`static`方法负责构造对象，加入`@TestObjectCreate`返回一个对象就能自动生成测试实例了。
```java
@TestObjectCreate static AtUnitExampl1 create() {
    return  new AtUnitExample1(10);
}
```
`@TestProperty`可以增加额外不属于单元测试的域，完成后的清理工作可以使用`@TestObjectCleanup`方法进行。

### 20.5.1 将@Unit用于泛型
让测试类继承自反省类的一个特定版本即可，不过这样可能会导致失去访问被测试类中private方法的能力。
### 20.5.2 不需要任何"套件"
`@Unit`不像`JUNit`需要套件。
### 20.5.3 实现@Unit
所有测试属性必须是`RUNTIME`然后用反射抽取注解中的信息，决定如何构造测试对象。
### 20.5.4 移除测试代码
`Javassist`开源工具类库可以辅助删除代码，本节的代码接受-r自动删除所有@Test注解。
## 20.6 总结

