
## 一、运行时数据区域
 ![jvm数据区域](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/jvm%E6%95%B0%E6%8D%AE%E5%8C%BA%E5%9F%9F%20.png)

### 程序计数器
**程序计数器是一块较小的内存空间**，可以看做是**当前线程所执行的字节码的行号指示器**。 字节码解释器工作时就是**通过改变这个计数器的值来选取下一跳需要执行的字节码指令**，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个技术器完成

由于java虚拟机多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，任何一个时刻，一个处理器（多核处理器是一个内核）都只会执行一条线程中的指令。因此为了线程切换后能恢复到正确的执行位置，**每条线程有一个独立的程序计数器**。

如果**线程执行的是一个java方法，计数器记录的是正在执行的虚拟机字节码指令的地址;如果执行的是Native方法，计数器值为空**。

此内存区域是**唯一一个在java虚拟机规范中没有规定任何OutOfMemoryError情况的区域**。

### Java虚拟机栈
每个**方法在执行的同时会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息**。从方法调用直至执行完成的过程，对应着一个栈帧在 Java 虚拟机栈中入栈和出栈的过程。  

![java虚拟机栈](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/java%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%A0%88.png)

可以通过 -Xss 这个虚拟机参数来指定每个线程的 Java 虚拟机栈内存大小：，

```
java -Xss512M HackTheJava
```

局部变量表存放了编译期可知的**各种基本数据类型**、**对象引用**(不是对象本身，可能是一个指向对象的指针)和**returnAddress类型**(指向了一条字节码指令的地址)。  **局部变量表所需要的内存空间在编译期间完成分配，并且完全确定不会改变**。 64位长度的long和double占用两个局部变量空间，其余数据类型只占用1个。

**java虚拟机栈也是线程私有的，生命周期与线程相同**


java虚拟机栈可能抛出以下异常：
- 当线程请求的栈深度超过最大值，会抛出 StackOverflowError 异常；
- 栈进行动态扩展时如果无法申请到足够内存，会抛出 OutOfMemoryError 异常。

### 本地方法栈
本地方法栈与Java虚拟机栈类似，它们之间的区别只不过是虚拟机栈为虚拟机执行java方法服务，而**本地方法栈为虚拟机使用到的Native方法服务**。

本地方法一般是用其它语言（C、C++ 或汇编语言等）编写的，并且被编译为基于本机硬件和操作系统的程序，对待这些方法需要特别处理。

![本地方法栈](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E6%9C%AC%E5%9C%B0%E6%96%B9%E6%B3%95%E6%A0%88.png)

与虚拟机栈一样，本地方法栈区域也会抛出StackOverflowError和 OutOfMemoryError异常
### Java堆
Java堆是**被所有线程共享**的一块内存区域，在虚拟机启动时创建。唯一作用就是**用来存放对象实例**。

Java堆是垃圾收集器管理的主要区域，因此也称为“GC堆”。现代的垃圾收集器基本都是采用分代收集算法，其主要的思想是针对不同类型的对象采取不同的垃圾回收算法。可以将堆分成两块：

- 新生代（Young Generation）
- 老年代（Old Generation）

java堆可以处于物理上不连续的内存空间，只要逻辑上是连续的即可。

java堆并且可以动态增加其内存，如果在堆中没有内存完成实例分配，并且堆也无法再扩展时会抛出OutOfMemoryError异常。

可以通过 -Xms 和 -Xmx 这两个虚拟机参数来指定一个程序的堆内存大小，第一个参数设置初始值，第二个参数设置最大值。
```
java -Xms1M -Xmx2M HackTheJava
```
### 方法区
方法区与java堆一样，是**各个线程共享**的内存区域，用于**存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据**。

方法区和堆一样不需要连续的内存，并且可以动态扩展，动态扩展失败一样会抛出OutOfMemoryError异常。

对这块区域进行垃圾回收的主要目标是对常量池的回收和对类的卸载，但是一般比较难实现。

HotSpot 虚拟机把它当成永久代来进行垃圾回收。但很难确定永久代的大小，因为它受到很多因素影响，并且每次 Full GC 之后永久代的大小都会改变，所以经常会抛出 OutOfMemoryError 异常。为了更容易管理方法区，**从 JDK 1.8 开始，移除永久代，并把方法区移至元空间，它位于本地内存中，而不是虚拟机内存中。**

方法区是一个 JVM 规范，永久代与元空间都是其一种实现方式。在 JDK 1.8 之后，原来永久代的数据被分到了堆和元空间中。元空间存储类的元信息，静态变量和常量池等放入堆中。
### 运行时常量池
**运行时常量池是方法区的一部分，Class文件中的常量池（编译器生成的字面量和符号引用）会在类加载后被放入这个区域**

运行时常量池相对于Class文件常量池还具备动态性，允许运行期间将新的常量放入池中，例如 String 类的 intern()方法。

既然是运行时常量池是方法区的一部分，自然会受到方法区内存的限制，当常量池无法再申请到内存时抛出OutOfMemoryError异常
### 直接内存
直接内存并不是虚拟机运行时数据区的一部分。但是这部分内存被频繁使用，而且也可能导致OutOfMemoryError异常。


在 JDK 1.4 中新引入了 NIO 类，引入了一种基于通道与缓冲区的I/O方式，它可以使用 Native 函数库直接分配堆外内存，然后通过 Java 堆里的 DirectByteBuffer 对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在堆内存和堆外内存来回拷贝数据。

## 二、垃圾收集
垃圾收集主要是针对堆和方法区进行。程序计数器、虚拟机栈和本地方法栈这三个区域属于线程私有的，只存在于线程的生命周期内，线程结束之后就会消失，因此不需要对这三个区域进行垃圾回收。

### 判断对象是否要被回收
#### 1.引用计数算法

为对象添加一个引用计数器，当对象增加一个引用时计数器加 1，引用失效时计数器减 1。引用计数为 0 的对象可被回收。

在两个对象出现循环引用的情况下，此时引用计数器永远不为 0，导致无法对它们进行回收。正是因为循环引用的存在，因此 Java 虚拟机不使用引用计数算法。

```java
public class Test {

    public Object instance = null;
    private static final int _1MB = 1024*1024;
    //用来占内存
    private byte[] bigSize = new byte[2*_1MB];
    public static void main(String[] args) {
        Test a = new Test();
        Test b = new Test();
        a.instance = b;
        b.instance = a;
        a = null;
        b = null;
        //在这行发生GC
        System.gc();
    }
}
```

命令行使用
```
javac Test.java  //编译
java -XX:+PrintGCDetails Test  //运行 -XX:+PrintGCDetails表示输出GC详细日志
```
![计数算法](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E8%AE%A1%E6%95%B0%E7%AE%97%E6%B3%95.png)

在上述代码中，a 与 b 引用的对象实例互相持有了对象的引用，因此当我们把对 a 对象与 b 对象的引用去除之后，由于两个对象还存在互相之间的引用，导致两个 Test 对象无法被回收。但实验结果显示，虚拟机并没有因为这两个对相关相互引用就不回收它们，因此**虚拟机并不是通过引用计数算法来判断对象是否存活**。

#### 2.可达性分析算法

算法思想是：以 GC Roots的对象为起始点向下进行搜索，可达的对象都是存活的，不可达的对象可被回收。如下图所示,对象object5、object6、object7虽然互相关联，但是它们到GC Roots是不可达的，因此会被判定为可回收对象
![可达性分析](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E5%8F%AF%E8%BE%BE%E6%80%A7%E5%88%86%E6%9E%90%E7%AE%97%E6%B3%95.png)

Java语言中，可作为GC Roots的对象包括下面几种：

- 虚拟机栈中局部变量表中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中的常量引用的对象
- 本地方法栈中 JNI 中引用的对象

值得注意的是：**一个对象真正的死亡，至少要经历两次标记过程**：如果对象在进行可达性分析后发现GC不可达，它将会被第一次标记并且进行一次筛选，**筛选条件是此对象是否有必要执行finalize()方法**。
- 如果认为没有必要执行finalize()方法，即此对象没有覆盖finalize()方法，或者finalize()方法已经被虚拟机调用过，则虚拟机将这两种情况视为没有必要执行即进行垃圾收集。

- 如果认为有必要执行finalize()方法，则这个对象会被放置在一个叫做F-Queue的队列之中，稍后由一个虚拟机自动建立的、优先级低的Finalizer线程去执行它。虚拟机触发这个方法，但并不等待它运行结束，如果对象在finalize方法中重新与引用链上的任何一个兑现建立关联(比如把自己this关键字赋值给某个类变量或者对象的成员变量)则可以进行自救，否则他就真的被回收了。

```java
package com.company.test;

/**
 * 第一次自救是因为调用了finalize()方法
 * 第二次无法自救是因为一个对象的finalize()方法最多只会被系统自动调用一次
 */
public class FinalizeEscapeGC {
    public static FinalizeEscapeGC SAVE_HOOK = null;
    public void isAlive(){
        System.out.println("yes, i am still alive");
    }

    @Override
    protected void finalize() throws Throwable {
        super.finalize();
        System.out.println("finalize method executed");
        FinalizeEscapeGC.SAVE_HOOK = this;  //重新关联
    }

    public static void main(String[] args) throws InterruptedException {
        SAVE_HOOK = new FinalizeEscapeGC();
        SAVE_HOOK = null;
        System.gc();
        Thread.sleep(500);
        if(SAVE_HOOK != null){
            SAVE_HOOK.isAlive();
        }else{
            System.out.println("No i'm dead");
        }

        //与上面代码完全相同
        SAVE_HOOK = null;
        System.gc();
        Thread.sleep(500);
        if(SAVE_HOOK != null){
            SAVE_HOOK.isAlive();
        }else{
            System.out.println("No i'm dead");
        }
    }
}

```
运行结果：
```
finalize method executed
yes, i am still alive
No i'm dead
```

finalize()方法是java刚诞生时为使C/C++程序员更容易接受它所作出的妥协。它的运行代价高昂，不确定性大无法保证各个对象调用顺序。finalize()能做的所有工作使用try-finally或者其他方式可以做的更好，可以忽略这个方法的存在。

#### 引用类型
无论是通过引用计数算法判断对象的引用数量，还是通过可达性分析算法判断对象的引用链是否可达，判定对象是否存活都与引用有关。

JDK1.2之后，java对引用的概念进行了扩充分为了强引用、软引用、弱引用、虚引用4种，这4中引用程度依次逐渐减弱。
- **强引用**
被强引用关联的对象不会被回收。

使用 new 一个新对象的方式来创建强引用。
```java
Object obj = new Object();
```
- **软引用**
被软引用关联的对象只有在内存不够的情况下才会被回收。如果这次回收内存依然不够的话，则会抛出OutOfMemoryError异常

使用 SoftReference 类来创建软引用。
```java
Object obj = new Object();
SoftReference<Object> sf = new SoftReference<Object>(obj);
obj = null;  // 使对象只被软引用关联
```
- **弱引用**
被弱引用关联的对象一定会被回收，也就是说它只能存活到下一次垃圾回收发生之前。

使用 WeakReference 类来创建弱引用。
```java
Object obj = new Object();
WeakReference<Object> wf = new WeakReference<Object>(obj);
obj = null;
```
- **虚引用**
又称为幽灵引用或者幻影引用，一个对象是否有虚引用的存在，不会对其生存时间造成影响，也无法通过虚引用得到一个对象。为一个对象设置虚引用的唯一目的是能在这个对象被回收时收到一个系统通知。

使用 PhantomReference 来创建虚引用。
```java
Object obj = new Object();
PhantomReference<Object> pf = new PhantomReference<Object>(obj, null);
obj = null;
```

#### 方法区的回收
因为方法区主要存放永久代对象，而永久代对象的回收率比新生代低很多，所以在方法区上进行回收性价比不高。

**永久代的垃圾收集主要是废弃常量的回收和对无用类的卸载**。

回收废弃常量与回收java堆中的对象非常类似。例如字符串"abc"进入常量池，但是没有任何String对象引用了常量池中的"abc",如果这时发生内存回收，而且有必要的话，"abc"就会被清理。

无用类的卸载条件很多，需要满足以下三个条件，并且满足了条件也不一定会被卸载：

- 该类所有的实例都已经被回收，此时堆中不存在该类的任何实例。
- 加载该类的 ClassLoader 已经被回收。
- 该类对应的 Class 对象没有在任何地方被引用，也就无法在任何地方通过反射访问该类方法。

为了避免内存溢出，在大量使用反射和动态代理的场景都需要虚拟机具备类卸载功能。
### 垃圾收集算法
#### 1.标记-清除算法
![标记清除算法]()  

标记-清除算法分为两个阶段：
在标记阶段，程序会检查每个对象是否为活动对象，如果是活动对象，则程序会在对象头部打上标记。  

在清除阶段，会进行对象回收并取消标志位，另外，还会判断回收后的分块与前一个空闲分块是否连续，若连续，会合并这两个分块。回收对象就是把对象作为分块，连接到被称为 “空闲链表” 的单向链表，之后进行分配时只需要遍历这个空闲链表，就可以找到分块。

在分配时，程序会搜索空闲链表寻找空间大于等于新对象大小 size 的块 block。如果它找到的块等于 size，会直接返回这个分块；如果找到的块大于 size，会将块分割成大小为 size 与 (block - size) 的两部分，返回大小为 size 的分块，并把大小为 (block - size) 的块返回给空闲链表。

不足：
- 标记和清除过程效率都不高；
- 会产生大量不连续的内存碎片，导致无法给大对象分配内存，从而不得不提前触发另一次垃圾收集动作。

#### 2.复制算法
![复制算法]()

它将可用内存按容量划分为大小相等的两块，每次只使用其中一块。当着一块的内存用完了，就将还存活的对象复制到另一块上面，然后再把已使用过的内存空间一次清理掉。

优点是由于每次只需要对整个半区进行内存回收，内存分配时就不用考虑内存碎片等问题，直接按顺序分配内存即可，实现简单，运行高效。

主要不足是只使用了内存的一半。

现在的商业虚拟机都采用这种收集算法回收新生代，但是并不是划分为大小相等的两块，而是一块较大的 Eden 空间和两块较小的 Survivor 空间，每次使用 Eden 和其中一块 Survivor。在回收时，将 Eden 和 Survivor 中还存活着的对象全部复制到另一块 Survivor 上，最后清理 Eden 和使用过的那一块 Survivor。

HotSpot 虚拟机的 Eden 和 Survivor 大小比例默认为 8:1，保证了内存的利用率达到 90%。如果每次回收有多于 10% 的对象存活，那么一块 Survivor 就不够用了，此时需要依赖于老年代进行空间分配担保，也就是借用老年代的空间存储放不下的对象。

#### 3.标记-整理算法
![标记整理算法]()
标记过程与标记-清除算法一样，但是后续不是直接对可回收对象进行清理而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。

优点:不会产生内存碎片

不足: 需要移动大量对象，处理效率比较低。

#### 4.分代收集算法
现在的商业虚拟机采用分代收集算法，它根据对象存活周期将内存划分为几块，不同块采用适当的收集算法。

一般将堆分为新生代和老年代。

- 新生代使用：复制算法
- 老年代使用：标记 - 清除 或者 标记 - 整理 算法
### 垃圾收集器
#### 1.Serial收集器
#### 2.ParNew收集器
#### 3.Parallel Scavenge收集器
#### 4.Serial Old收集器
#### 5.Parallel Old收集器
#### 6.CMS收集器
#### 7.G1收集器
## 三、内存分配与回收策略

## 四、类加载机制
