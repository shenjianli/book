# interview
## 1.类加载过程及对象创建过程
### 1.1 类加载过程：
1， JVM会先去方法区中找有没有相应类的.class存在。如果有，就直接使用；如果没有，则把相关类的.class加载到方法区
2， 在.class加载到方法区时，会分为两部分加载：先加载非静态内容，再加载静态内容
3， 加载非静态内容：把.class中的所有非静态内容加载到方法区下的非静态区域内
4， 加载静态内容：
    4.1、把.class中的所有静态内容加载到方法区下的静态区域内
    4.2、静态内容加载完成之后，对所有的静态变量进行默认初始化
    4.3、所有的静态变量默认初始化完成之后，再进行显式初始化
    4.4、当静态区域下的所有静态变量显式初始化完后，执行静态代码块
5，当静态区域下的静态代码块，执行完之后，整个类的加载就完成了。
 
### 1.2 对象创建过程：
1， 在堆内存中开辟一块空间

2， 给开辟空间分配一个地址

3， 把对象的所有非静态成员加载到所开辟的空间下

4， 所有的非静态成员加载完成之后，对所有非静态成员变量进行默认初始化

5， 所有非静态成员变量默认初始化完成之后，调用构造函数

6， 在构造函数入栈执行时，分为两部分：先执行构造函数中的隐式三步，再执行构造函数中书写的代码
    
    6.1、隐式三步：
        1，执行super语句
        2，对开辟空间下的所有非静态成员变量进行显式初始化
        3，执行构造代码块
    6.2、在隐式三步执行完之后，执行构造函数中书写的代码
7，在整个构造函数执行完并弹栈后，把空间分配的地址赋值给一个引用对象
### 总结类中能书写的成员

栈： 用来运行函数。可以存储局部信息

特点：先进后出

堆：用来存储实体（被new创建出来的）。

特点：
	
	1，  每一个实体所开辟的空间都有一个地址

	2，  每一个实体中存储的数据都有一个默认初始值

方法区：

存放class和static。 方法区其实是由N多个小的区域构成。有存放非静态内容的非静态区域，还有存放静态内容的静态区域，还有存放常量的常量池等
### 图解创建过程
![对象创建过程](img/class_object.png)

### 代码注释创建过程

```
class Person {  
    int age;  
  
    Person(int age) {  
        this.age = age;  
    }  
}  
  
class Student extends Person {
	// 1, 非静态成员加载完成之后，对所有非静态成员变量进行默认初始化
	// String school = null;	
	// 3，对开辟空间下的所有非静态成员变量进行显式初始化  
    String school = "abc";  
  
    Student(String school, int age) {  
        // 2，执行super语句  
        super(age);  
        // 5、在隐式三步执行完之后，执行构造函数中书写的代码  
        this.school = school;  
    }  
  
    {  
        // 4，执行构造代码块  
        System.out.println("Student code block");  
    }  
}  
```

### 类的组成
```
class  ABC
{
         非静态成员变量
         静态成员变量
 
        非静态成员函数
        静态成员函数
 
构造函数：ABC(...){...}
 
静态代码块： static {...}
构造代码块 :  {...}
}
```
## 2.栈堆内存
### 栈：
保存局部变量的值，包括：1.用来保存基本数据类型的值；2.保存类的实例，即堆区对象的引用(指针)。也可以用来保存加载方法时的帧。
### 堆：
用来存放动态产生的数据，比如new出来的对象。注意创建出来的对象只包含属于各自的成员变量，并不包括成员方法。因为同一个类的对象拥有各自的成员变量，存储在各自的堆中，但是他们共享该类的方法，并不是每创建一个对象就把成员方法复制一次。

无非就是两种类型的变量：基本类型和引用类型。二者作为局部变量，都放在栈中，基本类型直接在栈中保存值，引用类型只保存一个指向堆区的指针，真正的对象在堆里。作为参数时基本类型就直接传值，引用类型传指针。


### 小结
1.分清什么是实例什么是对象。Class a= new Class();此时a叫实例，而不能说a是对象。实例在栈中，对象在堆中，操作实例实际上是通过实例的指针间接操作对象。多个实例可以指向同一个对象。

2.栈中的数据和堆中的数据销毁并不是同步的。方法一旦结束，栈中的局部变量立即销毁，但是堆中对象不一定销毁。因为可能有其他变量也指向了这个对象，直到栈中没有变量指向堆中的对象时，它才销毁，而且还不是马上销毁，要等垃圾回收扫描时才可以被销毁。

3.以上的栈、堆、代码段、数据段等等都是相对于应用程序而言的。每一个应用程序都对应唯一的一个JVM实例，每一个JVM实例都有自己的内存区域，互不影响。并且这些内存区域是所有线程共享的。这里提到的栈和堆都是整体上的概念，这些堆栈还可以细分。

4.类的成员变量在不同对象中各不相同，都有自己的存储空间(成员变量在堆中的对象中)。而类的方法却是该类的所有对象共享的，只有一套，对象使用方法的时候方法才被压入栈，方法不使用则不占用内存。


```
public class test {  
    public static void main(String[] args) {      
        objPoolTest();  
    }  
  
    public static void objPoolTest() {  
        int i = 40;  
        int i0 = 40;  
        Integer i1 = 40;  
        Integer i2 = 40;  
        Integer i3 = 0;  
        Integer i4 = new Integer(40);  
        Integer i5 = new Integer(40);  
        Integer i6 = new Integer(0);  
        Double d1=1.0;  
        Double d2=1.0;  
          
        System.out.println("i=i0\t" + (i == i0));  
        System.out.println("i1=i2\t" + (i1 == i2));  
        System.out.println("i1=i2+i3\t" + (i1 == i2 + i3));  
        System.out.println("i4=i5\t" + (i4 == i5));  
        System.out.println("i4=i5+i6\t" + (i4 == i5 + i6));      
        System.out.println("d1=d2\t" + (d1==d2));   
          
        System.out.println();          
    }  
}  
```
结果：
```
i=i0    true  
i1=i2   true  
i1=i2+i3        true  
i4=i5   false  
i4=i5+i6        true  
d1=d2   false
```


### 结果分析：
1.i和i0均是普通类型(int)的变量，所以数据直接存储在栈中，而栈有一个很重要的特性：栈中的数据可以共享。当我们定义了int i = 40;，再定义int i0 = 40;这时候会自动检查栈中是否有40这个数据，如果有，i0会直接指向i的40，不会再添加一个新的40。

2.i1和i2均是引用类型，在栈中存储指针，因为Integer是包装类。由于Integer包装类实现了常量池技术，因此i1、i2的40均是从常量池中获取的，均指向同一个地址，因此i1=12。

3.很明显这是一个加法运算，Java的数学运算都是在栈中进行的，Java会自动对i1、i2进行拆箱操作转化成整型，因此i1在数值上等于i2+i3。

4.i4和i5均是引用类型，在栈中存储指针，因为Integer是包装类。但是由于他们各自都是new出来的，因此不再从常量池寻找数据，而是从堆中各自new一个对象，然后各自保存指向对象的指针，所以i4和i5不相等，因为他们所存指针不同，所指向对象不同。

5.这也是一个加法运算，和3同理。

6.d1和d2均是引用类型，在栈中存储指针，因为Double是包装类。但Double包装类没有实现常量池技术，因此Doubled1=1.0;相当于Double d1=new Double(1.0);，是从堆new一个对象，d2同理。因此d1和d2存放的指针不同，指向的对象不同，所以不相等。

### 小结：
1.以上提到的几种基本类型包装类均实现了**常量池技术**，但他们维护的常量仅仅是【-128至127】这个范围内的常量，如果常量值超过这个范围，就会从堆中创建对象，不再从常量池中取。比如，把上边例子改成Integer i1 = 400; Integer i2 = 400;，很明显超过了127，无法从常量池获取常量，就要从堆中new新的Integer对象，这时i1和i2就不相等了。
2.**String类型也实现了常量池技术**，但是稍微有点不同。String型是先检测常量池中有没有对应字符串，如果有，则取出来；如果没有，则把当前的添加进去。


### Java堆内存(heap memory)的十个要点

1. Java堆内存是操作系统分配给JVM的内存的一部分。

2. 当我们创建对象时，它们存储在Java堆内存中。

3. 为了便于垃圾回收，Java堆空间分成三个区域，分别叫作New Generation, Old Generation或叫作Tenured Generation，还有Perm Space。

4. 你可以通过用JVM的命令行选项 -Xms, -Xmx, -Xmn来调整Java堆空间的大小。不要忘了在大小后面加上”M”或者”G”来表示单位。举个例子，你可以用 -Xmx256m来设置堆内存最大的大小为256MB。（Xms表示起始的堆内存大小，Xmx表示最大的堆内存的大小。另外有一个参数 -Xmn，它表示new generation的大小）

5. 你可以用JConsole或者 Runtime.maxMemory(), Runtime.totalMemory(), Runtime.freeMemory()来查看Java中堆内存的大小。

6. **你可以使用命令“jmap”来获得heap dump，用“jhat”来分析heap dump。**

7. Java堆空间不同于栈空间，栈空间是用来储存调用栈和局部变量的。

8. Java垃圾回收器是用来将死掉的对象(不再使用的对象)所占用的内存回收回来，再释放到Java堆空间中。

9. 当你遇到java.lang.outOfMemoryError时，不要紧张，有时候仅仅增加堆空间就可以了，但如果经常出现的话，就要看看Java程序中是不是存在内存泄露了。

10. 请使用**Profiler和Heap dump分析工具**来查看Java堆空间，可以查看给每个对象分配了多少内存。



http://www.jianshu.com/p/251b1bf72cd8



## JVM基本构成：

![VM基本构成](img/jvm_001.jpeg)
从上图可知，JVM主要包括四个部分：
### 1.类加载器（ClassLoader）
在JVM启动时或者在类运行时将需要的class加载到JVM中。（下图表示了从java源文件到JVM的整个过程，可配合理解。

![类加载器](img/class_loader.jpeg)

### 2.执行引擎
负责执行class文件中包含的字节码指令；

### 3.内存区（也叫运行时数据区）
是在JVM运行的时候操作所分配的内存区。运行时内存区主要可以划分为5个区域，如图：

![内存区](img/memory_run_time.jpeg)
方法区(Method Area)：用于存储类结构信息的地方，包括常量池、静态变量、构造函数等。虽然JVM规范把方法区描述为堆的一个逻辑部分， 但它却有个别名non-heap（非堆），所以大家不要搞混淆了。方法区还包含一个运行时常量池。

java堆(Heap)：存储java实例或者对象的地方。这块是GC的主要区域。从存储的内容我们可以很容易知道，方法区和堆是被所有java线程共享的。

java栈(Stack)：java栈总是和线程关联在一起，每当创建一个线程时，JVM就会为这个线程创建一个对应的java栈。在这个java栈中又会包含多个栈帧，每运行一个方法就创建一个栈帧，用于存储局部变量表、操作栈、方法返回值等。每一个方法从调用直至执行完成的过程，就对应一个栈帧在java栈中入栈到出栈的过程。所以java栈是现成私有的。

程序计数器(PC Register)：用于保存当前线程执行的内存地址。由于JVM程序是多线程执行的（线程轮流切换），所以为了保证线程切换回来后，还能恢复到原先状态，就需要一个独立的计数器，记录之前中断的地方，可见程序计数器也是线程私有的。

本地方法栈(Native Method Stack)：和java栈的作用差不多，只不过是为JVM使用到的native方法服务的。

### 4.本地方法接口
主要是调用C或C++实现的本地方法及返回结果。


## GC机制:
垃圾收集器一般必须完成两件事：检测出垃圾；回收垃圾。怎么检测出垃圾？一般有以下几种方法：
### 引用计数法：
    给一个对象添加引用计数器，每当有个地方引用它，计数器就加1；引用失效就减1。好了，问题来了，如果我有两个对象A和B，互相引用，除此之外，没有其他任何对象引用它们，实际上这两个对象已经无法访问，即是我们说的垃圾对象。但是互相引用，计数不为0，导致无法回收，所以还有另一种方法：
### 可达性分析算法：
    以根集对象为起始点进行搜索，如果有对象不可达的话，即是垃圾对象。这里的根集一般包括java栈中引用的对象、方法区常量池中引用的对象、本地方法中引用的对象等。
    总之，JVM在做垃圾回收的时候，会检查堆中的所有对象是否会被这些根集对象引用，不能够被引用的对象就会被垃圾收集器回收。

一般回收算法也有如下几种：
1).标记-清除（Mark-sweep）
2).复制（Copying
3).标记-整理（Mark-Compact）
4).分代收集算法
http://blog.csdn.net/tonytfjing/article/details/44278233

## 类的加载器，双亲机制，Android的类加载器。

### 类的加载器

    大家都知道，当我们写好一个Java程序之后，不是管是CS还是BS应用，都是由若干个.class文件组织而成的一个完整的Java应用程序，当程序在运行时，即会调用该程序的一个入口函数来调用系统的相关功能，而这些功能都被封装在不同的class文件当中，所以经常要从这个class文件中要调用另外一个class文件中的方法，如果另外一个文件不存在的，则会引发系统异常。而程序在启动的时候，并不会一次性加载程序所要用的所有class文件，而是根据程序的需要，通过Java的类加载机制（ClassLoader）来动态加载某个class文件到内存当中的，从而只有class文件被载入到了内存之后，才能被其它class所引用。所以ClassLoader就是用来动态加载class文件到内存当中用的。

### 双亲机制
#### 1、原理介绍
    ClassLoader使用的是双亲委托模型来搜索类的，每个ClassLoader实例都有一个父类加载器的引用（不是继承的关系，是一个包含的关系），虚拟机内置的类加载器（Bootstrap ClassLoader）本身没有父类加载器，但可以用作其它ClassLoader实例的的父类加载器。当一个ClassLoader实例需要加载某个类时，它会试图亲自搜索某个类之前，先把这个任务委托给它的父类加载器，这个过程是由上至下依次检查的，首先由最顶层的类加载器Bootstrap ClassLoader试图加载，如果没加载到，则把任务转交给Extension ClassLoader试图加载，如果也没加载到，则转交给App ClassLoader 进行加载，如果它也没有加载得到的话，则返回给委托的发起者，由它到指定的文件系统或网络等URL中加载该类。如果它们都没有加载到这个类时，则抛出ClassNotFoundException异常。否则将这个找到的类生成一个类的定义，并将它加载到内存当中，最后返回这个类在内存中的Class实例对象。
#### 2、为什么要使用双亲委托这种模型呢？
    因为这样可以避免重复加载，当父亲已经加载了该类的时候，就没有必要子ClassLoader再加载一次。考虑到安全因素，我们试想一下，如果不使用这种委托模式，那我们就可以随时使用自定义的String来动态替代java核心api中定义的类型，这样会存在非常大的安全隐患，而双亲委托的方式，就可以避免这种情况，因为String已经在启动时就被引导类加载器（Bootstrap ClassLoader）加载，所以用户自定义的ClassLoader永远也无法加载一个自己写的String，除非你改变JDK中ClassLoader搜索类的默认算法。
#### 3、但是JVM在搜索类的时候，又是如何判定两个class是相同的呢？
    JVM在判定两个class是否相同时，不仅要判断两个类名是否相同，而且要判断是否由同一个类加载器实例加载的。只有两者同时满足的情况下，JVM才认为这两个class是相同的。就算两个class是同一份class字节码，如果被两个不同的ClassLoader实例所加载，JVM也会认为它们是两个不同class。比如网络上的一个Java类org.classloader.simple.NetClassLoaderSimple，javac编译之后生成字节码文件NetClassLoaderSimple.class，ClassLoaderA和ClassLoaderB这两个类加载器并读取了NetClassLoaderSimple.class文件，并分别定义出了java.lang.Class实例来表示这个类，对于JVM来说，它们是两个不同的实例对象，但它们确实是同一份字节码文件，如果试图将这个Class实例生成具体的对象进行转换时，就会抛运行时异常java.lang.ClassCaseException，提示这是两个不同的类型。

### Android类加载器
    对于Android而言，最终的apk文件包含的是dex类型的文件，dex文件是将class文件重新打包，打包的规则又不是简单地压缩，而是完全对class文件内部的各种函数表，变量表进行优化，产生一个新的文件，即dex文件。因此加载这种特殊的Class文件就需要特殊的类加载器DexClassLoader。


## 集合框架，list，map，set都有哪些具体的实现类，区别都是什么?
1.List,Set都是继承自Collection接口，Map则不是;

2.List特点：元素有放入顺序，元素可重复;
Set特点：元素无放入顺序，元素不可重复，重复元素会覆盖掉，（注意：元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的，加入Set 的Object必须定义equals()方法;
另外list支持for循环，也就是通过下标来遍历，也可以用迭代器，但是set只能用迭代，因为他无序，无法用下标来取得想要的值）。

3.Set和List对比：
Set：检索元素效率低下，删除和插入效率高，插入和删除不会引起元素位置改变。
List：和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低，因为会引起其他元素位置改变。

4.Map适合储存键值对的数据。

5.线程安全集合类与非线程安全集合类
LinkedList、ArrayList、HashSet是非线程安全的，Vector是线程安全的;
HashMap是非线程安全的，HashTable是线程安全的;
StringBuilder是非线程安全的，StringBuffer是线程安全的。

下面是这些类具体的使用介绍：
**ArrayList与LinkedList的区别和适用场景**
**Arraylist：**
优点：ArrayList是实现了基于动态数组的数据结构,因为地址连续，一旦数据存储好了，查询操作效率会比较高（在内存里是连着放的）。
缺点：因为地址连续， ArrayList要移动数据,所以插入和删除操作效率比较低。

**LinkedList：**

优点：LinkedList基于链表的数据结构,地址是任意的，所以在开辟内存空间的时候不需要等一个连续的地址，对于新增和删除操作add和remove，LinedList比较占优势。LinkedList 适用于要头尾操作或插入指定位置的场景。
缺点：因为LinkedList要移动指针,所以查询操作性能比较低。

适用场景分析：
当需要对数据进行访问的情况下选用ArrayList，当需要对数据进行多次增加删除修改时采用LinkedList。


**ArrayList和Vector都是用数组实现的，主要有这么三个区别：**

1).Vector是多线程安全的，线程安全就是说多线程访问同一代码，不会产生不确定的结果。而ArrayList不是，这个可以从源码中看出，Vector类中的方法很多有synchronized进行修饰，这样就导致了Vector在效率上无法与ArrayList相比；

2).两个都是采用的线性连续空间存储元素，但是当空间不足的时候，两个类的增加方式是不同。

3).Vector可以设置增长因子，而ArrayList不可以。

4).Vector是一种老的动态数组，是线程同步的，效率很低，一般不赞成使用。

适用场景：
1.Vector是线程同步的，所以它也是线程安全的，而ArrayList是线程异步的，是不安全的。如果不考虑到线程的安全因素，一般用ArrayList效率比较高。
2.如果集合中的元素的数目大于目前集合数组的长度时，在集合中使用数据量比较大的数据，用Vector有一定的优势。


**HashSet与Treeset的适用场景**
1.TreeSet 是二叉树（红黑树的树据结构）实现的,Treeset中的数据是自动排好序的，不允许放入null值。

2.HashSet 是哈希表实现的,HashSet中的数据是无序的，可以放入null，但只能放入一个null，两者中的值都不能重复，就如数据库中唯一约束。

3.HashSet要求放入的对象必须实现HashCode()方法，放入的对象，是以hashcode码作为标识的，而具有相同内容的String对象，hashcode是一样，所以放入的内容不能重复。但是同一个类的对象可以放入不同的实例。

适用场景分析:
HashSet是基于Hash算法实现的，其性能通常都优于TreeSet。为快速查找而设计的Set，我们通常都应该使用HashSet，在我们需要排序的功能时，我们才使用TreeSet。

**HashMap与TreeMap、HashTable的区别及适用场景**
HashMap 非线程安全 
HashMap：基于哈希表(散列表)实现。使用HashMap要求添加的键类明确定义了hashCode()和equals()[可以重写hashCode()和equals()]，为了优化HashMap空间的使用，您可以调优初始容量和负载因子。其中散列表的冲突处理主要分两种，一种是开放定址法，另一种是链表法。HashMap的实现中采用的是链表法。
TreeMap：非线程安全基于红黑树实现。TreeMap没有调优选项，因为该树总处于平衡状态。

适用场景分析：
HashMap和HashTable:HashMap去掉了HashTable的contains方法，但是加上了containsValue()和containsKey()方法。HashTable同步的，而HashMap是非同步的，效率上比HashTable要高。HashMap允许空键值，而HashTable不允许。
HashMap：适用于Map中插入、删除和定位元素。
Treemap：适用于按自然顺序或自定义顺序遍历键(key)。
(ps:其实我们工作的过程中对集合的使用是很频繁的,稍加注意并总结积累一下,在面试的时候应该会回答的很轻松)

## concurrentHashmap原理，原子类。
ConcurrentHashMap作为一种线程安全且高效的哈希表的解决方案，尤其其中的"分段锁"的方案，相比HashTable的全表锁在性能上的提升非常之大.

## volatile原理

    在《Java并发编程：核心理论》一文中，我们已经提到过可见性、有序性及原子性问题，通常情况下我们可以通过Synchronized关键字来解决这些个问题，不过如果对Synchronized原理有了解的话，应该知道Synchronized是一个比较重量级的操作，对系统的性能有比较大的影响，所以，如果有其他解决方案，我们通常都避免使用Synchronized来解决问题。而volatile关键字就是Java中提供的另一种解决可见性和有序性问题的方案。对于原子性，需要强调一点，也是大家容易误解的一点：对volatile变量的单次读/写操作可以保证原子性的，如long和double类型变量，但是并不能保证i++这种操作的原子性，因为本质上i++是读、写两次操作。
参考文章：https://www.cnblogs.com/paddix/p/5428507.html


## 七. 多线程的使用场景
使用多线程就一定效率高吗？ 有时候使用多线程并不是为了提高效率，而是使得CPU能够同时处理多个事件。

1).为了不阻塞主线程,启动其他线程来做耗时的事情,比如APP中耗时操作都不在UI中做.

2).实现更快的应用程序,即主线程专门监听用户请求,子线程用来处理用户请求,以获得大的吞吐量.感觉这种情况下，多线程的效率未必高。 这种情况下的多线程是为了不必等待， 可以并行处理多条数据。
比如JavaWeb的就是主线程专门监听用户的HTTP请求，然后启动子线程去处理用户的HTTP请求。

3).某种虽然优先级很低的服务，但是却要不定时去做。
比如Jvm的垃圾回收。

4.)某种任务，虽然耗时，但是不耗CPU的操作时，开启多个线程，效率会有显著提高。
比如读取文件，然后处理。 磁盘IO是个很耗费时间，但是不耗CPU计算的工作。 所以可以一个线程读取数据，一个线程处理数据。肯定比一个线程读取数据，然后处理效率高。 因为两个线程的时候充分利用了CPU等待磁盘IO的空闲时间。

## 八. JAVA常量池

Interger中的128(-128~127)
a.当数值范围为-128~127时：如果两个new出来Integer对象，即使值相同，通过“==”比较结果为false，但两个对象直接赋值，则通过“==”比较结果为“true，这一点与String非常相似。

b.当数值不在-128~127时，无论通过哪种方式，即使两个对象的值相等，通过“==”比较，其结果为false；
c.当一个Integer对象直接与一个int基本数据类型通过“==”比较，其结果与第一点相同；
d.Integer对象的hash值为数值本身；

为什么是-128-127?
    在Integer类中有一个静态内部类**IntegerCache**，在IntegerCache类中有一个Integer数组，用以缓存当数值范围为-128~127时的Integer对象。

九. 简单介绍一下java中的泛型，泛型擦除以及相关的概念。

    泛型是Java SE 1.5的新特性，泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。这种参数类型可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法。 Java语言引入泛型的好处是安全简单。
    在Java SE 1.5之前，没有泛型的情况的下，通过对类型Object的引用来实现参数的“任意化”，“任意化”带来的缺点是要做显式的强制类型转换，而这种转换是要求开发者对实际参数类型可以预知的情况下进行的。对于强制类型转换错误的情况，编译器可能不提示错误，在运行的时候才出现异常，这是一个安全隐患。
    泛型的好处是在编译的时候检查类型安全，并且所有的强制转换都是自动和隐式的，提高代码的重用率。
1、泛型的类型参数只能是类类型（包括自定义类），不能是简单类型。
2、同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。
3、泛型的类型参数可以有多个。
4、泛型的参数类型可以使用extends语句，例如<T extends superclass>。习惯上称为“有界类型”。
5、泛型的参数类型还可以是通配符类型。例如Class<?> classType = Class.forName("java.lang.String");

泛型擦除以及相关的概念
    Java中的泛型基本上都是在编译器这个层次来实现的。在生成的Java字节码中是不包含泛型中的类型信息的。使用泛型的时候加上的类型参数，会在编译器在编译的时候去掉。这个过程就称为类型擦除。

类型擦除引起的问题及解决方法
1、先检查，在编译，以及检查编译的对象和引用传递的问题
2、自动类型转换
3、类型擦除与多态的冲突和解决方法
4、泛型类型变量不能是基本数据类型
5、运行时类型查询
6、异常中使用泛型的问题
7、数组（这个不属于类型擦除引起的问题）
9、类型擦除后的冲突
10、泛型在静态方法和静态类中的问题


