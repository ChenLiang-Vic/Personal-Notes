<!-- TOC -->

- [0.数组](#0数组)
    - [基本知识点](#基本知识点)
    - [数组常用工具类](#数组常用工具类)
- [1.String类](#1string类)
    - [基本知识点](#基本知识点-1)
    - [StringBuilder和StringBuffer](#stringbuilder和stringbuffer)
    - [String Pool](#string-pool)
- [2.包装类](#2包装类)
    - [自动装箱与拆箱](#自动装箱与拆箱)
    - [缓存池](#缓存池)
- [3.Object类](#3object类)
    - [概览](#概览)
    - [equals()](#equals)
    - [hashCode()](#hashcode)
    - [toString()](#tostring)
    - [clone()](#clone)
        - [Cloneable接口](#cloneable接口)
        - [浅拷贝](#浅拷贝)
        - [深拷贝](#深拷贝)
- [4.时间相关类](#4时间相关类)
    - [Date类](#date类)
    - [DateFormat类](#dateformat类)
    - [Calendar类](#calendar类)
- [5.Math类](#5math类)
- [6.Random类](#6random类)

<!-- /TOC -->
## 0.数组
### 基本知识点
数组是相同类型数据的有序集合。在内存中为一块连续的空间。数组在创建后长度不可变。

```java
public class Demo {
    public static void main(String[] args) {
        //静态初始化
        int[] a = {1,2,3,4,5};

        //动态初始化，也可以用循环
        String[] str = new String[3];
        str[0] = "ch";
        str[1] = "en";
        str[2] = "haha";

        Student[] stu = new Student[2];  //简单的student类就不放代码了
        stu[0] = new Student("jack",18);
        stu[1] = new Student("mark",20);
        System.out.println(stu[1]);  //姓名mark年龄20

        //二维数组，每一维度可以长度不同，内存中存的是每个数组的地址
        int[][] b = new int[3][5];
        b[0][0] = 1;
        
        //循环1
        for(int i=0;i<str.length;i++) {
            System.out.println(str[i]);
        }
        //循环2  for-each循环
        for(String temp:str) {
            System.out.println(temp);
        }
        System.out.println(b[1][2]);  //5
        //二维数组两层循环即可
        for(int i=0;i<b.length;i++) {
            for(int j=0;j<b[i].length;j++) {
                System.out.println(b[i][j]);				
            }
        }
    } 
}

```
### 数组常用工具类
- System类中提供了数组的拷贝方法
```java
System.arraycopy(Object src, int srcPos,Object dest, int destPos,int length);
```
- java工具类中提供了便于操作数组的工具类Arrays
1.toString  将数组转化为字符串,其实是重写了Object的toString方法
2.sort将数组升序排序（快排）只提供了升序。
3.binarySearch(int[] a,int key)  二分法查找数组，返回元素索引值int类型

都是重载的基本支持各种类型
```java
import java.util.Arrays;

public class Demo {
    public static void main(String[] args) {
        int arr[] = { 5, 4, 8, 9, 6, 7, 1, 2, 3 };
        int arr2[] = new int[9];
        //数组拷贝
        System.arraycopy(arr,0,arr2,0,arr.length);  //全拷贝
        // toString
        System.out.println(Arrays.toString(arr2));// [5, 4, 8, 9, 6, 7, 1, 2, 3]
        // 数组升序排列
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));  //[1, 2, 3, 4, 5, 6, 7, 8, 9]
        // 查找4在当前数组中的位置，使用二分法查找
        System.out.println(Arrays.binarySearch(arr, 4));  //3
    } 
}
```
## 1.String类
### 基本知识点
String位于java.lang包中

String 被声明为 final，因此它不可被继承。在 Java 8 中，String 内部使用char数组存储数据，Java9之后改用 byte数组存储字符串。

字符串为不可更改的常量，在创建字符串后便不能更改。正是因为不可变，所以有很多优点：
- 可以缓存 hash 值  
  因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。
- String Pool 的需要
  如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。
- 安全性
  String 经常作为参数，String 不可变性可以保证参数不可变。
- 线程安全
  String 不可变性天生具备线程安全，可以在多个线程中安全地使用。

常用方法：
```java
import java.util.Arrays;

public class Demo {
    public static void main(String[] args) {
        String str1 ="hello world";
        String str2 = "HELLO WORLD";
        //返回字符串index处字母
        System.out.println(str1.charAt(2));  //第一个l
        //返回字符串长度
        System.out.println(str1.length());  //11
        //返回字符串索引位置
        System.out.println(str1.indexOf("wor"));  //6
        //返回一个新子串
        System.out.println(str1.substring(1,7));  //ello w 注意不包含7位置元素

        //将字符串转化为字符数组
        char[] ch = str1.toCharArray();
        System.out.println(Arrays.toString(ch)); //[h, e, l, l, o,  , w, o, r, l, d]
        //将字符串转化为字节数组，通过ASCII码进行转化
        byte[] by = str1.getBytes(); 
        System.out.println(Arrays.toString(by));//[104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100]

        //字符串分割,注意split后面跟的是正则表达式
        String str2 = "aaa,bbb,ccc";
        String[] str = str2.split(",");
        for(String temp:str){
            System.out.println(temp);  //aaa bbb ccc
        }
    } 
}
```
### StringBuilder和StringBuffer
都是抽象类AbstractStringBuilder的子类，均为可变字符串序列，底层实现方式还是字节数组，只不过没有final修饰

StringBuilder线程不安全，不做线程同步检查，效率高。（单线程优先采用）
StringBuffer线程安全，做线程同步检查，内部使用 synchronized 进行同步，效率低；

```java

public class TestStringBufferAndBuilder 1{
    public static void main(String[] args) {
        /**StringBuilder*/
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 7; i++) {
            sb.append((char) ('a' + i));//追加单个字符
        }
        System.out.println(sb.toString());//转换成String输出  abcdefg
        sb.append(", I can sing my abc!");//追加字符串  
        System.out.println(sb.toString());  abcdefg，I can sing my abc!
        /**StringBuffer*/
        StringBuffer sb2 = new StringBuffer("中华人民共和国");
        sb2.insert(0, "爱").insert(0, "我");//插入字符串
        System.out.println(sb2);                 //我爱中华人民共和国
        sb2.delete(0, 2);//删除子字符串
        System.out.println(sb2);                //中华人民共和国
        sb2.deleteCharAt(0).deleteCharAt(0);//删除某个字符
        System.out.println(sb2.charAt(0));//获取某个字符  //人
        System.out.println(sb2.reverse());//字符串逆序    //国和共民人
    }
}
```

### String Pool
先来看一个例子
```java
public class Demo {
    public static void main(String[] args) {
        String str1 = "aaa";
        String str2 = "aaa";
        String str3 = new String("aaa");

        System.out.println(str1==str2);  //true
        System.out.println(str1==str3);  //false
        System.out.println(str2==str3);  //false
        }
}
```
上面理论上来说str1和str2和str3都是abc,可是为什么str1和str2相等，而str1、str2和str3不相等。

原因：
1. 直接使用String str = "aaa"这种方式创建的字符串会自动放入字符串常量池中
2. 对于引用数据类型,==比较的是两个的地址是否相同。

字符串常量池（String Pool）保存着所有字符串字面量（literal strings）,这些字面量在编译时期就确定。

如果是以String str = "aaa"这种方式创建,"aaa" 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 "aaa" 字符串字面量。

如果使用String str3 = new String("aaa");这种方式,除了在编译期产生"aaa"对象外,还会在堆中创建一个字符串"aaa"对象,并将堆中的地址返回。

所以说上面的str1和str2地址相同,而str3和他们不同,自然出现上述结果。

那么既然他们都是abc,而且我们关心的也是他们字符串的字面量，那么如何才能进行相等判断呢?
- 使用equals()方法
String类重写了Object的equals方法,如下。可以看到，对于String类的对象，直接对字符数组中的每个元素挨个进行比较。
```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

```java
System.out.println(str1.equals(str3));  //true
```

- 使用intern()方法
一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。
```java
String str4 = str3.intern();
System.out.println(str1==str4);  //true
```


## 2.包装类
在实际应用中经常需要将基本数据转化成对象，以便于操作，比如将数据放入Object数组中，所以Java中每个基本类型都对应有一个包装类型。

除了int--Integer和char--Character外，其他基本类型的包装类都是首字母大写

### 自动装箱与拆箱
基本类型与对应包装类型之间，来回转换的过程称为装箱和拆箱
- 装箱:基本数据类型转为包装类
- 拆箱:包装类转为基本数据类型
```java
//装箱
Integer a = new Integer(1);
Integer b = Integer.valueOf(1);
//拆箱
int c = a.intValue();
```
由于经常使用装箱与拆箱操作，JDK1.5之后提供了自动装箱和拆箱的功能
```java
Integer a = 1;
int b = a 
```
### 缓存池
new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123) 每次都会新建一个对象；
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。
```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。
```java
Integer m = 123;
Integer n = 123;
Integer m1 = 128;
Integer n1 = 128;
System.out.println(m == n); // true
System.out.println(m == n); // false
```

通过查看源码知道，在 Java 8 中Integer 缓存池的大小默认为 -128~127。只有在这个范围内的数才会在缓存池中。
基本类型对应的缓冲池如下：

- boolean values true and false
- all byte values
- short values between -128 and 127
- int values between -128 and 127
- char in the range \u0000 to \u007F

再看下面double类型都为false
```java
Double i1 = 100.0;
Double i2 = 100.0;
Double i3 = 200.0;
Double i4 = 200.0;
System.out.println(i1==i2); //false
System.out.println(i3==i4); //false
```

在这里只解释一下为什么Double类的valueOf方法会采用与Integer类的valueOf方法不同的实现。很简单：在某个范围内的整型数值的个数是有限的，而浮点数却不是。

下面我们进行一个归类： 
Integer派别：Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的。 
Double派别：Double、Float的valueOf方法的实现是类似的。每次都返回不同的对象。

## 3.Object类
java.lang包中的类，是Java语言中所有类的根类，它中描述的所有方法子类都可以使用，在对象实例化的时候，最终找的就是父类Object。

如果一个类没有特别指定父类，那么默认继承自Object类
### 概览
```java
public native int hashCode()

public boolean equals(Object obj)

protected native Object clone() throws CloneNotSupportedException

public String toString()

public final native Class<?> getClass()

protected void finalize() throws Throwable {}

public final native void notify()

public final native void notifyAll()

public final native void wait(long timeout) throws InterruptedException

public final void wait(long timeout, int nanos) throws InterruptedException

public final void wait() throws InterruptedException
```

### equals()
**等价关系**
1. 自反性  x.equals(x); // true
2. 对称性 x.equals(y) == y.equals(x); // true
3. 传递性
if (x.equals(y) && y.equals(z))
    x.equals(z); // true;
4. 一致性 多次调用 equals() 方法结果不变 
x.equals(y) == x.equals(y); // true
5. 与 null 的比较  对任何不是 null 的对象 x 调用 x.equals(null) 结果都为 false
x.equals(null); // false;

**==和equals()**
- 对于基本类型，== 判断两个值是否相等.对于引用类型，== 判断两个变量是否引用同一个对象。
- Object类中的equals方法其实就是==,但如果重写了equals方法则会按照重写的equals方法进行比较。比如Srting类的包装类等。

Object类中的equals方法源码如下，可以看到直接是对两个对象的地址值进行比较。
```java
public boolean equals(Object obj) {
    return (this == obj);
}
```
```java
Integer x = new Integer(1);
Integer y = new Integer(1);
System.out.println(x.equals(y)); // true
System.out.println(x == y);      // false
```
**重写equals方法**
```java
Student stu1 = new Student("陈",18);
Student stu2 = new Student("陈",18);
System.out.println(stu1.equals(stu2));  //false
```
因为Student默认继承Object,所以会比较两者的地址。重写Student的equals方法,根据名字和年龄相同则认为两者相同
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Student student = (Student) o;
    return age == student.age &&
            Objects.equals(name, student.name);
}
```
重写后再次进行比较,值为true
```java
System.out.println(stu1.equals(stu2));  //true
```
### hashCode()
hashCode() 返回散列值，而 equals() 是用来判断两个对象是否等价。等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价。

在覆盖 equals() 方法时应当总是覆盖 hashCode() 方法，保证等价的两个对象散列值也相等。

下面的代码中，新建了两个等价的对象，并将它们添加到 HashSet 中。我们希望将这两个对象当成一样的，只在集合中添加一个对象，但是因为 EqualExample 没有实现 hasCode() 方法，因此这两个对象的散列值是不同的，最终导致集合添加了两个等价的对象。
```java
EqualExample e1 = new EqualExample(1, 1, 1);
EqualExample e2 = new EqualExample(1, 1, 1);
System.out.println(e1.equals(e2)); // true
HashSet<EqualExample> set = new HashSet<>();
set.add(e1);
set.add(e2);
System.out.println(set.size());   // 2
```
理想的散列函数应当具有均匀性，即不相等的对象应当均匀分布到所有可能的散列值上。这就要求了散列函数要把所有域的值都考虑进来。可以将每个域都当成 R 进制的某一位，然后组成一个 R 进制的整数。R 一般取 31，因为它是一个奇素数，如果是偶数的话，当出现乘法溢出，信息就会丢失，因为与 2 相乘相当于向左移一位。

一个数与 31 相乘可以转换成移位和减法：31*x == (x<<5)-x，编译器会自动进行这个优化。
```java
@Override
public int hashCode() {
    int result = 17;
    result = 31 * result + x;
    result = 31 * result + y;
    result = 31 * result + z;
    return result;
}
```

### toString()
通过源码可以看到，返回的是getClass().getName() + "@" + Integer.toHexString(hashCode()这种形式，@前面为包路径，@后面的数值为散列码的无符号十六进制表示。即整个返回的是这个类的地址。
```java
public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
```

因为每次调用System.out.println(对象),都是在调用对象的toString()方法。如果想要直接打印出想要的内容，需要重写Object类的toString方法
```java
@Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
```
```java
Student s = new Student("陈",18);
//Student没重写toString方法
System.out.println(s);  //com.company.test.Student@1b6d3586

//Student重写了toString方法
System.out.println(s);  //Student{name='陈', age=18}
```
### clone()
在某些场景中，我们需要获取到一个对象的拷贝用于某些处理。这时候就可以用到Java中的Object.clone方法进行对象复制，得到一个一模一样的新对象。

我们看一下clone方法的源码：
```java
protected native Object clone() throws CloneNotSupportedException;
```
可以看到
- clone方法是native方法，说明这个方法的实现不是在java中，而是由C/C++实现，并编译成.dll文件，由java调用。
- clone() 是 Object 的 protected 方法，它不是 public，一个类不显式去重写 clone()，其它类就不能直接去调用该类实例的 clone() 方法。(注意protected修饰方法时，对于不是同一个包中的子类不能访问该方法)
- 返回值是一个Object对象，所以要强制转换才行

#### Cloneable接口
在Student类中重写clone()方法
```java
@Override
protected Object clone() throws CloneNotSupportedException {
    return super.clone();
}
```
测试
```java
Student stu = new Student("陈",18);
    try {
        Student stucopy = (Student) stu.clone();
    } catch (CloneNotSupportedException e) {
        e.printStackTrace();
    }
```
会发现上面的程序报错，抛出了 CloneNotSupportedException，这是因为 CloneExample 没有实现 Cloneable 接口。

应该注意的是，clone() 方法并不是 Cloneable 接口的方法，而是 Object 的一个 protected 方法。Cloneable 接口只是规定，如果一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会抛出 CloneNotSupportedException。

现在让学生类实现Cloneable接口后,可以进行拷贝。

#### 浅拷贝
创建一个新对象，然后将当前对象的非静态字段复制到该对象，如果字段类型是值类型（基本类型）的，那么对该字段进行复制；如果字段是引用类型的，则只复制该字段的引用而不复制引用指向的对象。

Object类中的clone()方法就是浅拷贝

下面进行下测试，在上面的Student类中添加一个简单的Teacher类，就不写了，直接测试。
```java
public class Demo {
    public static void main(String[] args) throws CloneNotSupportedException {
        Student stu = new Student("陈",18,new Teacher("数学老师",30));
        Student stucopy = (Student) stu.clone();
        System.out.println(stu.hashCode());
        System.out.println(stucopy.hashCode());
        System.out.println(stu);
        System.out.println(stucopy);

        stucopy.setAge(20);

        //通过clone出的对象对属性的引用对象进行修改
        Teacher teacher = stucopy.getTeacher();
        teacher.setName("英语老师");
        System.out.println(stu);
        System.out.println(stucopy);
    }
}
```
结果如下所示：
```
460141958
1163157884
Student{name='陈', age=18, teacher=Teacher{name='数学老师', age=30}}
Student{name='陈', age=18, teacher=Teacher{name='数学老师', age=30}}
Student{name='陈', age=18, teacher=Teacher{name='英语老师', age=30}}
Student{name='陈', age=20, teacher=Teacher{name='英语老师', age=30}}
```
第1、2句输出说明了原对象与新对象是两个不同的对象。
第3、4句可以看到拷贝出来的新对象与原对象内容一致
但是，接着将克隆出的对象里面的信息进行了修改，然后输出发现对于基本类型只有clone出的对象发生了改变。而对于引用类型原对象里面的部分信息也跟着变了，这就跟clone本身的浅拷贝有关系了。

#### 深拷贝
知道了浅拷贝，深拷贝就很容易理解了，即将引用类型的属性内容也拷贝一份新的。

那么如何实现深拷贝呢？有两种方式：
- 深拷贝-clone方式
- 序列化-反序列化对象

**深拷贝-clone方式**
对于以上演示代码，利用clone方式进行深拷贝无非就是将Teacher类也实现Cloneable，然后对Student的clone方法进行调整。

Teacher类也实现Cloneable，重写clone方法
```java
@Override
protected Object clone() throws CloneNotSupportedException {
    return super.clone();
}
```
对Student的clone方法进行调整
```java
@Override
protected Object clone() throws CloneNotSupportedException {
    Student s = (Student) super.clone();
    //手动对Teacher属性进行clone，并赋值给新的Student对象
    s.teacher = (Teacher) teacher.clone();
    return s;
}
```
这个方法实际上就是将Teacher进行了一次浅拷贝，然后将拷贝好的teacher对象赋值给了s。

重新进行测试后,发现只有拷贝的对象属性发生了变化,原对象保持不变。

**序列化-反序列化对象**
这种方式其实就是将对象转成二进制流，然后再把二进制流反序列成一个java对象，这时候反序列化生成的对象是一个全新的对象，里面的信息与原对象一样，但是所有内容都是一份新的。

这种方式需要注意的地方主要是所有类都需要实现Serializable接口，以便进行序列化操作。

序列化核心代码
```java
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;

public class DeepClone implements Serializable{
    private static final long serialVersionUID = 1L;

    /**
     * 利用序列化和反序列化进行对象的深拷贝
     * @return
     * @throws Exception
     */
    protected Object deepClone() throws Exception{
        //序列化
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);
        
        oos.writeObject(this);
        
        //反序列化
        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bis);
        
        return ois.readObject();
    }
}
```
Student类和Teacher类都需要实现Serializable接口,对于Student直接继承DeepClone,就不写了。

测试：
```java
public class Demo {
    public static void main(String[] args) throws Exception {
        Student stu = new Student("陈",18,new Teacher("数学老师",30));
        Student stucopy = (Student) stu.deepClone();

        //通过clone出的对象对属性的引用对象进行修改
        Teacher teacher = stucopy.getTeacher();
        teacher.setName("英语老师");
        System.out.println(stu);
        System.out.println(stucopy);
    }
}
```
```
Student{name='陈', age=18, teacher=Teacher{name='数学老师', age=30}}
Student{name='陈', age=18, teacher=Teacher{name='英语老师', age=30}}
```
结果可以看到实现了深拷贝。

## 4.时间相关类
### Date类
构造方法
- Date()
初始化一个Date对象，测量一个当前最近的毫秒值。数值是从1970年1月1日00:00:00到当前系统时间的毫秒值。，存在一定时差跟底层操作系统有关。

- Date(long date)
指定一个毫秒值创建Date对象，这里指定的是从1970年1月1日00:00:00起到指定时间所经历的毫秒值。

常用方法  

- setTime(long time)   为当前Date对象设置毫秒值
- getTime()       返回Date对象的毫秒值转换成long类型返回。

```java
import java.util.Date;//使用Date类需要导包，注意是util包下的Date不是sql包下的。
 
public class Datetest {
    public static void main(String[] args) {
        Date d = new Date();
        // 直接打印输出的是Date的日期格式
        System.out.println(d);// Thu May 03 20:13:36 CST 2018
        // 使用指定的毫秒值创建Date对象
        Date d2 = new Date(1000);  
        // 这里因为是基于底层操作系统的时间计算的所以存在时差，当前操作系统是从1970年1月1日8：00开始计算时间的
        System.out.println(d2);// Thu Jan 01 08:00:01 CST 1970
        d.setTime(1000);// 为Date对象设置时间
        System.out.println(d);// Thu Jan 01 08:00:01 CST 1970
        Date d3 = new Date();
        long i = d3.getTime();// 将Date对象传换成毫秒值。
        System.out.println(i);// 1525349616789

    }
}
```
### DateFormat类
把时间对象转化成指定格式的字符串。反之，把指定格式的字符串转化成时间对象。

DateFormat是一个抽象类，一般使用它的的子类SimpleDateFormat类来实现。

常用构造方法:

SimpleDateFormat(String pattern)  使用规定的格式来创建一个SimpleDateFormat类对象。 一般为yyyy-MM-dd  hh:mm:ss 表示    年-月-日     时:分:秒。

常用方法:
- format(Date date) 使用SimpleDateFormat对象将Date类对象转化成String类型，括号中的参数需要给定一个Date类对象。

- parse(String source)通过SimpleDateFormat对象将指定字符串通过SimpleDateFormat中指定的格式解析成Date类对象的日期格式。需要注意如果给定的字符串格式不符合SimpleDateFormat中规定的格式将会发生异常ParseException。

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
 
public class Demo {
    public static void main(String[] args) throws ParseException {
        
        //Date转String（格式化）
        Date d = new Date(); 
        System.out.println(d);  //Tue Nov 20 20:33:44 CST 2018
        
        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");

        String s1 = sdf1.format(d); 
        System.out.println(s1);  //2018-11-20 20:31:07
        String s2 = sdf2.format(d);
        System.out.println(s2);  //2018年11月20日 20时31分07秒
        
        //String转Date（解析）
        String str = "1970-1-2 0:0:0";
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date d2 = sdf2.parse(str);
        System.out.println(d2);  //Fri Jan 02 00:00:00 CST 1970
	} 
}
```
### Calendar类
Calendar 类是一个抽象类，为我们提供了关于日期计算的相关功能，比如：年、月、日、时、分、秒的展示和计算。

注意：Calendar类因为是抽象类，无法直接创建对象，不过里面提供了静态的getInstance()方法返回来Calendar子类对象。

常用方法：
```java
public int get(int field)  //返回给定日历字段的值 
public void set(int field)  //将给定的日历字段设置为给定值
public abstract void add(int field, int amount)  //根据日历的规则，为给定的日历字段添加或减去在指定的时间量
public Date getTime()  //返回一个表示此Calendar的时间值(从历元到现在的毫秒偏移量)
```

```java
import java.util.Calendar;
import java.util.Date;

public class Demo {
    public static void main(String[] args) {
        Calendar calendar = Calendar.getInstance();
        System.out.println(calendar);  //当前时间2018年6月8日,星期6 16:29:41

        printCalendar(calendar);

        //set方法
        Calendar calendar1 = Calendar.getInstance();
        calendar1.set(2016,8,9,20,0,0);
        printCalendar(calendar1);   //2016年9月9日,星期5 20:0:0

        //add方法
        calendar1.add(Calendar.YEAR,2);  //calendar1加两年
        printCalendar(calendar1);  //2018年9月9日,星期日 20:0:0

        //getTime()
        Date d = calendar.getTime();
        System.out.println(d);  // Sat Jun 08 16:29:41 CST 2018
    }

    public static void printCalendar(Calendar calendar) {
        int year = calendar.get(Calendar.YEAR);
        int month = calendar.get(Calendar.MONTH) + 1;
        int day = calendar.get(Calendar.DAY_OF_MONTH);
        int date = calendar.get(Calendar.DAY_OF_WEEK) - 1; // 星期几
        String week = "" + ((date == 0) ? "日" : date);
        int hour = calendar.get(Calendar.HOUR_OF_DAY);
        int minute = calendar.get(Calendar.MINUTE);
        int second = calendar.get(Calendar.SECOND);
        System.out.printf("%d年%d月%d日,星期%s %d:%d:%d\n", year, month, day,
                week, hour, minute, second);
    }
}
```

## 5.Math类
java.lang.Math包含执行基本数字运算的方法，如基本指数，对数，平方根和三角函数。基本上学过的一些基本运算都包含。

计算高等数学中的相关内容，可以使用apache commons下面的Math类库。

随便写几个常用方法，其他的用的时候再查api

```java
public class TestMath {
    public static void main(String[] args) {
        //取整相关操作
        System.out.println(Math.ceil(3.2)); //向上取整  4.0
        System.out.println(Math.floor(3.2));  //向下取整  3.0
        System.out.println(Math.round(3.2));  //四舍五入  3
        System.out.println(Math.round(3.8));  //四舍五入  4
        //绝对值、开方、a的b次幂等操作
        System.out.println(Math.abs(-45));  //绝对值 45
        System.out.println(Math.sqrt(64));  //开根  8.0
        System.out.println(Math.pow(5, 2));  //5的平方  25.0
        System.out.println(Math.pow(2, 5));  //2的5次方  32.0
        //Math类中常用的常量
        System.out.println(Math.PI);  //3.141592653589793
        System.out.println(Math.E);  //2.718281828459045
        //[0,1)范围随机数
        System.out.println(Math.random());  //0.8817756624530653
    }
}
```

## 6.Random类
java.util.Random用来获取伪随机数

构造方法
- Random()   创建一个新的随机数对象。  
- Random(long seed)  使用一个种子数创建一个新的随机数对象。

在进行随机时，随机算法的起源数字称为种子数(seed)，在种子数的基础上进行一定的变换，从而产生需要的随机数字。


相同种子数的Random对象，相同次数生成的随机数字是完全相同的。也就是说，两个种子数相同的Random对象，第一次生成的随机数字完全相同，第二次生成的随机数字也完全相同。

再次强调：种子数只是随机算法的起源数字，和生成的随机数字的区间无关。

常用方法 
- nextInt()  获取一个随机数,它的范围是在int类型范围之内。
- nextInt(int n)   获取随机数,它的范围是在0到n之间，不包含n。

```java
public class Demo {
	public static void main(String[] args) {
		Random r = new Random(10);
		Random r1 = new Random(10);
		//初始种子数相同，产生的随机数完全相同
		for (int i = 0; i < 10; i++) {
			System.out.println(r.nextInt()+"====="+r1.nextInt());
		}

		Random r2 = new Random();
		//产生int范围内的随机数
		System.out.println(r2.nextInt());
		//产生[0,n)范围内的随机数，注意是左避右开
		System.out.println(r2.nextInt(10)); //4
	}
}
```
当然上面的示例只是整数，同样其他类型的数字也都可以，比如r.nextDouble(1),产生[0,1.0)区间的小数。

另外，生成任意非从0开始的小数区间[d1,d2)范围的随机数字(其中d1不等于0)，则只需要首先生成[0,d2-d1)区间的随机数字，然后将生成的随机数字区间加上d1即可。即double x = d1+r.r.nextDouble(d2-d1)。 整数同理