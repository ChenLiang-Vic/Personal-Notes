
# 一、运行时数据区域
 <div align=center>![jvm数据区域](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/jvm%E6%95%B0%E6%8D%AE%E5%8C%BA%E5%9F%9F%20.png)</div>

## 程序计数器
**程序计数器是一块较小的内存空间**，可以看做是**当前线程所执行的字节码的行号指示器**。 字节码解释器工作时就是**通过改变这个计数器的值来选取下一跳需要执行的字节码指令**，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个技术器完成

由于java虚拟机多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，任何一个时刻，一个处理器（多核处理器是一个内核）都只会执行一条线程中的指令。因此为了线程切换后能恢复到正确的执行位置，**每条线程有一个独立的程序计数器**。

如果**线程执行的是一个java方法，计数器记录的是正在执行的虚拟机字节码指令的地址;如果执行的是Native方法，计数器值为空**。

此内存区域是**唯一一个在java虚拟机规范中没有规定任何OutOfMemoryError情况的区域**。

## Java虚拟机栈
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

## 本地方法栈
本地方法栈与Java虚拟机栈类似，它们之间的区别只不过是虚拟机栈为虚拟机执行java方法服务，而**本地方法栈为虚拟机使用到的Native方法服务**。

本地方法一般是用其它语言（C、C++ 或汇编语言等）编写的，并且被编译为基于本机硬件和操作系统的程序，对待这些方法需要特别处理。

![本地方法栈](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E6%9C%AC%E5%9C%B0%E6%96%B9%E6%B3%95%E6%A0%88.png)

与虚拟机栈一样，本地方法栈区域也会抛出StackOverflowError和 OutOfMemoryError异常
## Java堆
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
## 方法区
方法区与java堆一样，是**各个线程共享**的内存区域，用于**存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据**。

方法区和堆一样不需要连续的内存，并且可以动态扩展，动态扩展失败一样会抛出OutOfMemoryError异常。

对这块区域进行垃圾回收的主要目标是对常量池的回收和对类的卸载，但是一般比较难实现。

HotSpot 虚拟机把它当成永久代来进行垃圾回收。但很难确定永久代的大小，因为它受到很多因素影响，并且每次 Full GC 之后永久代的大小都会改变，所以经常会抛出 OutOfMemoryError 异常。为了更容易管理方法区，**从 JDK 1.8 开始，移除永久代，并把方法区移至元空间，它位于本地内存中，而不是虚拟机内存中。**

方法区是一个 JVM 规范，永久代与元空间都是其一种实现方式。在 JDK 1.8 之后，原来永久代的数据被分到了堆和元空间中。元空间存储类的元信息，静态变量和常量池等放入堆中。
## 运行时常量池
**运行时常量池是方法区的一部分，Class文件中的常量池（编译器生成的字面量和符号引用）会在类加载后被放入这个区域**

运行时常量池相对于Class文件常量池还具备动态性，允许运行期间将新的常量放入池中，例如 String 类的 intern()方法。

既然是运行时常量池是方法区的一部分，自然会受到方法区内存的限制，当常量池无法再申请到内存时抛出OutOfMemoryError异常
## 直接内存
直接内存并不是虚拟机运行时数据区的一部分。但是这部分内存被频繁使用，而且也可能导致OutOfMemoryError异常。


在 JDK 1.4 中新引入了 NIO 类，引入了一种基于通道与缓冲区的I/O方式，它可以使用 Native 函数库直接分配堆外内存，然后通过 Java 堆里的 DirectByteBuffer 对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在堆内存和堆外内存来回拷贝数据。

# 二、垃圾收集
垃圾收集主要是针对堆和方法区进行。程序计数器、虚拟机栈和本地方法栈这三个区域属于线程私有的，只存在于线程的生命周期内，线程结束之后就会消失，因此不需要对这三个区域进行垃圾回收。

## 判断对象是否要被回收
### 1.引用计数算法

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

### 2.可达性分析算法

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

### 引用类型
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

### 方法区的回收
因为方法区主要存放永久代对象，而永久代对象的回收率比新生代低很多，所以在方法区上进行回收性价比不高。

**永久代的垃圾收集主要是废弃常量的回收和对无用类的卸载**。

回收废弃常量与回收java堆中的对象非常类似。例如字符串"abc"进入常量池，但是没有任何String对象引用了常量池中的"abc",如果这时发生内存回收，而且有必要的话，"abc"就会被清理。

无用类的卸载条件很多，需要满足以下三个条件，并且满足了条件也不一定会被卸载：

- 该类所有的实例都已经被回收，此时堆中不存在该类的任何实例。
- 加载该类的 ClassLoader 已经被回收。
- 该类对应的 Class 对象没有在任何地方被引用，也就无法在任何地方通过反射访问该类方法。

为了避免内存溢出，在大量使用反射和动态代理的场景都需要虚拟机具备类卸载功能。
## 垃圾收集算法
### 1.标记-清除算法
![标记清除算法](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E6%A0%87%E8%AE%B0%E6%B8%85%E9%99%A4.png)  

标记-清除算法分为两个阶段：
在标记阶段，程序会检查每个对象是否为活动对象，如果是活动对象，则程序会在对象头部打上标记。  

在清除阶段，会进行对象回收并取消标志位，另外，还会判断回收后的分块与前一个空闲分块是否连续，若连续，会合并这两个分块。回收对象就是把对象作为分块，连接到被称为 “空闲链表” 的单向链表，之后进行分配时只需要遍历这个空闲链表，就可以找到分块。

在分配时，程序会搜索空闲链表寻找空间大于等于新对象大小 size 的块 block。如果它找到的块等于 size，会直接返回这个分块；如果找到的块大于 size，会将块分割成大小为 size 与 (block - size) 的两部分，返回大小为 size 的分块，并把大小为 (block - size) 的块返回给空闲链表。

不足：
- 标记和清除过程效率都不高；
- 会产生大量不连续的内存碎片，导致无法给大对象分配内存，从而不得不提前触发另一次垃圾收集动作。

### 2.复制算法
![复制算法](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E5%A4%8D%E5%88%B6.png)

它将可用内存按容量划分为大小相等的两块，每次只使用其中一块。当着一块的内存用完了，就将还存活的对象复制到另一块上面，然后再把已使用过的内存空间一次清理掉。

优点是由于每次只需要对整个半区进行内存回收，内存分配时就不用考虑内存碎片等问题，直接按顺序分配内存即可，实现简单，运行高效。

主要不足是只使用了内存的一半。

现在的商业虚拟机都采用这种收集算法回收新生代，但是并不是划分为大小相等的两块，而是一块较大的 Eden 空间和两块较小的 Survivor 空间，每次使用 Eden 和其中一块 Survivor。在回收时，将 Eden 和 Survivor 中还存活着的对象全部复制到另一块 Survivor 上，最后清理 Eden 和使用过的那一块 Survivor。

HotSpot 虚拟机的 Eden 和 Survivor 大小比例默认为 8:1，保证了内存的利用率达到 90%。如果每次回收有多于 10% 的对象存活，那么一块 Survivor 就不够用了，此时需要依赖于老年代进行空间分配担保，也就是借用老年代的空间存储放不下的对象。

### 3.标记-整理算法
![标记整理算法](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E6%A0%87%E8%AE%B0%E6%95%B4%E7%90%86.png)  

标记过程与标记-清除算法一样，但是后续不是直接对可回收对象进行清理而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。

优点:不会产生内存碎片

不足: 需要移动大量对象，处理效率比较低。

### 4.分代收集算法
现在的商业虚拟机采用分代收集算法，它根据对象存活周期将内存划分为几块，不同块采用适当的收集算法。

一般将堆分为新生代和老年代。

- 新生代使用：复制算法
- 老年代使用：标记 - 清除 或者 标记 - 整理 算法
## 垃圾收集器
![垃圾收集器](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8.png)

以上是 HotSpot 虚拟机中的 7 个垃圾收集器，连线表示垃圾收集器可以配合使用。

### 1.Serial收集器
Serial/Serial Old收集器
![Serial收集器](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/Serial%E6%94%B6%E9%9B%86%E5%99%A8.png)

**Serial是单线程的收集器**，只会使用一个线程进行垃圾收集工作。并且在**进行垃圾收集时需要暂停其他所有工作线程(Stop The World)，直到它收集结束**。

它的优点是简单高效，在单个 CPU 环境下，由于没有线程交互的开销，因此拥有最高的单线程收集效率。

**它是 Client 场景下的默认新生代收集器**，因为在该场景下内存一般来说不会很大。它收集一两百兆垃圾的停顿时间可以控制在一百多毫秒以内，只要不是太频繁，这点停顿时间是可以接受的。
### 2.ParNew收集器
ParNew/Serial Old收集器
![ParNew收集器](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/Par%20New%E6%94%B6%E9%9B%86%E5%99%A8.png)

它是 Serial 收集器的多线程版本。除了使用多条线程进行垃圾收集外，其他行为比如垃圾收集算法、Stop The World、对象分配与回收等都与Serial收集器完全相同

**它是 Server 场景下默认的新生代收集器**，除了性能原因外，主要是因为除了 Serial 收集器，只有它能与 CMS 收集器配合使用。
### 3.Parallel Scavenge收集器
与 ParNew 一样是多线程收集器。

其它收集器目标是尽可能缩短垃圾收集时用户线程的停顿时间，而**它的目标是达到一个可控制的吞吐量**，因此它被称为“吞吐量优先”收集器。这里的吞吐量指 CPU 用于运行用户程序的时间与CPU总消耗时间的比值。

停顿时间越短就越适合需要与用户交互的程序，良好的响应速度能提升用户体验。而高吞吐量则可以高效率地利用 CPU 时间，尽快完成程序的运算任务，适合在后台运算而不需要太多交互的任务。

缩短停顿时间是以牺牲吞吐量和新生代空间来换取的：系统把新生代空间调小，垃圾回收变得频繁，导致吞吐量下降。

### 4.Serial Old收集器
Serial/Serial Old收集器
![Serial Old收集器](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/SerialOld%E6%94%B6%E9%9B%86%E5%99%A8.png)

是 Serial 收集器的老年代版本，同样是一个单线程收集器，使用"标记-整理"算法。

Serial Old收集器主要是给 Client 场景下的虚拟机使用。如果用在 Server 场景下，它有两大用途：

- 在 JDK 1.5 以及之前版本（Parallel Old 诞生以前）中与 Parallel Scavenge 收集器搭配使用。
- 作为 CMS 收集器的后备预案，在并发收集发生 Concurrent Mode Failure 时使用。

### 5.Parallel Old收集器
Parallel Scavenge/Parallel Old收集器
![Parallel Old收集器](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/Parallel%20Old%E6%94%B6%E9%9B%86%E5%99%A8.png)  

Parallel Old收集器是 Parallel Scavenge 收集器的老年代版本。使用多线程和"标记-整理"算法。

在注重吞吐量以及 CPU 资源敏感的场合，都可以优先考虑 Parallel Scavenge 加 Parallel Old 收集器。

### 6.CMS收集器
CMS收集器
![CMS收集器](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/CMS%E6%94%B6%E9%9B%86%E5%99%A8.png)

CMS（Concurrent Mark Sweep)收集器是一种以获取最短回收停顿时间为目标的收集器。Mark Sweep 指的是标记 - 清除算法。

整个运行过程分为4个步骤：
1. 初始标记：仅仅只是标记一下 GC Roots 能直接关联到的对象，速度很快，需要停顿。
2. 并发标记：进行 GC Roots Tracing 的过程，它在整个回收过程中耗时最长，不需要停顿。
3. 重新标记：为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，需要停顿。
4. 并发清除：不需要停顿。

由于整个过程中耗时最长的并发标记和并发清除过程收集器线程都可以与用户线程一起工作，所以，总体上来说CMS收集器的内存回收过程是与用户线程一起并发执行的。

具有以下缺点：

- 吞吐量低：低停顿时间是以牺牲吞吐量为代价的，导致 CPU 利用率不够高。
- 无法处理浮动垃圾，可能出现 Concurrent Mode Failure。浮动垃圾是指并发清除阶段由于用户线程继续运行而产生的垃圾，这部分垃圾只能到下一次 GC 时才能进行回收。由于浮动垃圾的存在，因此需要预留出一部分内存，意味着 CMS 收集不能像其它收集器那样等待老年代快满的时候再回收。如果预留的内存不够存放浮动垃圾，就会出现 Concurrent Mode Failure，这时虚拟机将临时启用 Serial Old 来替代 CMS。
- 标记 - 清除算法导致的空间碎片，往往出现老年代空间剩余，但无法找到足够大连续空间来分配当前对象，不得不提前触发一次 Full GC

### 7.G1收集器
G1（Garbage-First），它是一款面向服务端应用的垃圾收集器，在多 CPU 和大内存的场景下有很好的性能。HotSpot 开发团队赋予它的使命是未来可以替换掉 CMS 收集器。

堆被分为新生代和老年代，其它收集器进行收集的范围都是整个新生代或者老年代，而 G1 可以直接对新生代和老年代一起回收。

![G1收集器1](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/G1%E6%94%B6%E9%9B%86%E5%99%A81.png)

G1 把堆划分成多个大小相等的独立区域（Region），新生代和老年代不再物理隔离。

![G1收集器2](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/G1%E6%94%B6%E9%9B%86%E5%99%A82.png)

通过引入 Region 的概念，从而将原来的一整块内存空间划分成多个的小空间，使得每个小空间可以单独进行垃圾回收。这种划分方法带来了很大的灵活性，使得可预测的停顿时间模型成为可能。通过记录每个 Region 垃圾回收时间以及回收所获得的空间（这两个值是通过过去回收的经验获得），并维护一个优先列表，每次根据允许的收集时间，优先回收价值最大的 Region。

每个 Region 都有一个 Remembered Set，用来记录该 Region 对象的引用对象所在的 Region。通过使用 Remembered Set，在做可达性分析的时候就可以避免全堆扫描。

![G1收集器3](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/G1%E6%94%B6%E9%9B%86%E5%99%A83.png)

如果不计算维护 Remembered Set 的操作，G1 收集器的运作大致可划分为以下几个步骤：

1. 初始标记：仅仅只是标记一下 GC Roots 能直接关联到的对象，并且修改TAMS的值，速度很快，需要停顿。
2. 并发标记：进行 GC Roots Tracing 的过程，它在整个回收过程中耗时最长，不需要停顿。
3. 最终标记：为了修正在并发标记期间因用户程序继续运作而导致标记产生变动的那一部分标记记录，虚拟机将这段时间对象变化记录在线程的 Remembered Set Logs 里面，最终标记阶段需要把 Remembered Set Logs 的数据合并到 Remembered Set 中。这阶段需要停顿线程，但是可并行执行。
4. 筛选回收：首先对各个 Region 中的回收价值和成本进行排序，根据用户所期望的 GC 停顿时间来制定回收计划。此阶段其实也可以做到与用户程序一起并发执行，但是因为只回收一部分 Region，时间是用户可控制的，而且停顿用户线程将大幅度提高收集效率。

### 垃圾收集相关常用参数
|参数|意义|
|:--:|:--:|
|UseSerialGC|	虚拟机运行在Client模式下的默认值，打开此开关后，使用Serial + Serial Old的收集器组合进行内存回收|
|UseParNewGC|	打开此开关后，使用ParNew + Serial Old的收集器组合进行内存回收|
|UseConcMarkSweepGC|	打开此开关后，使用ParNew + CMS + Serial Old的收集器组合进行内存回收。Serial Old收集器将作为CMS收集器出现Concurrent Mode Failure失败后的后备收集器使用|
|UseParallelGC|	虚拟机运行在Server模式下的默认值，打开此开关后，使用Parallel Scavenge + Serial Old（PS MarkSweep）的收集器组合进行内存回收|
|UseParallelOldGC|	打开此开关后，使用Parallel Scavenge + Parallel Old 的收集器组合进行内存回收|
|SurvivorRatio|	新生代中Eden区域与Survivor区域的容量比值，默认为8，代表Eden : Survivor=8:1|
|PretenureSizeThreshold|	直接晋升到老年代的对象大小，设置这个参数后，大于这个参数的对象将直接在老年代分配|
|MaxTenuringThreshold|	晋升到老年代的对象年龄。每个对象在坚持过一次Minor GC之后，年龄就增加1，当超过这个参数值时就进入老年代|
|UseAdaptiveSizePolicy|	动态调整Java堆中各个区域的大小以及进入老年代的年龄|
|HandlePromotionFailure|	是否允许分配担保失败，即老年代的剩余空间不足以应付新生代的整个Eden和Survivor区的所有对象都存活的极端情况|
ParallelGCThreads|	设置并行GC时进行内存回收的线程数
GCTimeRatio	|GC时间占总时间的比率，默认值为99，即允许1%的GC时间。仅使用Parallel Scavenge收集器时生效
MaxGCPauseMillis|	设置GC的最大停顿时间。仅在使用Parallel Scavenge收集器生效
CMSInitiatingOccupancyFraction|	设置CMS收集器在老年代空间被使用多少后触发垃圾收集，默认值为68%，仅使用CMS收集器时生效
UseCMSCompactAtFullCollection|	设置CMS收集器在完成垃圾收集后是否要进行一次内存碎片整理。仅在使用CMS收集器时生效
CMSFullGCsBeforeCompaction|	设置CMS收集器在进行若干次垃圾收集后再启动一次内存碎片整理。仅在使用CMS收集器时生效

# 三、内存分配与回收策略
## 1. 对象优先在 Eden 分配
大多数情况下，对象在新生代 Eden 上分配，当 Eden 空间不够时，发起 Minor GC。

## 2. 大对象直接进入老年代
大对象是指需要连续内存空间的对象，最典型的大对象是那种很长的字符串以及数组。

经常出现大对象会提前触发垃圾收集以获取足够的连续空间分配给大对象。

-XX:PretenureSizeThreshold，大于此值的对象直接在老年代分配，避免在 Eden 和 Survivor 之间的大量内存复制。

## 3. 长期存活的对象进入老年代
为对象定义年龄计数器，对象在 Eden 出生并经过 Minor GC 依然存活，将移动到 Survivor 中，年龄就增加 1 岁，增加到一定年龄则移动到老年代中。

-XX:MaxTenuringThreshold 用来定义年龄的阈值。

## 4. 动态对象年龄判定
虚拟机并不是永远要求对象的年龄必须达到 MaxTenuringThreshold 才能晋升老年代，如果在 Survivor 中相同年龄所有对象大小的总和大于 Survivor 空间的一半，则年龄大于或等于该年龄的对象可以直接进入老年代，无需等到 MaxTenuringThreshold 中要求的年龄。

## 5. 空间分配担保
在发生 Minor GC 之前，虚拟机先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，如果条件成立的话，那么 Minor GC 可以确认是安全的。

如果不成立的话虚拟机会查看 HandlePromotionFailure 的值是否允许担保失败，如果允许那么就会继续检查老年代最大可用的连续空间是否大于历次晋升到老年代对象的平均大小，如果大于，将尝试着进行一次 Minor GC；如果小于，或者 HandlePromotionFailure 的值不允许冒险，那么就要进行一次 Full GC。

# 四、类加载机制

虚拟机把描述类的数据从Class文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的Java类型，这就是虚拟机的类加载机制。

类从加载到虚拟机内存中开始，到卸载出内存为止，它的整个生命周期包括：加载、验证、准备、解析、初始化、使用和卸载7个阶段。

![类的生命周期](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/%E7%B1%BB%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

## 类加载过程
### 加载
加载过程完成以下三件事：

- 通过类的完全限定名称获取定义该类的二进制字节流。
- 将该字节流表示的静态存储结构转换为方法区的运行时存储结构。
- 在内存中生成一个代表该类的 java.lang.Class 对象，作为方法区中该类各种数据的访问入口。


### 验证
验证的目的是确保 Class 文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。

主要包括：
- 文件格式验证
- 元数据验证
- 字节码验证
- 符号引用验证

### 准备
准备阶段是正式为类变量分配内存并设置类变量初始值的阶段，这些变量所使用的内存都将在方法区中进行分配。

注意这时候进行内存分配的仅仅是类变量(被static修饰的变量)，而不包括实例变量，实例变量会在对象实例化时随着对象一起分配在Java堆中。



### 解析
将常量池内的符号引用替换为直接引用的过程。

其中解析过程在某些情况下可以在初始化阶段之后再开始，这是为了支持 Java 的动态绑定。

### 初始化

## 类加载器
### 类与类加载器

### 双亲委派模型

### 破坏双亲委派模型

# 五、Java内存模型

# 六、线程安全与锁优化
