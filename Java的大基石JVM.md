# Java的大基石JVM

## 一、JVM体系结构

JVM运行在操作系统之上，它与硬件没有直接的交互。

![JVM体系结构概览](https://raw.githubusercontent.com/daffupman/markdown-img/master/20191015134605.png)



### 1、类加载器（Class Loader）

类加载是一个将class字节码实例化成Class对象并进行相关初始化的过程。

而类加载器是负责加载class文件，class文件在文件开头有 ==特定的文件标识（cafe babe）== 将class文件字节码加载进内存中，并将这些内容转换为方法区中的运行时数据结构，并且Class Loader只负责class文件的加载，至于它是否可以运行，则由Executing Engine决定。

类加载器分为：

- 启动类加载器（BootStrap Class Loader）：由C++编写，使用Java代码获取时显示为null。

- 扩展类加载器（Extension Class Loader）：jdk9中为Platform Class Loader

  > sun.misc.Launcher\$ExtClassLoader@6eObe858

- 应用程序类加载器（Application Class Loader）：应用程序类加载器，加载当前应用的classpath的所有类。

  > sun.misc.Launcher\$AppClassLoader@l4dad5dc

- 用户自定义加载器：用户编写继承于 ==java.lang.ClassLoader== 的类。

  ```java
  public class MyClassLoader extends ClassLoader {
      private String path;
      private String classLoaderName;
      
      public MyClassLoader(String path, String classLoaderName) {
          this.path = path;
          this.classLoaderName = classLoaderName;
      }
      
      // 用于寻找类文件
      @Override
      public class findClass(String name) {
          byte[] b = loadClassData(name);
          return defineClass(name, b, 0, b.length);
      }
      
      // 用于加载类文件
      private byte[] loadClassData(String name) {
          name = path + name + ".class";
          InputStream in = null;
          ByteArrayOutputStream out = null;
          try {
              in = new FileInputStream(new File(name));
              out = new ByteArrayOutputStream();
              int i = 0;
              while((i = in.read()) != -1) {
                  out.write(i);
              }
          } catch(Exception e) {
              e.printStackTrace();
          } finally {
              try {
                  out.close();
                  in.close();
              } catch(Execption e) {
                  e.printStackTrace();
              }
          }
          return out.toByteArray();
      }
  }
  ```

  

#### 1.1、==双亲委派== :star: 

当一个类收到了类加载请求，它不会先去尝试自己加载这个类，而是把这个请求委派给父加载器去完成，每一个层次加载器都是如此。因此所有的加载请求最后都会送到 ==启动类加载器中== 。只有当父类加载器反馈自己无法完成这个请求，子类加载器才会去尝试自己去加载。

采用双亲委派的好处就是保证了使用不同的类加载器最终得到的都是一个对象。

![双亲委派模型](C:\Users\daffupman\AppData\Roaming\Typora\typora-user-images\1571225231231.png)

### 2、执行引擎（Executing Engine）

负责解释命令，提交给操作系统执行。

### 3、本地接口（Native Interface）

为融合C/C++而设立的一块区域，可以与硬件做交互，现在很少使用。

### 4、本地方法栈（Native Method Stack）

用于登记native方法，在Executing Engine执行时加载本地方法库。

### 5、程序计数器（Programming Counter Registry）

每个程序都有一个程序计数器，是线程私有的。程序计数器实际上是一个指针，指向方法区的方法区中的方法字节码，存储着执行引擎将要执行的下一条指令的地址，是当前线程所执行的字节码行号指示器。它所占用的空间特别小，可忽略不计。

### 6、方法区（Method Area）

线程共享的内存运行时内存区域。它==存储着每一个类的结构信息==，如常量池（Runtime Constant Pool）、字段和方法数据、构造函数和普通方法的字节码内容。方法区实际上是一个规范，在不同虚拟机中的实现也不一致，典型的就是永久代和元空间。

> 实例变量在堆内存中，与方法区无关。

### 7、栈（Stack）

栈也叫栈内存，主管Java的程序运行，随线程的创建而创建，随线程的消亡而消亡。八种基本数据类型、对象的引用、实例方法都是在函数的栈内存中分配的。当栈内存使用完后，会抛出==java.lang.StackOverflowError==。

#### 7.1、栈的存储内容

- 本地变量（Local Variables）：出入参及方法内的变量；
- 栈操作（Operand Stack）：记录出栈入栈的操作；
- 栈帧数据（Frame Data）：包括类文件、方法等。

#### 7.2、栈、堆和方法区的交互关系

![1571224254865](C:\Users\daffupman\AppData\Roaming\Typora\typora-user-images\1571224254865.png)

HotSpot是使用指针的方式访问对象：Java堆中会存放类元数据的地址，reference存储的是对象的地址。

## 二、堆体系结构

一个JVM实例只有一个堆内存，堆内存的大小是可以调节的。类加载器读取了类文件后，需要把类、方法、常量、变量放到堆内存中，保存所有应用类型的真实信息，以方便执行，堆内存在逻辑上分为：

![](https://raw.githubusercontent.com/daffupman/markdown-img/master/20191016201304.png)

### 1、新生代

新生区是类的诞生、成长、消亡的区域。一个类在这里产生，应用，最后被垃圾回收器收集，结束生命。

新生区被划分为伊甸区（Eden Space）和幸存区（Survivor Space）。

当伊甸区的空间耗尽时，程序还需要创建对象。JVM将对伊甸区进行==Minor GC==，销毁不再使用的对象。然后将幸存的对象放入Survivor 0区。如果Survivor 0区也满了，那么再进行垃圾回收。将幸存对象放入Survivor 1区，若Survivor 1区也满了，将转移到老年代。老年代满了之后会进行一次==Full GC==，如果仍无法满足新对象的容量，则抛出异常`OutOfMemory`Error 。	

出现 ==java.lang.OutOfMemoryError:Java heap space== 异常说明Java虚拟机的堆内存不足以使用，原因在于：

- Java虚拟机堆内存太小，可以通过 ==-Xms、-Xmx== 来调整；
- 代码中创建了大量大对象，并且长时间不能被垃圾回收器收集（存在被引用）。

> 新生代为什么需要Survivor区？
>
> - 减少被送到老年代的对象，减少Full GC的发生，提高性能。
>
> 新生区为什么需要2个Survivor区？
>
> - 解决内存碎片化。

### 2、MinorGC的过程

Java堆从GC的角度还可以细分为：新生代（Eden区、From Survivor区和To Survivor区）和老年代。

#### 2.1、复制

第一次触发GC时，把存活的对象拷贝到Survivor From区。再次触发GC的时候会扫描==Eden区和From区==，把存活的对象拷贝到To区，然后对象的年龄+1

#### 2.2、清空

清空Eden区和Survivor From区中的对象。

#### 2.3、交换

To区和From区互换。部分对象会在From和To区域中复制来复制去,如此交换15次(由JVM参数MaxTenuringThreshold决定,这个参数默认是15),最终如果还是存活,就存入到老年代。

> GC之后有交换，谁空谁是To。
>
> 如果分配的对象比较大，Eden区放不下但Tenured区可以放得下，该对象直接分配到Tenured区。

#### 2.4、MinorGC和FullGC的范围

![MinorGC和FullGC的范围](https://raw.githubusercontent.com/daffupman/markdown-img/master/20191017111117.png)

### 3、方法区

方法区（Method Area）和堆一样，是各个线程共享的内存区域，存储着虚拟机加载的：类信息、普通常量、静态常量和编译器编译后的代码等。==虽然JVM规范将方法区描述为堆的一个逻辑部分，但它却还有一个别名：非堆（Non-Heap），目的就是要和堆分开。==

在jdk1.7中，方法区的实现是老年代（PermGen），这里也存储着字符串常量池和运行环境所必须的类信息。新生代与老年代共同使用着JVM的堆空间，比例为1：2。

而在jdk1.8，元空间（MetaSpace）替代了老年代，并且直接使用物理内存。之前方法区存储的字符串常量移至堆内存，其他内容包括类元信息、字段、静态属性、方法、常量等都移动至元空间内。

### 4、堆参数调优

通常将-Xms和-Xmx设置成一样的数值，防止内存震荡。

| 参数                | 说明                                        |
| ------------------- | ------------------------------------------- |
| -Xms                | 设置初始分配大小，默认为物理内存的 ==1/64== |
| -Xmx                | 最大分配内存，默认为物理内存的 ==1/4==      |
| -XX:+PrintGCDetails | 输出详细的GC处理日志                        |

```java
Runtime.getRuntime().maxMemory();		// 返回JVM试图使用的最大的内存
Runtime.getRuntime().totalMemory();		// 返回最大内存容量
```

> :warning:注意：==我们经常把Xms和Xmx设置成一样大，这样可以防止内存震荡==。如果内存已经使用到Xmx的设置的值，也是Xmx的值，那么就可以直接触发gc，而不是让堆的容量增加来避免gc。

## 三、GC垃圾回收

### 1、简述

垃圾回收器（Garbage Collection）作用于方法区（Method Area）和堆（Heap）。

GC垃圾回收，采用分代垃圾收集算法：

- 频繁收集新生代
- 较少收集老年代
- 基本不动元空间

### 2、GC算法

JVM在进行GC时并不是对Young区（排除To区）、Old区和MetaSpace一起回收。大部分回收的是Young区，Young区的80%对象都是临时对象，是需要回收的。

#### 2.1、引用计数

对象实例每有一次引用，该实例的引用计数器就 +1 。发生GC时，销毁引用为0的实例。每次对对象赋值时都要维护引用计数器，且计数器本身也有一定消耗，同时也比较难处理循环引用。该算法已经被JVM弃用。

#### 2.2、复制算法（Copying）

HotSpot JVM把新生代分为三个部分：1个Eden区和2个Survivor区（from区和to区），默认占比为8：1：1。通常情况下，新创建的对象都会被分配到Eden区（大对象除外）。

由于==Young区==的对象基本上都是朝生夕死的，所以这里的对象回收将会采用 ==复制算法（Copying）== 。

复制算法的基本思想是将内存分为两块，每次只用其中一块，当这块内存使用完了，就将存活的对象复制到另外一块内存上。复制算法不会产生碎片。

在Young区，复制算法具体是这样的：发生GC时，存在于Eden区和From区（首次GC不包括From区）的对象将复制到To区。对于Eden区的幸存对象直接复制到To区，而From区的幸存对象会根据年龄决定去往的区域。年龄超过15的对象送往Old区，剩下的也复制到To区。清空Eden区和From区（90%的堆空间），然后From区和To区交换角色。Minor GC会一直重复这样的工作，直到To区被填满，会将所有对象移到到Old区。

复制算法没有标记和清除的过程，效率高，而且没有内存碎片，可以利用 ==bump-the-pointer== 实现快速分配。缺点是会浪费一部分空间，如果要复制的对象比较多时，效率会大大下降。

#### 2.3、标记清除（Mark-Sweep）

标记清除将垃圾回收分为两个阶段：标记和清除。

- 标记：从引用根节点开始标记遍历，标记出要回收的对象；
- 清除：遍历整个堆，清除被标记的对象

该算法不要额外的空间。但是缺点也比较明显：

- 暂停整个应用，然后遍历所有对象，效率低；
- GC过后的内存是不连续的，会影响对象插入的效率。

#### 2.4、标记压缩（Mark-Compact）

标记压缩是对标记清除的改进。

标记压缩分为标记和压缩两个阶段。标记过程与标记清除过程的标记一致。而压缩阶段，不是直接对对象做回收，而是通过所有存活对象都向一端移动，然后直接清除边界以外的内存。

标记压缩算法弥补了清除算法当中内存区域分散的缺点。

#### 2.5、四大算法的比较

从内存效率上看：复制算法 > 标记清除算法 > 标记压缩算法

从内存整齐度上看：复制算法 = 标记整理算法 > 标记清除算法

内存利用率上看：标记清除算法 = 标记压缩算法 > 复制算法

## 四、Java内存模型

Java Memory Model是一组规范，需要各个JVM的实现来遵守JMM规范，以便开发者可以利用这些规范，更方便地开发多线程程序。如果没有这样的一个JMM来规范，那么可能经过了不同JVM的不同规则的重排序之后，导致不同的虚拟机上运行的结果不一样。

JMM是工具类和关键字的原理：volatile、synchronized、lock等。如果没有JMM，那么就需要我们自己指定什么时候用内存栅栏等，这很麻烦，有了JMM，让我们只需要用同步工具和关键字就可以开发并发程序。

JMM的三个内容：重排序、可见性和原子性。

### 重排序

重排序可以提高处理速度。发生重排序的三种情况：

- 编译器优化：包括JVM、JIT编译器等
- CPU指令重排：就算编译器不发生重排，CPU也可能对指令进行重排
- 内存的重排序：线程A的修改线程B却看不到，引出可见性问题。

### 可见性

JMM将各个内存抽象成主存和本地内存。所有的变量都存储在主内存中，同时每个线程也有自己独立的工作内存，工作内存中的变量内容是主内存中的拷贝。线程不能直接读写主内存中的变量，而是只能操作自己工作内存中的变量，然后同步到主内存中主内存是多个线程共享的，当线程间不共享工作内存，如果线程间需要通信，必须借助内存中转来完成。所有的共享变量存在于主内存中，每个线程有自己的本地内存，而且线程读写共享数据也是通过本地内存交换的，所有才导致了可见性问题。

#### Happens-Before原则

各个JVM都要遵守的Happens-Before原则：

- 单线程规则
- 锁操作（synchronized和lock）
- volatile变量
- 线程启动
- 线程join
- 传递性
- 中断
- 构造方法
- 工具类的Happens-Before原则
  - 线程安全的容器get一定能看到在此之前的put等存入动作
  - CountDownLatch
  - Semaphore
  - Future
  - 线程池
  - CyclicBarrier

#### volatile关键字

volatile是一种同步机制，比synchronized或lock相关类更轻量，因为使用volatile并不会发生上下文切换等开销很大的行为。如果一个变量被volatile修饰，那么JVM就知道这个变量可能会被并发修改。 但是开销小，相应的能力也小，虽说volatile是用来同步的保护线程安全的，但是volatile做不到synchronized那样的原子保护，volatile仅在有限的场景下才能发挥作用。

volatile的作用：

- volatile保证了可见性。读取一个volatile变量之前，需要先使相应的本地缓存失效，这样就必须到主内存读取最新值，写一个volatile属性会立即刷入到主内存。
- volatile也可禁止重排序：禁止指令重排序优化，可以解决单例双重锁乱序问题。

volatile不适用于a++（不适合依赖于前一次的结果的场景）。适合场景有：

- boolean flag：如果一个共享变量自始至终只被各个线程赋值，而没有其他的操作，那么可以使用volatile来代替synchronize或代替原子变量，因为赋值自身是有原子性的，而volatile又保证了可见性，所以就足以保证线程安全。但是如果赋值是需要依赖之前的结果时，不适用。
- 适合作用刷新之前变量的触发器。

volatile可以看做是轻量版的synchronized：如果一个共享变量自始至终只被各个线程复制，而没有其他的操作，那么就可以用volatile来代替synchronized或原子变量，因为赋值自身是有原子性的，而volatile又保证了可见性，所以就注意保证线程安全。synchronized不仅保证原子性，也可以保证可见性，synchronized代码块之前的代码也同样可见。

#### 总结

- volatile适用于下面场景：某个属性被多线程共享，其他有一个线程修改了此属性，其他线程可以立即得到修改后的值；或者作为触发器，实现轻量级同步。
- volatile属性的读写操作都是无锁的，它不能替代synchronized，因为他没有提供原子性和互斥性。因为无锁，不需要话费时间在获取锁和释放锁上，所以说他是低成本的。
- volatile只能作用于属性，我们用volatile修饰属性，这样编译器就不会对个属性做指令重排序。
- volatile提供可见性，任何一个线程对其的修改将立马对其他线程可见，volatile属性不会被线程缓存，始终从主存中读取。
- volatile提供了happens-before保证，对volatile变量的写入happens-before所有其他线程后续对变量的读操作。
- volatile可以使得long和double的赋值是原子的（本身不是原子的）。

### 原子性

一组操作，要么全部成功，要么全部失败。synchronized可以实现原子性。在Java中，除long和double的基本类型的赋值操作，所有的引用的复制操作，java.concurrent.Atomic包中的所有类中的操作都是原子操作。

> long和double是64位的值，在32位JVM上写值时需要分成两次，64位的需要一次。使用volatile修饰解决，但一般不用考虑。原子操作 + 原子操作 != 原子操作。