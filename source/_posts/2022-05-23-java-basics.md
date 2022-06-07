---
title: Java基础-复习整理01
index_img: https://static.kevinchu.top/blog/assets/img/cover_021.jpeg
date: 2022-05-23 16:41:39
tags:
    - Java
categories:
    - 学习记录
---

## 1.JDK和JRE有什么区别？
JDK（Java Development Kit），Java开发工具包
JRE（Java Runtime Environment），Java运行环境
JDK中包含JRE，JDK中有一个名为jre的目录，里面包含两个文件夹bin和lib，bin就是JVM，lib就是JVM工作所需要的类库。


## 2.==和equals的区别？
1.对于基本类型，==比较的是值；
2.对于引用类型，==比较的是地址；
3.equals不能用于基本类型的比较；
4.如果没有重写equals，equals就相当于==；
5.如果重写了equals方法，equals比较的是对象的内容；


## 3.final关键字的作用？
（1）用来修饰一个引用
 如果引用为基本数据类型，则该引用为常量，该值无法修改；
 如果引用为引用数据类型，比如对象、数组，则该对象、数组本身可以修改，但指向该对象或数组的地址的引用不能修改。
 如果引用时类的成员变量，则必须当场赋值，否则编译会报错。

（2）用来修饰一个方法
当使用final修饰方法时，这个方法将成为最终方法，无法被子类重写。但是，该方法仍然可以被继承。

（3）用来修饰类
当用final修改类时，该类成为最终类，无法被继承。
比如常用的String类就是最终类。


## 4.java中的Math.round(-1.5)等于多少？
Math提供了三个与取整有关的方法：ceil、floor、round。
（1）ceil：向上取整；
Math.ceil(11.3) = 12;
Math.ceil(-11.3) = 11;
（2）floor：向下取整；
Math.floor(11.3) = 11;
Math.floor(-11.3) = -12;
（3）round：四舍五入；
**加0.5然后向下取整。**
Math.round(11.3) = 11;
Math.round(11.8) = 12;
Math.round(-11.3) = -11;
Math.round(-11.8) = -12;
（Math的round方法是四舍五入,如果参数是负数,则往大的数如,Math.round(-1.5)=-1，如果是Math.round(1.5)则结果为2。）


## 5.String属于基础的数据类型吗？
不属于。
Java八种基本数据类型：
1、整型：byte、short、int、long
2、字符型：char
3、浮点型：float、double
4、布尔型：boolean


## 6.String str="i"与 String str=new String(“i”)一样吗？
String str="i"会将起分配到常量池中，常量池中没有重复的元素，如果常量池中存中i，就将i的地址赋给变量，如果没有就创建一个再赋给变量。

String str=new String(“i”)会将对象分配到堆中，即使内存一样，还是会重新创建一个新的对象。

## 7.如何将字符串反转？
将对象封装到stringBuilder中，调用reverse方法反转。
```JAVA
    public static void main(String[] args){
        String str="asdsadqwQT";
        StringBuilder sb=new StringBuilder(str);
        System.out.println(sb.reverse().toString());
    }
```

## 8.String 类的常用方法都有那些？
（1）常见String类的获取功能

length：获取字符串长度；
charAt(int index)：获取指定索引位置的字符；
indexOf(int ch)：返回指定字符在此字符串中第一次出现处的索引；
substring(int start)：从指定位置开始截取字符串,默认到末尾；
substring(int start,int end)：从指定位置开始到指定位置结束截取字符串；

（2）常见String类的判断功能

equals(Object obj)： 比较字符串的内容是否相同,区分大小写；
contains(String str): 判断字符串中是否包含传递进来的字符串；
startsWith(String str): 判断字符串是否以传递进来的字符串开头；
endsWith(String str): 判断字符串是否以传递进来的字符串结尾；
isEmpty(): 判断字符串的内容是否为空串""；

（3）常见String类的转换功能

byte[] getBytes(): 把字符串转换为字节数组；
char[] toCharArray(): 把字符串转换为字符数组；
String valueOf(char[] chs): 把字符数组转成字符串。valueOf可以将任意类型转为字符串；
toLowerCase(): 把字符串转成小写；
toUpperCase(): 把字符串转成大写；
concat(String str): 把字符串拼接；

（4）常见String类的其他常用功能

replace(char old,char new) 将指定字符进行互换
replace(String old,String new) 将指定字符串进行互换
trim() 去除两端空格
int compareTo(String str) 会对照ASCII 码表 从第一个字母进行减法运算 返回的就是这个减法的结果，如果前面几个字母一样会根据两个字符串的长度进行减法运算返回的就是这个减法的结果，如果连个字符串一摸一样 返回的就是0。


## 9.new String("a") + new String("b") 会创建几个对象？
对象1：new StringBuilder()

对象2：new String("a")

对象3：常量池中的"a"

对象4：new String("b")

对象5：常量池中的"b"

深入剖析：StringBuilder中的toString()：

对象6：new String("ab")

强调一下，toString()的调用，在字符串常量池中，没有生成"ab"


## 10.普通类和抽象类有哪些区别？
抽象类不能被实例化；
抽象类可以有抽象方法，只需申明，无须实现；
有抽象方法的类一定是抽象类；
抽象类的子类必须实现抽象类中的所有抽象方法，否则子类仍然是抽象类；
抽象方法不能声明为静态、不能被static、final修饰。


## 11.接口和抽象类有什么区别?
（1）接口

接口使用interface修饰；
接口不能实例化；
类可以实现多个接口；

①java8之前，接口中的方法都是抽象方法，省略了public abstract。②java8之后；接口中可以定义静态方法，静态方法必须有方法体，普通方法没有方法体，需要被实现；

（2）抽象类

抽象类使用abstract修饰；
抽象类不能被实例化；
抽象类只能单继承；
抽象类中可以包含抽象方法和非抽象方法，非抽象方法需要有方法体；
如果一个类继承了抽象类，①如果实现了所有的抽象方法，子类可以不是抽象类；②如果没有实现所有的抽象方法，子类仍然是抽象类。


## 12.java 中 IO 流分为几种？
（1）按流划分，可以分为输入流和输出流；

（2）按单位划分，可以分为字节流和字符流；

字节流：inputStream、outputStream；

字符流：reader、writer


## 13.BIO、NIO、AIO 有什么区别？
（1）同步阻塞BIO

一个连接一个线程。

JDK1.4之前，建立网络连接的时候采用BIO模式，先在启动服务端socket，然后启动客户端socket，对服务端通信，客户端发送请求后，先判断服务端是否有线程响应，如果没有则会一直等待或者遭到拒绝请求，如果有的话会等待请求结束后才继续执行。

（2）同步非阻塞NIO

NIO主要是想解决BIO的大并发问题，BIO是每一个请求分配一个线程，当请求过多时，每个线程占用一定的内存空间，服务器瘫痪了。

JDK1.4开始支持NIO，适用于连接数目多且连接比较短的架构，比如聊天服务器，并发局限于应用中。

一个请求一个线程。

（3）异步非阻塞AIO

一个有效请求一个线程。

JDK1.7开始支持AIO，适用于连接数目多且连接比较长的结构，比如相册服务器，充分调用OS参与并发操作。


## 14.Files的常用方法都有哪些？
exist
createFile
createDirectory
write
read
copy
size
delete
move

## 15.什么是反射？
所谓反射，是java在运行时进行自我观察的能力，通过class、constructor、field、method四个方法获取一个类的各个组成部分。

在Java运行时环境中，对任意一个类，可以知道类有哪些属性和方法。这种动态获取类的信息以及动态调用对象的方法的功能来自于反射机制。

## 16.什么是 java 序列化？什么情况下需要序列化？
序列化就是一种用来处理对象流的机制。将对象的内容流化，将流化后的对象传输于网络之间。

序列化是通过实现serializable接口，该接口没有需要实现的方法，implement Serializable只是为了标注该对象是可被序列化的，使用一个输出流（FileOutputStream）来构造一个ObjectOutputStream对象，接着使用ObjectOutputStream对象的writeObejct（Object object）方法就可以将参数的obj对象到磁盘，需要恢复的时候使用输入流。

序列化是将对象转换为容易传输的格式的过程。

例如，可以序列化一个对象，然后通过HTTP通过Internet在客户端和服务器之间传输该对象。在另一端，反序列化将从流中心构造成对象。

一般程序在运行时，产生对象，这些对象随着程序的停止而消失，但我们想将某些对象保存下来，这时，我们就可以通过序列化将对象保存在磁盘，需要使用的时候通过反序列化获取到。

对象序列化的最主要目的就是传递和保存对象，保存对象的完整性和可传递性。

譬如通过网络传输或者把一个对象保存成本地一个文件的时候，需要使用序列化。


## 17.为什么要使用克隆？如何实现对象克隆？深拷贝和浅拷贝区别是什么？
（1）什么要使用克隆？

想对一个对象进行复制，又想保留原有的对象进行接下来的操作，这个时候就需要克隆了。

（2）如何实现对象克隆？

实现Cloneable接口，重写clone方法；
实现Serializable接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深克隆。
BeanUtils，apache和Spring都提供了bean工具，只是这都是浅克隆。

（3）深拷贝和浅拷贝区别是什么？

浅拷贝：仅仅克隆基本类型变量，不克隆引用类型变量；
深克隆：既克隆基本类型变量，又克隆引用类型变量；


## 18.throw 和 throws 的区别？
（1）throw

作用在方法内，表示抛出具体异常，由方法体内的语句处理；
一定抛出了异常；

（2）throws

作用在方法的声明上，表示抛出异常，由调用者来进行异常处理；
可能出现异常，不一定会发生异常；

## 19.final、finally、finalize 有什么区别？
final可以修饰类，变量，方法，修饰的类不能被继承，修饰的变量不能重新赋值，修饰的方法不能被重写

finally用于抛异常，finally代码块内语句无论是否发生异常，都会在执行finally，常用于一些流的关闭。

finalize方法用于垃圾回收。

一般情况下不需要我们实现finalize，当对象被回收的时候需要释放一些资源，比如socket链接，在对象初始化时创建，整个生命周期内有效，那么需要实现finalize方法，关闭这个链接。

但是当调用finalize方法后，并不意味着gc会立即回收该对象，所以有可能真正调用的时候，对象又不需要回收了，然后到了真正要回收的时候，因为之前调用过一次，这次又不会调用了，产生问题。所以，不推荐使用finalize方法。


## 20.try-catch-finally 中，如果 catch 中 return 了，finally 还会执行吗？
会！

## 21.常见的异常类有哪些？
NullPointerException：空指针异常；
SQLException：数据库相关的异常；
IndexOutOfBoundsException：数组下角标越界异常；
FileNotFoundException：打开文件失败时抛出；
IOException：当发生某种IO异常时抛出；
ClassCastException：当试图将对象强制转换为不是实例的子类时，抛出此异常；
NoSuchMethodException：无法找到某一方法时，抛出；
ArrayStoreException：试图将错误类型的对象存储到一个对象数组时抛出的异常；
NumberFormatException：当试图将字符串转换成数字时，失败了，抛出；
IllegalArgumentException：抛出的异常表明向方法传递了一个不合法或不正确的参数。
ArithmeticException当出现异常的运算条件时，抛出此异常。例如，一个整数“除以零”时，抛出此类的一个实例。 


## 22.hashcode是什么？有什么作用？
Java中Object有一个方法：

public native int hashcode();

（1）hashcode()方法的作用

hashcode()方法主要配合基于散列的集合一起使用，比如HashSet、HashMap、HashTable。

当集合需要添加新的对象时，先调用这个对象的hashcode()方法，得到对应的hashcode值，实际上hashmap中会有一个table保存已经存进去的对象的hashcode值，如果table中没有该hashcode值，则直接存入，如果有，就调用equals方法与新元素进行比较，相同就不存了，不同就存入。

（2）equals和hashcode的关系

如果equals为true，hashcode一定相等； 

如果equals为false，hashcode不一定不相等；

如果hashcode值相等，equals不一定相等；

如果hashcode值不等，equals一定不等；

（3）重写equals方法时，一定要重写hashcode方法


## 23.java 中操作字符串都有哪些类？它们之间有什么区别？
（1）String

String是不可变对象，每次对String类型的改变时都会生成一个新的对象。

（2）StringBuilder

线程不安全，效率高，多用于单线程。

（3）StringBuffer

线程安全，由于加锁的原因，效率不如StringBuilder，多用于多线程。

不频繁的字符串操作使用String，操作频繁的情况不建议使用String。

StringBuilder > StringBuffer > String。


## 24.java 中都有哪些引用类型？
（1）强引用

Java中默认声明的就是强引用，比如：

Object obj = new Object();
obj = null;
只要强引用存在，垃圾回收器将永远不会回收被引用的对象。如果想被回收，可以将对象置为null； 

（2）软引用（SoftReference）

在内存足够的时候，软引用不会被回收，只有在内存不足时，系统才会回收软引用对象，如果回收了软引用对象之后仍然没有足够的内存，才会跑出内存溢出异常。
```JAVA
byte[] buff = new byte[1024 * 1024];
SoftReference<byte[]> sr = new SoftReference<>(buff);
```

（3）弱引用（WeakReference）

进行垃圾回收时，弱引用就会被回收。

（4）虚引用（PhantomReference）

（5）引用队列（ReferenceQueue）

引用队列可以与软引用、弱引用、虚引用一起配合使用。

当垃圾回收器准备回收一个对象时，如果发现它还有引用，就会在回收对象之前，把这个引用加入到引用队列中。

程序可以通过判断引用队列中是否加入了引用，来判断被引用的对象是否将要被垃圾回收，这样可以在对象被回收之前采取一些必要的措施。


## 25.在 Java 中，为什么不允许从静态方法中访问非静态变量？
1.静态变量属于类本身，在类加载的时候就会分配内存，可以通过类名直接访问；
2.非静态变量属于类的对象，只有在类的对象产生时，才会分配内存，通过类的实例去访问；
3.静态方法也属于类本身，但是此时没有类的实例，内存中没有非静态变量，所以无法调用。

## 26.实例化对象有哪几种方式
- new
- clone()
- 通过反射机制创建
- 序列化反序列化

## 27.byte类型127+1等于多少
byte的范围是-128~127。

字节长度为8位，最左边的是符号位，而127的二进制为01111111，所以执行+1操作时，01111111变为10000000。

大家知道，计算机中存储负数，存的是补码的兴衰。左边第一位为符号位。

那么负数的补码转换成十进制如下：

一个数如果为正，则它的原码、反码、补码相同；一个正数的补码，将其转化为十进制，可以直接转换。

已知一个负数的补码，将其转换为十进制数，步骤如下：

先对各位取反；
将其转换为十进制数；
加上负号，再减去1；
例如10000000，最高位是1，是负数，①对各位取反得01111111，转换为十进制就是127，加上负号得-127，再减去1得-128；


## 27.Java 容器都有哪些？
（1）Collection

① set

HashSet、TreeSet

② list

ArrayList、LinkedList、Vector

（2）Map

HashMap、HashTable、TreeMap


## 28.Collection 和 Collections 有什么区别？
（1）Collection是最基本的集合接口，Collection派生了两个子接口list和set，分别定义了两种不同的存储方式。

（2）Collections是一个包装类，它包含各种有关集合操作的静态方法（对集合的搜索、排序、线程安全化等）。

此类不能实例化，就像一个工具类，服务于Collection框架。


## 29.list与Set区别
（1）List简介

实际上有两种List：一种是基本的ArrayList,其优点在于随机访问元素，另一种是LinkedList,它并不是为快速随机访问设计的，而是快速的插入或删除。
ArrayList：由数组实现的List。允许对元素进行快速随机访问，但是向List中间插入与移除元素的速度很慢。
LinkedList ：对顺序访问进行了优化，向List中间插入与删除的开销并不大。随机访问则相对较慢。
还具有下列方 法：addFirst(), addLast(), getFirst(), getLast(), removeFirst() 和 removeLast(), 这些方法 (没有在任何接口或基类中定义过)使得LinkedList可以当作堆栈、队列和双向队列使用。

（2）Set简介

Set具有与Collection完全一样的接口，因此没有任何额外的功能。实际上Set就是Collection,只是行为不同。这是继承与多态思想的典型应用：表现不同的行为。Set不保存重复的元素(至于如何判断元素相同则较为负责) 

Set : 存入Set的每个元素都必须是唯一的，因为Set不保存重复元素。加入Set的元素必须定义equals()方法以确保对象的唯一性。Set与Collection有完全一样的接口。Set接口不保证维护元素的次序。 
HashSet：为快速查找设计的Set。存入HashSet的对象必须定义hashCode()。 
TreeSet： 保存次序的Set, 底层为树结构。使用它可以从Set中提取有序的序列。 

（3）list与Set区别

① List,Set都是继承自Collection接口

② List特点：元素有放入顺序，元素可重复 ，Set特点：元素无放入顺序，元素不可重复，重复元素会覆盖掉，（元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的，加入Set 的Object必须定义equals()方法 ，另外list支持for循环，也就是通过下标来遍历，也可以用迭代器，但是set只能用迭代，因为他无序，无法用下标来取得想要的值。） 

③ Set和List对比： 

Set：检索元素效率低下，删除和插入效率高，插入和删除不会引起元素位置改变。 
List：和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低，因为会引起其他元素位置改变。


## 30.HashMap 和 Hashtable 有什么区别？
HashMap是线程不安全的，HashTable是线程安全的；
HashMap中允许键和值为null，HashTable不允许；
HashMap的默认容器是16，为2倍扩容，HashTable默认是11，为2倍+1扩容；


## 31.说一下 HashMap 的实现原理？
（1）简介

HashMap基于map接口，元素以键值对方式存储，允许有null值，HashMap是线程不安全的。

（2）基本属性

初始化大小，默认16，2倍扩容；
负载因子0.75；
初始化的默认数组；
size
threshold。判断是否需要调整hashmap容量

（3）HashMap的存储结构

JDK1.7中采用数组+链表的存储形式。

HashMap采取Entry数组来存储key-value，每一个键值对组成了一个Entry实体，Entry类时机上是一个单向的链表结构，它具有next指针，指向下一个Entry实体，以此来解决Hash冲突的问题。

HashMap实现一个内部类Entry，重要的属性有hash、key、value、next。


## 32.ArrayList 和 LinkedList 的区别是什么？
ArrayList是动态数组的数据结构实现，查找和遍历的效率较高；

LinkedList 是双向链表的数据结构，增加和删除的效率较高；

## 33. 如何实现数组和 List 之间的转换？
```JAVA
String[] arr = {"zs","ls","ww"};
List<String> list = Arrays.asList(arr);
System.out.println(list);
 
ArrayList<String> list1 = new ArrayList<String>();
list1.add("张三");
list1.add("李四");
list1.add("王五");
String[] arr1 = list1.toArray(new String[list1.size()]);
System.out.println(arr1);
for(int i = 0; i < arr1.length; i++){
    System.out.println(arr1[i]);
}
```

## 34.在 Queue 中 poll()和 remove()有什么区别？
（1）offer()和add()区别：

增加新项时，如果队列满了，add会抛出异常，offer返回false。

（2）poll()和remove()区别：

poll()和remove()都是从队列中删除第一个元素，remove抛出异常，poll返回null。

（3）peek()和element（）区别：

peek()和element（）用于查询队列头部元素，为空时element抛出异常，peek返回null。

## 35.哪些集合类是线程安全的
Vector：就比Arraylist多了个同步化机制（线程安全）。
Stack：栈，也是线程安全的，继承于Vector。
Hashtable：就比Hashmap多了个线程安全。
ConcurrentHashMap:是一种高效但是线程安全的集合。
