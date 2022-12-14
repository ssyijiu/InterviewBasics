## Java 基础：反射

### 一. 什么是反射

反射就是在程序运行过程中通过硬盘上的一个字节码文件对象（.class文件），去获取并使用这个类的属性和方法（常用的有构造方法，成员变量，成员方法），这种动态获取的信息以及动态调用对象 方法的功能称为 java 语言的反射机制。

### 二. 如何获取字节码文件对象

Java中有一个专门的类用来描述字节码文件对象，那就是 Class，反射就是依靠这个类来完成的。
获取一个类的Class对象有三种方法

1. 通过 Object 中的 getClass() 

   ```java
   Person p = new Person();  
   Class c = p.getClass();
   ```

2. 类的静态属性 .class 

   ```java
   Class c = Person.class
   ```

3. Class 类中的静态方法：Class.forName(className)

      ```java
      String className = "reflect.Person";   
      Class c = Class.forName(className); 
      // 注意这里要写 包名+类名来表示这个类，否则会报 ClassNotFoundException
      ```

#### 小提示：

* isPrimitive() 可以用来判断一个指定的 Class 对象是否表示一个基本类型 byte, short, int, long, boolean, char, float, double, void 的 Class 对象都是基本类型
* Integer.class 和 int.class 不是一个 Class 实例
* Integer.TYPE 表示基本类型 int 的 Class 实例，也就是 int.class

代码：

```java
class PrimitiveTest {

    public static void main(String[] args) {
        // Strings是一个类, 它的字节码文件不是基本类型的字节码
        System.out.println(String.class.isPrimitive());  // false

        // 四类八种数据类型和 void 的字节码文件都是基本类型的字节码
        System.out.println(int.class.isPrimitive());   // true
        System.out.println(void.class.isPrimitive());  // true

        // Integer.class 和 int.class 不是一个字节码
        System.out.println(Integer.class == int.class); // false
        System.out.println(Integer.class);  // class java.lang.Integer
        System.out.println(int.class);      // int  

        // Integer.TYPE 就是 int.class
        System.out.println(Integer.TYPE == int.class);  // true
    }
}
```

##三. 通过反射获取构造方法并使用

在获取构造方法之前，我们要先介绍一个类，Constructor ，API 中是这样解释的：Constructor 提供关于类的单个构造方法的信息以及对它的访问权限，简单点说，我们通过 Java 把反射所获取的构造方法也用一个类来描述，就是Constructor。

Class 类中给我提供了以下获取构造方法的函数：

```java
Constructor getConstructor (Class ... parameterTypes) // 获取指定的共有构造方法
Constructor[] getConstructors() // 获取所有的共有构造方法   
Constructor getDeclaredConstructor (Class ... parameterTypes) // 获取指定的构造方法，包括私有
Constructor[] getDeclaredConstructor s() // 获取所有的构造方法，包括私有
```

下面的例子中我们要获取 Person 这个类的构造方法并用它创建对象，看 Person 类的代码：

```java
class Person {
     String name;
     int age;
    public Person(){
          System.out.println("无参 public 构造方法");
     }
 
     private Person(String name) {
          System.out.println("有参 private 构造方法");
          this.name = name;
     }
 
     public Person(String name, int age) {
          System.out.println("有参 public 构造方法");
          this.name = name;
          this.age = age;
     }
    public String toString() {
          return "Person [age=" + age + ", name=" + name + "]";
    }
}
```

在上面这个类中，有三种不同类型的构造方法，一起来看看如何获取它们。

1.获取无参public构造方法

```java
public static void main(String[] args) throws Exception {
    // 注意这里要写 包名+类名来表示这个类，否则会报ClassNotFoundException
    Class c = Class.forName("reflect.Person"); // 得到要获取构造方法类的Class对象，
    Constructor con = c.getConstructor();	    // 获取无参构造方法
    // con.newInstance() 是用以获取的构造方法创建相应对象的方法，这个函数返回 Object，强转一下
    Person p = (Person) con.newInstance();	  
}
// 运行结果：
// 无参 public 构造方法
```
2.获取有参public构造方法

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");

    // 此时 getConstructor 方法的参数是你要获取的构造方法参数的 .class 对象
    // 注意它们的顺序要和构造方法参数顺序相一致，否则会出现 NoSuchMethodException（找不到这个方法）
    Constructor con = c.getConstructor(String.class, int.class);
    Person p = (Person) con.newInstance("李小龙", 27);
    System.out.println(p);
}
// 运行结果
// 有参 public 构造方法
// Person [age=27, name=李小龙]
```
3.获取有参private构造方法

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");

    // 获取私有构造方法我们要使用 getDeclaredConstructor 
    // 使用 getConstructor 会出现 NoSuchMethodException
    Constructor con = c.getDeclaredConstructor (String.class);

    // 这句代码的意思是，取消反射的对象在使用时的权限检查，也就是常说的暴力访问
    // 注释掉它会出现 IllegalAccessException 非法访问异常，毕竟是私有方法，不是你什么都不做就可以访问的
    con.setAccessible(true);
    Person p = (Person) con.newInstance("李小龙");
    System.out.println(p);
}
// 运行结果
// 有参 private 构造方法
// Person [age=0, name=李小龙]
```

4.获取构造方法数组

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");

    // 获取所有的共有构造方法
    Constructor[] cons = c.getConstructors();
    // 获取所有的构造方法，包括私有
    Constructor[] deCons = c.getDeclaredConstructors();

    // 把数组转换为字符串打印
    System.out.println(Arrays.toString(cons));
    System.out.println(Arrays.toString(deCons));
}
```
运行结果：

    [public reflect.Person(), public reflect.Person(java.lang.String,int)]
    [public reflect.Person(), private reflect.Person(java.lang.String),
     public reflect.Person(java.lang.String,int)]

##四. 通过反射获取成员变量并使用

在获取成员变量之前，我们依旧要先介绍一个类，Field，字段（字段就可以直接理解为成员变量），API 中是这样解释的：Field 提供有关类或接口的单个字段的信息，以及对它的动态访问权限，简单点说，我们通过 Java 把反射所获取的字段（成员变量）用一个类来描述，就是Field。

Class类中给我提供了以下获取构造方法的函数：

```java
Field getField (String name) // 获取指定的共有成员变量
Field [] geFields() // 获取所有的共有成员变量  
Field getDeclaredField(String name) // 获取指定的成员变量，包括私有
Field [] getDeclaredFields() // 获取所有的成员变量，包括私有


// Field中常用的方法介绍：  
public Object get(Object obj) // 返回 obj 对象上该 Field 字段的值
public int getInt(Object obj) // 和上面的方法用法差不多，只不过返回值是 int
public void set(Object obj,Object value) // 将 obj 对象上的 Filed 的值设置为value
public void setInt(Object obj,int value) // 将 obj 对象上 Field 的值设置为一个 int 值
public Class<?> getType() // 返回一个 Class 对象，它标识了此 Field 对象所表示字段的声明类型
```

Person：

```java
class Person {
	private String name = "李小龙";
       // 私有成员变量
	public int age = 27;
       // 共有成员变量
        public address;
	public String toString() {
		return "Person [age=" + age + ", name=" + name + "]";
	}
}
```
1.通过反射获取共有成员变量

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    Field field = c.getField("age");
    // 定义一个字段用来接收要获取的成员变量 age
    // new 一个 Person对象，因为无论是获得还是修改成员变量，必须有一个实例
    // 没有实例你获得的是谁的成员变量呢，上局代码只是定义的字段，并没有产生实例
    Person p = new Person();
    System.out.println(p);
    // 获取 p 中的age字段，因为 age 是 int 类型，所以这里我直接使用 getInt();
    int age = field.getInt(p);
    System.out.println(age);
    // 修改一下 p中 age字段的值
    field.set(p,23);
    System.out.println(p);
}
```
运行结果：

    Person [age=27, name=李小龙]
    27
    Person [age=23, name=李小龙]

2.通过反射获取私有成员变量

```java
public static void main(String[] args) throws Exception {
	Class c = Class.forName("reflect.Person");
	// 获取私有字段必须使用getDeclaredField
    Field field = c.getDeclaredField("name");
	Person p = new Person();
	System.out.println(p);
    // 取消访问权限检查，暴力访问，注释掉它会出现IllegalAccessException 非法访问异常
	field.setAccessible(true);
    // Field中的 get() 方法返回的是 Object 类型，强转一下
	String name = (Object)field.get(p);
	System.out.println(name);
	field.set(p,"成龙");
	System.out.println(p);
}
```
运行结果：

    Person [age=27, name=李小龙]
    李小龙
    Person [age=27, name=成龙]

3.通过反射获取成员变量数组

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    // 获取共有成员变量数组
    Field[] fields = c.getFields();
    // 获取全部成员变量数组，包括私有
    Field[] fieldDecls = c.getDeclaredFields();
    // 把数组装换为字符串打印
    System.out.println(Arrays.toString(fields));
    System.out.println(Arrays.toString(fieldDecls));
}
```
运行结果：

    [public int reflect.Person.age]
    [private java.lang.String reflect.Person.name, public int reflect.Person.age]

##五. 通过反射获取成员方法并使用

在获取成员方法之前，我们依旧要先介绍一个类，Method，API 中是这样解释的：Method 提供关于类或接口上单独某个方法（以及如何访问该方法）的信息，简单点说，我们通过 Java 把反射所获取的成员方法也用一个类来描述，就是Method，这个成员方法可以是一般成员方法、静态方法、抽象方法、main 方法。

Class 类中给我提供了以下获取构造方法的函数：

```java
Method getMethod  (String name, Class... parameterTypes) // 获取指定的共有成员方法
Method [] geMethods() // 获取所有的共有成员变量，注意：这个方法有些特殊，它会把从父类继承的共有方法也获取过来  
Method getDeclaredMethod (String name) // 获取指定的成员方法，包括私有
Method [] getDeclaredMethods() // 获取所有的成员方法，包括私有，不会获取到从父类继承方法

public Object invoke(Object obj,Object ... args)  // 执行获取的方法
public Class<?> getReturnType()  // 返回一个 Class 对象，该对象描述了此 Method 对象所表示的方法的正式返回类型
```

Person:

```java
class Person {
	public void publicShow() {
		System.out.println("共有无参方法");
	}
    
	private void privateShow(String str, int i) {
		System.out.println("有参私有方法\t" + str + "\t" + i);
	}
    
	public static void staticShow() {
		System.out.println("静态方法");
        System.out.println(str);
	}
    
    public String returnShow(String str) {
		return str;
	}
    
	public static void main(String[] args) {
		System.out.println("main方法");
        System.out.println(Arrays.toString(args));
	}
}
```
1.获取无参共有方法

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    Person p = new Person();
    // 通过方法名来获取到方法， 并创建一个 Method 对象接收方法，这样m就和publicShow关联起来了
    Method m = c.getMethod("publicShow");
    // 执行方法也必有一个实例，以前我们调用方法都是 实例.方法() 例如：p.publicShow()
    // 而反射正好反过来， 方法对象.invoke(实例);
    m.invoke(p);
}
// 运行结果：
// 无参共有方法
```
2.获取有参私有方法

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    Person p = new Person();
    // 此时getDeclaredMethod方法的参数是你要获取的成员方法的 方法名和这个方法的参数的字节码对象
    // 注意字节码对象的顺序要和成员方法参数顺序相一致，否则会出现NoSuchMethodException（找不到这个方法）
    Method m = c.getDeclaredMethod ("privateShow", String.class, int.class);
    m.setAccessible(true);
    // 此时invoke的第一个参数是一个该类的实例，其他参数是调用成员方法需要传入的参数
    m.invoke(p," Hello Java",99);
}
// 运行结果
// 有参私有方法	 Hello Java	99
```
3.获取静态方法

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    Method method = c.getMethod("staticShow",String.class);
    // 我们说invoke的第一个参数是一个要反射类的实例，那是因为对于一般的方法，必须先有实例才能调用(实例.方法)
    // 而静态方法是属于一个类的在这样类被实例化之前就加载到内存了，所以调用静态方法不不需要实例，直接写null就可以
    method.invoke(null,"好好学习 天天向上");
}
// 运行结果：
// 静态方法
// 好好学习 天天向上
```
对于抽象方法的调用没什么特殊的，因为抽象类无法实例化，记得调用前用子类把抽象类实例化传入 invoke 即可，在此就不在赘述了。

4.获取带返回值的方法

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    Person p = new Person();
    Method method = c.getMethod("returnShow",String.class);
    // 无论原来的方法返回的值是什么类型，method.invoke 都返回 Object，强转一下
    String str = (String) method.invoke(p, "hello");
    System.out.println(str);
}
// 运行结果
// hello
```
5.获取成员方法数组

```java
public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    // 获取所有的公共方法，包括从父类继承的，不包括私有
    Method[] methods = c.getMethods();

    System.out.println("getMethods:");
    System.out.println(Arrays.toString(methods));
    // 获取所有的方法，不包括从父类继承的，包括私有
    Method[] methodDecls = c.getDeclaredMethods();

    System.out.println("getDeclaredMethods:");
    System.out.println(Arrays.toString(methodDecls));
}

```
运行结果：

```java
getMethods:
[public static void reflect.Person.main(java.lang.String[]), public void reflect.Person.publicShow(), pu    blic static void reflect.Person.staticShow(), public final void java.lang.Object.wait() throws java.lang    .InterruptedException, public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedExc    eption, public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException, publ    ic native int java.lang.Object.hashCode(), public final native java.lang.Class java.lang.Object.getClass    (), public boolean java.lang.Object.equals(java.lang.Object), public java.lang.String java.lang.Object.t    oString(), public final native void java.lang.Object.notify(), public final native void java.lang.Object    .notifyAll()]
getDeclaredMethods:
[public static void reflect.Person.main(java.lang.String[]), public void reflect.Person.publicShow(), pr    ivate void reflect.Person.privateShow(java.lang.String,int), public static void reflect.Person.staticSho    w()]

// getMethods 获得了如此多的方法，但大部分是从Object类中继承来的
```

6.获取 mian 方法，这里有点特殊

```java

public static void main(String[] args) throws Exception {
    Class c = Class.forName("reflect.Person");
    Method methodMian = c.getMethod("main", String[].class);
    String[] arg = {"hellow","world","java"};
    // main 方法是静态方法，不单独属于某一个实例，直接写 null 就可了，arg 是我们传入的数组参数
    methodMian.invoke(null，arg);
}

```
执行结果：

![](http://i.imgur.com/zQcTaD0.png)

NoSuchMethodException，意思找不到我们要调用的 main 方法，可是我们的步骤就以前的经验来看并没有什么错误，难道 main 方法有什么特殊的吗? 其实这是 Java 版本更新产生的一个问题。

我们知道启动 Java 程序的 main 方法的参数是一个字符串数组，按照 jdk1.5的语法，整个数组是一个参数， 而按照 jdk1.4 的语法，数组中的每一个元素对应一个参数，当把一个字符串数组作为参数传递给invoke方法时，javac会按照jdk1.4的语法进行处理，将数组打散把每个元素当做一个参数传入，因为 jdk1.5 肯定要兼容 jdk1.4 的语法，也就是把数组打散成若干个单独的参数，所以也就会出现上面的异常了。

我们知道了原因，那么也就好解决了，这里提供两种解决方法。

<1>. 既然字符串数组会拆包成一个个的对象参数，那么我们就在这个字符串的外面再套上一层外衣当拆包的时候只是拆掉外面的那层，里面的字符串数组就可以作为一个单独的参数进行传递了。

<2>. 我们把数组强转为 Object类，这样 jdk1.4 就不会把我们传入的参数当做数组给打散了。

```java
public class PersonDemo {
	public static void main(String[] args) throws Exception {
		Class c = Class.forName("reflect.Person");
		Method methodMian = c.getMethod("main",String[].class);
		String[] arg = {"hellow", "world","java"};
	    // 方法1. 在原来数组的基础上包一层数组外衣
		methodMian.invoke(null, new Object[]{arg});
	    // 方法2. 强转为Object
		methodMian.invoke(null, (Object)arg);
	}
}
```
运行结果：

![](http://i.imgur.com/YR0sDvb.png)

##六. 反射的使用案例
到此我们反射的基本内容就讲完了，说了这么多反射，那么反射在开发中究竟是用来做什么的呢？

#### 1、通过反射运行配置文件的内容

##### A. 学生老师工人问题

问题的开始，客户有一个学生类，让我给运行起来。

Student:

```java
public class Student {
    public void show() {
        System.out.println("我是学生");
    }
}
```
运行：

```java
public class ReflectDemo {
    public static void main(String[] args) throws Exception{
        Student s = new Student();
        s.show();
    }
}
// 运行结果
// 我是学生
```
后来，客户的寻求改了，他又给我一个老师类，和我说，别运行学生类了，我们现在需要运行老师类。

```java
public class Teacher {
    public void show() {
        System.out.println("我是老师");
    }
}
```

修改下代码，把学生注释掉，添加上老师就OK了。

```java
public static void main(String[] args) throws Exception{
    //	 Student s = new Student();
    //	 s.show();

    Teacher t = new Teacher();
    t.show();
}
// 运行结果
// 我是老师
```
这天，客户又和我说，我们现在要运行工人类。

```java
public class Worker {
    public void show() {
        System.out.println("我是工人");
    }
}
```

昨天老师，今天就工人，明天没准就是医生，后天还可能是警察呢，这职业这么多，我天天也别做其他事了，光给他改代码吧。不行有没有一种方法，他给我什么类，我程序运行时加载进去就行了，不用每次都修改代码。

创建一个配置文件

class.properties：        

```java
className = reflecttest2.Teacher
methodName = show
```

现在我要做的就是让程序运行时加载class.properties中的写好的类，并调用它的方法就好了。

```java
public static void main(String[] args) throws Exception{

    // 创建一个 Properties，用来加载配置文件中的内容
    Properties p = new Properties();
    // 创建一个 FileReader 关联到配置文件的路径
    FileReader fr = new FileReader("src/reflecttest2/class.properties");

    // 加载 fr，这样配置文件中的内容就保存到了 Properties 中
    p.load(fr);
    // 关闭流，释放资源
    fr.close();

    // Properties 实际上是一个 Map 集合，获取键 className 对应的值，这里是 reflecttest2.Teacher
    String className = p.getProperty("className");
    // 获取键 className 对应的值
    String methodName = p.getProperty("methodName");

    // 在执行过程中通过反射动态加载要执行的类，首先获取该类的 Class 对象
    Class c = Class.forName(className);

    // 创建一个反射得到的类实例
    Object obj = c.newInstance();

    // 获取成员方法
    Method m = c.getMethod(methodName);
    // 调用方法
    m.invoke(obj);
}
```
运行结果：

    我是工人         

##### B. 组装电脑问题

我想自己组装一台电脑，首先买了一个主板

MainBoard ：        

```java
public class MainBoard {
    public void run() {
        System.out.println("the MainBoard is running");
    }
}
```
好，先写一个类，测试一下主板能不能正常运行

```java
public class ReflectDemo {
    public static void main(String[] args) {
        MainBoard mb = new MainBoard();
        mb.run();
    }
}
```
主板可以用了，可是我还想听听声音，OK再买个声卡，装到主板上

SoundCard：

```java
public class SoundCard {
    public void open(){
        System.out.println("SoundCard open");
    }

    public void close(){
        System.out.println("SoundCard close");
    }
}
```

MainBoard:


```java
public class MainBoard {
    public void run() {
        System.out.println("the MainBoard is running");
    }

    public void useSound(SoundCard sc) {
        sc.open();
        sc.close();
    }    
}
```
测试一下：

```java
public class ReflectDemo {
    public static void main(String[] args) {
        MainBoard mb = new MainBoard();
        mb.run();
        mb.useSound(new SoundCard());
    }
}
```
运行结果：

```java
the MainBoard is running
SoundCard open
SoundCard close
```

虽然声卡装好了，这样每一次添加一个新的设备, 都要在 MainBoard 中实现一些方法，太过麻烦而且我们不知道后期会出现什么设备 ，现在定义一个接口，在接口中定义规则，只要后期添加的设备符合我们的规则就可以使用

MainBoard：

```java
public class MainBoard {
    public void run() {
        System.out.println("the MainBoard is running");
    }

    public void usePCI(PCI p) {
        if(p != null) {
            p.open();
            p.close();
        }
    }
}
```
我们让声卡实现这个接口：

```java
public class SoundCard implements PCI {
    public void open(){
        System.out.println("SoundCard open");
    }

    public void close(){
        System.out.println("SoundCard close");
    }
}
```

测试一下：

```java
public static void main(String[] args) {
    MainBoard mb = new MainBoard();
    mb.run();
    mb.usePCI(new SoundCard());
}
```
运行结果：

    the MainBoard is running
    SoundCard open
    SoundCard close

现在我们想运行什么设备，只要让它实现我们的接口就可以了，但现在比如我又添加了一个显卡，运行的时候还要在测试代码中添加一行：mb.usePCI(new ShowCard());

还是有点麻烦，有没有一种方法，让程序在运行的时候，获取我要运行的设备。

下面我们就用配置文件完成这个需求

配置文件：pci.properties

```java
pci1=reflecttest.SoundCard
pci2=reflecttest.ShowCard
```

测试文件：

```java
public class ReflectTest {
    public static void main(String[] args) {
        MainBoard mb = new MainBoard();
        mb.run();

        // 创建一个文件关联配置文件
        File configure = new File("src/reflecttest/pci.properties");
        FileReader fr = null;

        // 创建一个Properties 将读取到的配置文件内容保存到里面
        Properties pcipro = new Properties();
        String className = null;
        PCI p = null;
        Class<?> cla = null;

        try {
            // 将读取流与文件关联
            fr = new FileReader(configure);
            // 使用Properties中的load方法把文件中的数据加载到Properties中
            pcipro.load(fr);

            // 遍历 Map 集合
            for(Entry<Object, Object> me: pcipro.entrySet()) {
                className = pcipro.getProperty((String) me.getKey());
                cla = Class.forName(className);
                p = (PCI) cla.newInstance();
                mb.usePCI(p);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            try {
                if(fr != null) {
                    fr.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```
运行结果：

    the MainBoard is running
    SoundCard open
    SoundCard close
    ShowCard open
    ShowCard close

#### 2、 通过反射越过泛型检查

需求：现在有个ArrayList<Integer\>的集合，请问如何在这个类型的集合中添加一个字符串。

这个问题看上去是不可完成的

我们先看一下下面的代码：

```java
public class ArrayListDemo {
     public static void main(String[] args) throws Exception {
          ArrayList<Integer> list = new ArrayList<Integer>();
          list.add(10);
     }
}
```
编译一下产生了 ArrayListDemo.class 文件，使用 jd-gui.exe 这个工具反编译一下这 class 文件

![](http://i.imgur.com/GlA6y8N.png)
    

看这两句代码：

```java
ArrayList<Integer> list = new ArrayList();
list.add(Integer.valueOf(10));
```

ArrayList 在new 的时候并没有指定 ArrayList 的类型，

而是在 add 的时候用 Integer.valueOf() 来产生 Integer 对象，而 add 方法原本的参数是Object，那么我们可以通过这一点用反射来越过这个泛型检查。

代码：

```java
public static void main(String[] args) throws Exception {
      ArrayList<Integer> list = new ArrayList<Integer>();
      list.add(10);
     
      // 获取 ArrayList 的 Class 对象。
      Class<?> c = ArrayList.class;
     // 获取 add 方法，并将参数写 Object.class，这样我们调用这个方法是就可以传入 Object 对象了
      Method m = c.getMethod("add", Object.class);
      // 执行方法
      m.invoke(list,"hello");
      m.invoke(list,"world");
 
      System.out.println(list);
 }
```
运行结果：

```java
 [10, hello, world]
```

#### 3、通过反射给任意的一个对象的任意的属性赋值为指定的值

需求：完成这样一个函数：setProperty(Object obj, String propertyName, Object value)可以随意将某个对象(obj)中的成员变量(propertyName)的值改变成value

代码：

Tool：

```java
public class Tool {
    public static void setProperty(Object obj, String propertyName, Object value) throws Exception{
          // 1. 获取这个对象的字节码对象
          Class<?> c = obj.getClass();
          // 2. 获取要改变的成员变量，因为成员变量可能是私有的用 getDeclaredField
          Field f = c.getDeclaredField(propertyName);
          // 3. 暴力访问
          f.setAccessible(true);
          // 4. 用 set 方法赋值
          f.set(obj,value);
     }
}
```


##七. 数组的反射

具有相同维数和相同元素类型的数组属于同一类型，也就是有相同的 Class 对象。

代表数组的 Class 实例对象的 getSupperClass() 方法返回的父类为Object 类对应的 Class 对象。

基本类型的一维数组可以作为 Object 类型使用，不能作为 Object[] 类型使用。

非基本类型的一维数组，既可以当做 Object 类型使用，又可以当做 Object[] 使用。

####  1、基本类型数组的Class及其父类演示：

```java
public static void main(String[] args) {

    // 打印 int 的 Class 对象
    System.out.println(int.class);  // int

    // 打印 int 的父类 Class 对象
    System.out.println(int.class.getSuperclass()); // null

    // 打印 Double[] (一维数组)的 Class 对象
    System.out.println(Double[].class); // class [Ljava.lang.Double;

    // 打印 int[](一维数组)的 Class 对象
    System.out.println(int[].class); // class [I

    // 打印 int[] 父类的 Class 对象
    System.out.println(int[].class.getSuperclass()); // class java.lang.Object

    // 打印 int[][](二维数组)的 Class 对象
    System.out.println(int[][].class); // class [[I

    // 打印 int[][] 父类的 Class 对象
    System.out.println(int[][].class.getSuperclass()); // class java.lang.Object
}    
```
我们一条一条的来分析结果：

(1). int 的 Class 是 int 本身

(2). int 的父类打印结果是 null 不是 Object，说明了基本数据类型不是一个类（是类就要继承Object）。

(3). 一维数组的 Class 用 Class ["数据类型的标示"  来表示。 Double 的标示是 Ljava.lang.Double，int 的标示是 I

(4). 一维数组的父类是 Object

(5). 二位数组的 Class 用 Class [["数据类型的标示"  来表示。

(6). 二维数组的父类是 Object (多维数组的父类也是Object，在这里就不演示了)

以上都是针对进本数据类型的分析，我们再来看一下引用数据类型

#### 2、应用类型数组的Class及其父类演示：

```java
public static void main(String[] args) {

    System.out.println(String.class);   // class java.lang.String
    System.out.println(String[].class); // class [Ljava.lang.String;

    System.out.println(String.class.getSuperclass());  // class java.lang.Object
    System.out.println(String[].class.getSuperclass());  // class java.lang.Object
    System.out.println(String[][].class.getSuperclass()); // class java.lang.Object
}
```
可以看到只有一点与基本数据类型不同： 引用数据类型的父类是 Object

#### 3、结论：

* 基本类型的一维数组，可当做Object类使用，不可以当做 Object[] 使用
* 引用类型的一维数组，可当做 Object 类使用和 Object[] 使用

* 基本类型的多维数组，可当做 Object 类使用，也可以当做比它维数低的 Object 多维数组使用
* 引用类型的多维数组，可当做 Object 类使用，也可以当做比它维数低或者和它维数相等的 Object 多维数组使用
          

验证：

![](http://i.imgur.com/I7VUkuk.png)

可以明显看到13行的编译错误。



![](http://i.imgur.com/ZDdrrvE.png)
        

解释：

```java
  int[][][] i = new int[2][3][];
  Object oi = i;     // 把三维数组转化为一个Object
  Object[] oi1 = i;  // 把三维int数组转化为一个一维Object数组，这个数组的长度是2，每元素是int[3][]

  Object[][] oi2 = i;
  // 把三维int数组转化为一个二维Object数组，这个数组的一维长度是2，每元素是int[3][]
  // 二维长度是3，每个元素是int[]

  Object[][][] oi3 = i;  // 编译错误

  String[][][] s = new String[4][5][6];
  Object[][][] os3 = s; 
  // 把字符串三维数组转化为Object三维数组，一维长度4，元素String[5][6]
  // 二维长度5，元素String[6]，三维长度6，元素String
```

​       

#### 4、数组的反射与 Array 类

Array 这个类提供了一些用反射的方法创建数组，获取数组元素，修改数组元素的方法。

列出下面几个常用的方法：

```java
public static Object newInstance(Class<?> componentType, int length) 
// 创建一个具有指定的组件类型和长度的新数组，componentType 表示数组类型的字节码文件，length 指数组的长度

public static Objectget(Object array, int index) 
// 返回指定数组对象中索引组件的值，如果该值是一个基本类型值，则自动将其包装在一个对象中
   
public static int getInt(Object array, int index) 
// 以int 形式返回指定数组对象中索引组件的值

public static void set(Object array,int index,Object value) 
// 将指定数组对象中索引组件的值设置为指定的新值

public static void setInt(Object array,int index,int i)  
// 将指定数组对象中索引组件的值设置为指定的 int 值
```

isArray() 是 Class 类中的方法，用来判断 Class 对象是否表示一个数组类

那么数组的Class对象是什么样子的呢，看下面代码
        

需求1：写一个打印函数， 当传一个对象，将对象打印 ，传一个数组，将数组中的内容打印 

代码：

```java
private static void print(Object obj) {
    // 如果传的对象是一个数组
    if(obj.getClass().isArray()) {
        // 循环遍历，用Array.getLength(obj)获取数组长度
        for(int i=0;i<Array.getLength(obj);i++) {
            // 用Array.get(obj,i)获取数组中的元素
            System.out.print(Array.get(obj,i) + " ");
        }
        System.out.println();
    } else {
        // 不是数组，直接打印
        System.out.println(obj);
    }

}
```
需求2：使用 Array 类中的方法创建一个数组和填充元素

代码：

```java
private static void newArray() {
    // 创建数组类型为 Object，长度为5
    Object obj = Array.newInstance(Object.class, 5);
    // 0 下标赋值 Integer 类型（JDK5自动装箱 Integer.valueOf(1)）
    Array.set(obj, 0, 1);
    // 1 下标赋值 String 类型
    Array.set(obj, 1, new String("abd"));
    // 2 下标赋值 Character 类型（自动装箱，Character..valueOf('t')）
    Array.set(obj, 2, 't');
    // 上面自己写的打印函数
    print(obj);   // 1 abd t null null 
}
```
##八. 总结

反射就是在程序运行过程中通过 Class 文件获取并使用一个类中属性和方法的一种机制。

本文重点：

* Constructor、Field、Method 获取和使用的方法
* 用反射和配置文件结合提高程序扩展性。
  
  ​      
  





