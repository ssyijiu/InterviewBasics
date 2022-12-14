## Java基础：集合框架

### 一. 什么是集合

集合是为了方便对多个对象进行存储而产生的一种容器。

既然集合也是一种容器，那么集合与数组有什么不同呢?

1. 数组的长度是固定的，集合的长度可变。
2. 数组可以存储基本数据类型，集合不可以（基础类型的包装类可以）。
3. 数组中元素必须是同一数据类型，集合中的数据类型可以不同。

### 二. 集合体系简单介绍

集合主要分为三种类型：

* Set
* List
* Map

Collection 是最基本的集合接口，声明了适用于 Java 集合（Set、List）的通用方法。

Set 和 List 都继承了Conllection，Map没有。

常用集合体系：

* List：
  * ArrayList
  * LinkedList
  * Vector
* Set:
  * HashSet
  * TreeSet
* Map:
    * HashMap
    * TreeMap
    * LinkedHashMap
    * Hashtable

 注意：Collection、List、Set、Map 都是接口，无法实例化。

### 三. Collection接口及其常用功能

Collection是集合框架的顶层接口，它定义了集合的共性功能。

- 添加

  ```java
  boolean add(Object e);  // 添加一个元素到集合中，成功返回 true，失败返回 false
  boolean addAll(Collection collection); // 添加一个集合的所有元素，成功返回 true，返回 false
  ```

- 删除

  ```java
  boolean remove(Object e); // 删除集合中的元素 e，成功返回 true，失败返回 false
  boolean removeAll(Collection coll);  // 删除集合中所有属于集合 coll 的元素
  void clear();    // 删除集合中的所有元素                        
  ```

- 判断

  ```java
  boolean contains(Object e);  // 集合中包含元素 e 返回 true，否则 false
  boolean isEmpty();  // 集合有元素返回 true，否则返回 false
  ```

- 获取

  ```java
  Iterator iterator();  // 获取该集合的迭代器
  int size();           // 获取集合长度
  ```


- 获取交集

  ```java
  retainAll(Collection coll);  // 获取原集合与coll的交集，有交集返回true，没有返回false
  ```

- 集合变数组

  ```java
  Object[] toArray();  // 将集合中的元素存入一个Object数组中
  ```


- 代码：（没有迭代，后面单独拿出来讲）

  ```java
  public class CollectionDemo {
       public static void main(String[] args) {
            // 用ArrayList实例化Collection 
            Collection coll = new ArrayList();
            // 1. add
            coll.add(1);
            coll.add(2);
            coll.add(3);
            Collection coll2 = new ArrayList();
            coll2.add(3);
            coll2.add(4);
            System.out.println(coll);  // [1, 2, 3]
            System.out.println(coll2); // [3, 4]
   
            // 2. addAll
            coll.addAll(coll2);
            System.out.println(coll);  // [1, 2, 3, 3, 4]
   
            // 3. remove
            coll.remove(1);
            System.out.println(coll);  // [2, 3, 3, 4]
   
            // 4. removeAll
            coll.removeAll(coll2);
            System.out.println(coll);  // [2]
   
            // 5. clear
            coll.clear();
            System.out.println(coll);  // []
   
            // 6. contains
            System.out.println(coll2.contains(3));  // true
   
            // 7. isEmpty
            System.out.println(coll.isEmpty());  // true 
            System.out.println(coll2.isEmpty()); // false
   
            // 8. size()
            System.out.println(coll.size());  // 0
            System.out.println(coll2.size()); // 2
   
            // 9. retainAll 取交集
            coll.add(1);
            coll.add(2);
            coll.add(3);
            System.out.println(coll);  // [1, 2, 3]
            System.out.println(coll2); // [3, 4]
   
            coll.retainAll(coll2);
            System.out.println(coll);  // [3]
            System.out.println(coll2); // [3, 4]
   
            // 10. toArray()
            Object[] objs = coll2.toArray();
            for(Object obj: objs) {
                 System.out.print(obj + "、");  // 3、4、
            }
      }
  }
  ```


### 四. 迭代和迭代器 Iterator

迭代是取出集合中元素的一种方式，迭代器 Iterator 由集合中的 iterator() 方法获取。

因为每种集合底层的数据结构不同，所以取出的动作细节也不一样，所以该迭代器对象是在集合中进行内部实现的，也就是 iterator() 方法在每个容器中的实现方式是不同的。

#### Iterator 接口声明了如下方法：

```java
boolean hasNext() // 判断集合中元素是否遍历完毕，如果没有，就返回 true
Object next()     // 返回下一个元素
void remove()     // 移除元素

```

代码：


```java
public class IteratorDemo {
    public static void main(String[] args) {
        Collection coll = new ArrayList();
        coll.add(1);
        coll.add(2);
        coll.add(3);
        coll.add("hello");
        
        // 获取迭代器并遍历集合，for 循环结束后 Iterator 变量内存释放，更高效
        for(Iterator it = coll.iterator();it.hasNext();) {
           // next() 方法返回的是 Objetc (在没涉及泛型的情况下)
           Object obj = it.next();
           System.out.println(obj);
        }
     }
}
```

#### 小提示：

1.  为保证正常迭代集合中的所有元素，不要在循环中多次调用 next() 方法，因为每次调用都会使迭代器指向元素的指针向指向下一个元素，避免出现 NoSuchElementException
2.  在没加泛型时，迭代器的 next() 方法返回值类型是 Object
3.  如果在迭代过程中，不要通过集合变量调用添加或删除元素的方法对集合中元素个数进行修改，则会引发**并发修改异常 ConcurrentModificationException** 在迭代器迭代过程中只能使用 remove 对元素进行移除

### 五. List

#### List集合的特点：

1. 元素以线性方式存储，集合中可以存放重复对象
2. 有序（元素的存入顺序和取出顺序一致）
3. 元素都有索引，可以使用索引访问元素。

####List集合特有的方法，凡是使用索引的都是该集合的特有方法

- 增

  ```Java
  void add(index, element);   // 在指定的索引位置添加元素 element
  void addAll(index, coll);   // 在指定的索引位置添加集合 coll 中的所有元素 
  ```

- 删

  ```java
  Object remove(index);  // 删除并获取指定索引位置的元素
  boolean remove(e);     // 删除集合中的元素 e，如果有多个 e 删除第一个
  ```


- 改

  ```java
  Object set(index,element);  // 修改自定索引位置的元素，返回修改之前的元素
  ```

- 获取

  ```java
  Object get(index):     // 取出集合中的元素，注意取出来的元素类型
  int indexOf(obj);      // 正序索引，获取第一个指定元素的位置。
  int lastIndexOf(obj);  // 倒序索引，获取第一个指定元素的位置。
  List subList(from,to); // 获取部分元素
  ```

- List 集合特有的迭代器

  ```java
  ListIterator listIterator();  // 返回 ListIterator，List 集合特有的迭代器
  ```

- 提示：List 集合中判断元素是否相同，移除等操作，依据的是元素的 equals() 方法

- 代码：

  ```java
   import java.util.ArrayList;
   import java.util.Collection;
   import java.util.Iterator;
   import java.util.List;
       public class ListDemo {
           public static void main(String[] args) {
                List list = new ArrayList();
                // 1. add
                list.add(1);
                list.add(2);
                list.add(3);
   
                List list2 = new ArrayList();
                list2.add("hello");
                list2.add("world");
   
                System.out.println(list);  // [1, 2, 3]
                System.out.println(list2); // [hello, world]
   
                // 2. addAll
                list.addAll(list2);
                System.out.println(list);  // [1, 2, 3, hello, world]
   
                // 3. remove
                list.remove(1);
                System.out.println(list);  // [1, 3, hello, world]
                list.remove("hello");
                System.out.println(list);  // [1, 3, world]
   
                // 4. set
                list.set(0,"world");
                System.out.println(list);  // [world, 3, world]
   
                // 5. get
                System.out.println(list.get(0));  // world
   
                // 6. indexOf(obj); lastIndexOf(obj);
                System.out.println(list.indexOf("world"));  // 0
                System.out.println(list.lastIndexOf("world")); // 2
   
                // 7. subList
                List subList = list.subList(0,2);
                System.out.println(subList);  // [world, 3]
           }
      }
  ```


### 六. List集合特有的迭代器

ListIterator 是 Iterator的子接口。

在迭代时，不可以通过集合对象的方法操作集合中的元素。因为会发生 **ConcurrentModificationException** 异常。所以，在迭代时，只能用迭代器的放过操作元素，可是 Iterator 方法是有限的，只能对元素进行判断，取出，删除的操作。


如果想要其他的操作如添加，修改等，就需要使用其子接口，ListIterator，该接口只能通过 List 集合的 listIterator 方法获取。

####listIterator 特有的方法：

````java
void add(Object obj)  // 添加元素，添加到下一个 next() 方法返回的元素前面(光标的后面)
boolean hasPrevious() // 有上一个元素，返回 true，没有返回 false.
Object previous()     // 返回上一个元素，与 next 相反。
void set(Object e)    // 用指定元素替换 next 或 previous 返回的最后一个元素
int nextIndex()       // 返回下一个元素的索引
int previousIndex()   // 返回上一个元素的索引
````

代码：

```Java
public class ListIteratorDemo {
     public static void main(String[] args) {
          List list  = new ArrayList();
          list.add(1);
          list.add(2);
          list.add(3);
          list.add("hello");
          list.add("world");
          ListIterator it = list.listIterator();
          while(it.hasNext()) {
               Object obj = it.next();
               if(obj.equals("hello")) {
                    // 此时光标在 hello和 world 之间, 上一个是 hello 下一个是 world
                    System.out.println("hello nextIndex:" + it.nextIndex());
                    System.out.println("hello previousIndex:" + it.previousIndex());            
                    // 将 hello 该为 java
                    it.set("java");
                    // 在 java 后面添加 html
                    it.add("html");
               }
          }
     }
}
```

---
```java
运行结果：
hello nextIndex:4
hello previousIndex:3
[1, 2, 3, java, html, world]
```

### 七. List 接口的重要实现类

1. ArrayList 底层的数据结构使用的是数组结构，特点：查询快，增删慢，非线程安全。
   可以看到，我们前面讲 List 的时候都是用 ArrayList 实例化，这里就不再讲解了。

2. LinkedList 底层使用的链表数据结构，特点：增删快，查询慢，非线程安全。
3. Vector 底层是数组数据结构，线程安全，因为效率低，被 ArrayList 替代了，开发几乎用不到，
  多线程访问时使用 Collections.synchronizedList(new ArrayList());

####LinkedList 特有方法：

- 增

  ```java
  void addFirst();
  void addLast();
  ```

- 获取，如果集合中没有元素，会出现NoSuchElementException

  ```java
  E getFirst();
  E getLast();
  ```

- 删，获取元素，并删除元素。如果集合中没有元素，会出现NoSuchElementException

  ```java
  E removeFirst();
  E removeLast();
  ```


- 在 JDK1.6 以后，出现了替代方法

  ```java
  // 和 addFirst()没有什么不同，只不过返回值变了，使用几乎没区别
  boolean offFirst();  
  boolean offLast();

  // 源码，一直返回 true
  public boolean offerFirst(E e) {
    	addFirst(e);
    	return true;
  }

  // 获取元素，如果集合中没有元素，会返回 null
  E peekFirst();
  E peekLast();

  // 获取元素，并删除元素，如果集合中没有元素，会返回 null
  E pollFirst();
  E pollLast();
  ```

- 代码

  ```java
  public class LinkedListDemo {
      public static void main(String[] args) {
          LinkedList list = new LinkedList();
          // 1. addLast、addFirst
          list.addLast(1);
          list.addLast(2);
          list.addLast(3);

          list.addFirst("hello");
          System.out.println(list);  // [hello, 1, 2, 3]

          // 2. peekFirst、peekLast
          System.out.println(list.peekFirst());	// hello
          System.out.println(list.peekLast());    //  3

          // 3. pollFirst、pollLast
          list.pollFirst();
          System.out.println(list);  // [1, 2, 3]

          list.pollLast();
          System.out.println(list);  // [1, 2]
      }
  }
  ```

- LinkedList 的应用一：用 LinkedList 实现队列数据结构

  ```java
  public class QueueDemo {
      private final LinkedList queue = new LinkedList();

      // 添加元素到队列，总是放在队尾
      public void add(Object obj) {
          queue.addLast(obj);
      }

      // 判断队列是否为空
      public boolean isEmpty() {
          return queue.isEmpty();
      }

      // 返回队列元素个数
      public int length() {
          return queue.size();
      }

      // 得到队头元素, 队列为空 peekFirst 返回 null
      public Object get() {
          return queue.peekFirst();
      }

      // 移除队头元素
      public boolean remove() {
          if (!queue.isEmpty()) {
              queue.removeFirst();
              return true;
          }
          return false;
      }

      // 查看队列元素
      public void print() {
          System.out.println(queue);
      }

      public static void main(String[] args) {
          QueueDemo queue = new QueueDemo();
          queue.add(1);
          queue.add(2);
          queue.add("java");
          queue.print();  // [1, 2, java]
          System.out.println(queue.get());     // 1

          queue.remove();
          System.out.println(queue.length());  // 2
          queue.print();  // [2, java]
      }
  }
  ```

- LinkedList 的应用二：用 LinkedList 实现堆栈数据结构

  ```java
  public class StackDemo {
      private final LinkedList stack = new LinkedList();
   
       // 入栈
       public void push(Object obj) {
            stack.addFirst(obj);
       }
       
       // 判断栈是否为空
       public boolean isEmpty() {
            return stack.isEmpty();
       }
    
       // 返回栈元素个数
       public int length() {
            return stack.size();
       }
   
       // 得到栈顶元素，空栈 peekFirst 返回 null
       public Object get() {
            return stack.peekFirst();
       }
       // 出栈，空栈 pollFirst 返回 null
       public Object pop() {
            return stack.pollFirst();
       }
   
       // 查看栈元素，从栈顶到栈底
       public void print() {
           System.out.println(stack);
       }
   
   	 public static void main(String[] args) {
        	StackDemo stack = new StackDemo();
        	stack.push(1);
        	stack.push(2);
        	stack.push("java");
   
        	stack.print();  // [java, 2, 1]
   
        	System.out.println(stack.get());     // java
   
        	stack.pop();
        	System.out.println(stack.length());  // 2
        	stack.print();  // [2, 1]
   	}
  }
  ```

### 八. Set

Set 集合的特点：元素无序 (存入和取出的顺序不一定一致)，元素不可以重复。

Set 可以被用来过滤在其他集合中存放的元素，从而得到一个没有包含重复新的集合。

Set 集合有两个重要的子类：

* HashSet：底层数据结构是哈希表，存取速度比较快，线程不安全。
* TreeSet： 底层数据结构是二叉树，可以对集合中的元素进行排序，线程不安全。

####1. HashSet 详解：

HashSet 是如何保证元素唯一性的呢?
是通过元素的两个方法，hashCode 和 equals 来完成。
如果元素的 hashCode 值相同，才会判断 equals 是否为 true。
如果元素的 hashCode 值不同，不会调用 equals。

因此在将元素存储到 HashSet 集合时，必须保证元素对象覆盖 Object 类的 hashCode 和 equals 方法。

对于判断元素是否存在、查找（iterator）、添加、删除等操作，依赖的方法都是hashCode 和 equals。

代码：

```java
public class HashSetDemo {
    public static void main(String[] args) {
        HashSet hs = new HashSet();
        hs.add(new Person("李小龙", 27));
        hs.add(new Person("成龙", 45));
        hs.add(new Person("成龙", 45));
        hs.add(new Person("叶良辰", 19));
        hs.add(new Person("叶良辰", 20));
        hs.add(new Person("叶良辰", 19));  // 和上面的一样
        for (Iterator it = hs.iterator(); it.hasNext(); ) {
            Person p = (Person) it.next();
            System.out.println(p);
        }
    }
}

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.age = age;
        this.name = name;
    }

    public Person() {
    }

    // 重写hashCode方法，* 39 为了尽量让各元素的哈希值不同
    public int hashCode() {
        return name.hashCode() + age * 39;
    }

    // 重写equals方法，
    public boolean equals(Object obj) {
        // 如果是同一个元素，直接返回true
        if (this == obj)
            return true;
        // 传入空元素
        // HashSet 底层是由 HashMap 只使用一个键实现的，而 HashMap 的键允许存入 null
        // 在存入是先判断是不是 null，再计算 hashCode 值，如果 null，则不必计算 hashCode 值
        if (obj == null)
            return false;
        // 存入其他对象抛出异常
        if (!(obj instanceof Person)) {
            throw new ClassCastException("类型不匹配");
        }
        Person p = (Person) obj;
        // 通过姓名和年龄判断是不是同一个对象
        return name.equals(p.name) && (age == p.age);
    }

    public String toString() {
        return "Person [age=" + age + ", name=" + name + "]";
    }
}
```

---
    运行结果：
    Person [age=27, name=李小龙]
    Person [age=45, name=成龙]
    Person [age=19, name=叶良辰]
    Person [age=20, name=叶良辰]

####2. TreeSet 详解
TreeSet 如何实现元素的可排序呢?

**TreeSet 排序的第一种方式：**

让元素自身具备比较性，元素需要实现 Comparable 接口，覆盖 compareTo 方法。
这种方式也称为元素的自然顺序，或者叫做默认顺序。

**TreeSet的第二种排序方式：**

当元素自身不具备比较性时，或者具备的比较性不是所需要的，这时就需要让集合自身具备比较性。 

定义一个比较器，实现 Comparator 接口，覆盖compare方法，将比较器对象作为参数传递给 TreeSet 集合的构造函数。

当两种排序都存在时，**以比较器为主**，排序时，当主要条件相同时，一定判断一下次要条件。

两种排序的底层实现都是二叉树（红黑树），最终返回值负数代表小于会放在二叉树左边，正数代表大于放右边，0 则相同不插入。

排序的顺序为二叉树的中序遍历。

**TreeSet是如何保证元素唯一性的呢?**

通过 compareTo 或 compare 方法的返回值，是正数、负数或零，则两个对象较大、较小或相等，相等时则不会存入集合。


排序方式一：让元素实现 Comparable 接口，覆盖 compareTo 方法

```java
class Person implements Comparable{
    private String name;
    private int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String toString() {
        return "name:" + name + " " + "age:" + age;
    }

    // 重写 Comparable 接口中的 compareTo 方法
    public int compareTo(Object o) {
        Person p = (Person)o;
        // 以年龄为主要条件排序
        if(age != p.age) {
            return age - p.age;
        } else {
            // 年龄相等，以姓名为次要条件排序
            return name.compareTo(p.name);
        }
    }
}
// 测试代码：
public class Test{
    public static void main(String[] args) {
        TreeSet<Person> tr  = new TreeSet<>();
        tr.add(new Person("11", 11));
        tr.add(new Person("12", 9));
        tr.add(new Person("13", 13));
        tr.add(new Person("12", 9));
        tr.add(new Person("99", 1));
        for (Person aTr : tr) {
            System.out.println(aTr);
        }
    }
}      
```

---
    运行结果：
    name:99 age:1
    name:12 age:9
    name:11 age:11
    name:13 age:13

排序方式二： 定义一个类实现 Comparator 接口，覆盖 compare 方法

```java
class Compare implements Comparator {
    // 实现 Camparator 接口，重写 compare 方法
    public int compare(Object o1, Object o2) {
        Person p1 = (Person) o1;
        Person p2 = (Person) o2;
        // 以姓名为主要条件排序
        int res = p1.getName().compareTo(p2.getName());
        // 姓名相同，以年龄为次要条件排序
        if (res == 0) {
            return p1.getAge() - p2.getAge();
        }
        return res;
    }
}

// 测试代码:
public class TreeSetDemo {
    public static void main(String[] args) {

        TreeSet<Person> tr = new TreeSet<>(new Compare());
        tr.add(new Person("11", 11));
        tr.add(new Person("12", 9));
        tr.add(new Person("13", 13));
        tr.add(new Person("12", 9));
        tr.add(new Person("99", 1));
        for (Person aTr : tr) {
            System.out.println(aTr);
        }
    }
}
```

---

    运行结果：
    name:11 age:11
    name:12 age:9
    name:13 age:13
    name:99 age:1

####3. LinkedHashSet
LinkedHashSet 是一个有序的 HashSet ，即按照将元素插入到set中的顺序进行迭代。
代码：

```java
public class LinkedHashSetDemo {
     public static void main(String[] args) {
          LinkedHashSet lhs = new LinkedHashSet();
          lhs.add(1);
          lhs.add(3);
          lhs.add(5);
          lhs.add(2);
          lhs.add(0);
          // 元素的取出顺序与插入顺序一致
          for(Iterator it = lhs.iterator();it.hasNext();) {
               System.out.print(it.next() + " ");  // 1 3 5 2 0
          }
     }
}
```

### 九. Map

Map 集合 Collection 集合不同的是，它是双列集合，存储时可以给对象加上名字，即键（Key）。

Map 集合中存储的是键值对，存储时一对一对的存储，键值对关系的特点是：一个键可以对应多个值，但一个值只能对应唯一的一个键，换句话说，键具有唯一性。

####1. Map 集合常用方法

- 添加

  ```java
  V put (K key, V value)  // 将一组键值对添加进 Map 集合
  // key 重复，后添加的值会覆盖原有键的对应值，并 put 方法会返回被覆盖的值，没有返回 null  
    
  void putAll(Map<? extends K,? extends V> m)  // 将 m 集合中所有的键值对添加进 Map 集合
  ```


- 删除

  ```java
  void clear()  // 清空 Map 集合
  V remove(Object key)  // 根据指定的键删除这个键值对，并返回该 key 映射的 value
                        // 如果指定的键不存在，返回null                      
  ```

- 判断

  ```java
  boolean containsValue(Object value)  // 判断值是否存在
  boolean containsKey(Object key)      // 判断键是否存在
  boolean isEmpty()     // 判断集合是否为空
  ```

- 获取

  ```java
  V get(Object key)  // 通过键获取值，如果键不存在，返回 null
  int size()         // 获取集合中键值对的数目
  Collection<V> values()  // 获取 Map 集合中所有的值，返回一个 Collection
  ```

- 代码

  ```java
  public class MapDemo {   
        public static void main(String[] args) {
        Map m = new HashMap();
        
        // 1. 添加
        System.out.println(m.put("1","hello"));   // null
        System.out.println(m.put("1","world"));   // hello
   
        m.put("2","22");
        m.put("3","333");
        m.put("4","4444");
        System.out.println(m);  // {3=333, 2=22, 1=world, 4=4444}
   
        // 2. 删除
        System.out.println(m.remove("1"));  // world
        System.out.println(m);  // {3=333, 2=22, 4=4444}
   
        // 3. 判断
        System.out.println(m.containsValue("22"));  // true
        System.out.println(m.containsKey("3"));   // true
        System.out.println(m.isEmpty());  // false
   
        // 4. 获取
        System.out.println(m.get("4"));  // 4444
        // 存在键字符串"4", 不存在Integer4(自动装箱)
        System.out.println(m.get(4));  // null
        System.out.println(m.size());  // 3
        System.out.println(m.values()); // [333, 22, 4444]
     }
  }
  ```

####2. Map集合的两种取出方式 
取出原理：将 Map 集合转换为 Set 集合，再对 Set 集合遍历

##### 取出方式一：Set<K\> keySet()
将Map集合中键存储到一个 Set 集合中，再利用迭代器或者增强 for 循环遍历 set 集合的键，用 get 方法通过键获取值。

```java
public class KeySetDemo {
     public static void main(String[] args) {
          Map map = new HashMap();
          map.put(1,1);
          map.put(2,22);
          map.put(3,333);
          map.put(4,4444);
          map.put(5,55555);
          map.put("itheima","itheima");
         
          // 取出所有的键值并且保存在一个Set集合中
          Set s = map.keySet();
          System.out.println("使用增强for循环遍历");
         
          // Set集合中存储的元素是Object，使用增强for来遍历
          for(Object obj : s) {
               System.out.println(obj + " = " + map.get(obj));
          }
 
          System.out.println("使用迭代器遍历");
          for(Iterator it = s.iterator();it.hasNext();) {
               // 通过迭代器获取键
               Object obj = it.next();
               // 用get方法通过键获取值
               System.out.println(obj + " = " + map.get(obj));
          }
     }
}
```

运行结果：

```java
使用增强for循环遍历
1 = 1
2 = 22
3 = 333
4 = 4444
5 = 55555
itheima = itheima
使用迭代器遍历
1 = 1
2 = 22
3 = 333
4 = 4444
5 = 55555
itheima = itheima
```

##### 取出方式二：Set<Map.Entry<K,V>> entrySet()

将 Map 中键和值的映射关系作为对象存储到了 Set 集合中，而这个映射关系的类型就是Map.Entry类型

Map.Entry 是Map 接口中的一个 static 内部接口，就相当于是类中有内部类一样。

为何要定义在其内部呢？

原因：

* Map 集合中存的是映射关系这样的两个数据，是先有 Map 这个集合，才可有映射关系的存在，而且此类关系是集合的内部事务。
* 并且这个映射关系可以直接访问 Map 集合中的内部成员，所以定义在内部。

Map.Entry 常用方法介绍：

```java
K getKey()   // 返回该映射关系对应的键。
V getValue() // 返回该映射关系对应的值。
```

代码：

```java
public class EntrySetDemo {
     public static void main(String[] args) {
          Map map = new HashMap();
          map.put(1,1);
          map.put(2,22);
          map.put(3,333);
          map.put(4,4444);
          map.put(5,55555);
          map.put("itheima","itheima");
         
          // 得到存放映射关系（Entry）的集合
          Set s = map.entrySet();
          System.out.println("使用增强for循环遍历");
          for(Object obj : s) {
               Map.Entry me = (Entry) obj;
               // 使用Entry的getKey、getValue得到Map集合中的键和值
               System.out.println(me.getKey() + " = " + me.getValue());
          }
 
          System.out.println("使用迭代器遍历");
          for(Iterator it = s.iterator();it.hasNext();) {
               Map.Entry me = (Entry) it.next();
               System.out.println(me.getKey() + " = " + me.getValue());
          }
     }
}
```

运行结果：

```java
使用增强for循环遍历
1 = 1
2 = 22
3 = 333
4 = 4444
5 = 55555
itheima = itheima
使用迭代器遍历
1 = 1
2 = 22
3 = 333
4 = 4444
5 = 55555
itheima = itheima
```

### 十. Map集合的几个常用子类

1. Hashtable
   底层是哈希表数据结构，不可以存入 null 键 null 值，该集合是线程同步的，效率低。

2. HashMap
   底层是哈希表数据结构，允许使用 null 值和 null 键，该集合是不同步的，将 hashtable 替代，效率高。
   判断元素唯一性与 HashSet 相同：hashCode 和 equals、如果元素的 hashCode 值相同，判断 equals 是否为 true。

3. LinkedHashMap
   LinkedHashSet 是一个有序的 Map 集合，即按照将元素插入到 Map 中的顺序进行迭代。

4. Properties      

   Hashtable 的子类，用来存储键值对型的配置文件的信息，可以和 IO 技术相结合。

5. TreeMap
   底层是二叉树数据结构，线程不同步，可以用于给 map 集合中的键进行排序，排序方法和 TreeSet 一样。

可以看到，Map集合与 Set 集合非常相似，Set 有 HashSet，TreeSet，LinkedHashSet，而 Map 集合对应有
HashMap、TreeMap、LinkedHashMap。

实际上，Set集合的底层就是 Map 集合，Set 集合只是将 Map 中的 Key 抽取而成一个新的集合。

二者的最大区别就是 Set 集合只存储单列数据，而 Map 集合存储的是键值对双列数据。

因此，当所要描述的对象或数据存在映射关系时就选择使用Map集合。

因为 Map 集合与 Set 太像，这里就不单独用代码演示 HashMap、TreeMap、LinkedHashMap了，简单看一下Properties，至于 Properties 与 IO 流和配置文件的结合在后面的 IO 流和反射中要讲到。

```java
PropertiesDemo：
public class PropertiesDemo {
     public static void main(String[] args) {
     
      // 创建一个Properties，用System.getProperties()实例化
      // 这样，在这个Properties中我们就可以看到所有关于系统的属性
      Properties prop = System.getProperties();
 
      // 如何在系统中自定义一些特有信息呢?
      prop.setProperty("mykey","myvalue");
      System.out.println("value=" + prop.getProperty("mykey"));
   
      // 当然，用 pu t和 get 方法也是一样的
      prop.put("mykey2","myvalue2");
      System.out.println("value2=" + prop.get("mykey2"));
      // 获取指定属性信息
      String value = prop.getProperty("os.name");
      System.out.println("os.name="+value);
    
      // 因为 Properties 是 Hashtable的子类，也就是 Map 集合的一个子类对象。
      // 那么可以通过 map 的方法取出该集合中的元素。
      // 获取所有属性信息。
      System.out.println("----以下是系统属性信息-----");
      for(Object obj : prop.keySet()) {
          String va = (String)prop.get(obj);
           System.out.print(obj+"::"+value + "、");
      }
   }
}
```

运行结果：(只粘贴了一部分)

```java
value=myvalue
value2=myvalue2
os.name=Windows Vista
----以下是系统属性信息-----
java.runtime.name::Java(TM) SE Runtime Environment
sun.boot.library.path::E:\Genuitec\Common\binary\com.sun.java.jdk.win32.x86_1.6.0.013\jre\bin
java.vm.version::11.3-b02
java.vm.vendor::Sun Microsystems Inc.
... 
```

### 十一. Map的应用案例

获取该字符串中的字母出现的次数，如："sjokafjoilnvoaxllvkasjdfns"，希望打印的结果是：a(3)c(0).....

分析：通过结果发现，每个字母都有对应的次数，说明字母和次数之间有映射关系，选择使用 Map 集合，而打印结果是有排序的，选择 TreeMap 集合。


```java
public class StringCount {

    public static void main(String[] args) {
        String str = "sjokafjoilnvoaxllvkasjdfnsmmzzzzz";
        // 从字符串中获取字符个数的Map集合
        Map map = getCharCountFromStr(str);
        System.out.println(map);

        StringBuilder sbr = new StringBuilder();
        // 遍历Map
        for(Object obj: map.entrySet()) {
            Map.Entry me = (Map.Entry)obj;
            // 通过map的键和值来拼接字符串
            sbr.append(me.getKey()).append("(").append(me.getValue()).append(")");
        }
        System.out.println(sbr);

    }

    private static Map getCharCountFromStr(String str) {
        Map map = new TreeMap();
        char c;
        // 遍历字符串
        for(int i=0;i<str.length();i++) {
            c = str.charAt(i);
            // 如果 Map 中包含该字符，值加一
            if(map.containsKey(c))
                map.put(c,(Integer)map.get(c) + 1);
            // 如果不包含保存进去，值初始化为一
            else
                map.put(c,1);
        }
        return map;
    }	
}

// 运行结果
{a=3, d=1, f=2, i=1, j=3, k=2, l=3, m=2, n=2, o=3, s=3, v=2, x=1, z=5}
a(3)d(1)f(2)i(1)j(3)k(2)l(3)m(2)n(2)o(3)s(3)v(2)x(1)z(5)
```

### 十二. 总结

####常用集合体系：
* Collection：
  * List

    * ArrayList
    * LinkedList
    * Vector

  * Set

    * HashSet
    * TreeSet
    * LinkedHashSet

  * Map

    - TreeMap

    * LinkedHashMap
####如此多的集合，我们在实际开发中如何选择呢? 

* 是否存在映射关系（键值对）?

       * 是：Map
           * 是否需要排序?
               * 是：TreeMap
               * 否：HashMap
                   * 是否要求元素有序?
                       * 是：LinkedHashMap
                       * 否：HashMap
               * 不明确（无所谓）：HashMap
                 
       * 否：Collection
           * 元素是否可以重复?
               * 是：List
                   * 查询多，增删少：ArrayList
                   * 查询少，增删多：LinkedLIst
                   * 不知道，区别不大：ArrayList
               * 否：Set             
                   * 是否需要排序?
                       * 是：TreeSet
                       * 否：HashSet
                           * 是否要求元素有序?
                               * 是：LinkedHashSet
                               * 否：HashSet
                       * 不明确（无所谓）：HashSet











