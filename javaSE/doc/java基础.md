
## 基本语法
### 数据类型
1. 基本数据类型
   - 整数型 byte short int long
   - 浮点数 float double
   - 字符型 char
   - 布尔型 boolean

2. 引用数据类型
字符串、数组、类、接口、Lambda表达式

![基本数据类型]()

注意：
- 字符串不是基本类型
- 浮点型可能只是一个近似值，并非精确值，所以尽可能避免两个浮点数进行比较，同时进行没有误差的精确数字可以使用java.math包中的**BigInteger**和**BigDecimal**类
- 数据范围与字节数不一定相关，例如float(4字节)数据范围比long(8字节)更广泛
- 整数默认为int，如果一定要使用long类型要在后面加上L。浮点数默认是double，如果一定要使用float类型后面加上F。


另外一个要注意的是**数据类型转换**
数据类型转换分为两种：自动类型转换和强制类型转换
- 自动类型转换
**按数据范围从小到大的顺序转换**，不需要进行特殊处理
注意float的数据范围比long类型大
```java
//int-->long
long a = 100;  //100

//float-->double
double b = 2.5F;  //2.5

//long-->float
float c = 100L;  //30.0
```
- 强制类型转换
**范围小的类型转为范围大的类型**，在需要转型的数据前加上“( )”，然后在括号内加入需要转化的数据类型
```java
//long-->int
int a =(int)100F;  //100

//long强转为int发生数据溢出
int b =(int)6000000000L //1705032704

//double强转为int发生精度损失
int c = (int)3.5;

char d = 'A';
System.out.println(d+1);  //66 大写字母'A'表示65 'a'表示97 '0'表示48
```
- 强制类型转换不推荐使用，因为有可能发生精度损失、数据溢出
- byte/short/char都可以发生数据运算，例如加法"+",发生运算时都会首先被提升为int类型
- boolean类型不能发生数据类型转换

### 运算符
![运算符]()

- 算数运算符中有不同类型数据时会进行自动数据类型转换后在进行计算。当然别忘了byte/short/char发生运算时都会首先被提升为int类型
- 右移一位相当于除以2（整除），左移一位相当于乘以2。
- &和&&最主要区别在于&&具有短路功能，即第一个表达式为false时则不再计算第二个表达式

**运算符优先级问题**
1. 没必要知道，需要的时候，使用（）表示运算先后顺序，使程序可读性强。

2. 逻辑与、逻辑或、逻辑非的优先级一定要熟悉！（逻辑非>逻辑与>逻辑或）。如：a||b&&c的运算结果是：a||(b&&c)，而不是(a||b)&&c 

### 控制语句
#### 选择结构
- if
```java
public class TestIf {
	public static void main(String[] args) {
		int x = 80;
		if(x<60) {
			System.out.println("D");
		}else if(x<75) {
			System.out.println("C");
		}else if(x<85) {
			System.out.println("B");
		}else {
			System.out.println("A");
		}
	}
}
```
- switch
```java
public class TestIf {
	public static void main(String[] args) {
		int x = 80;
        switch(x){
            case 60:
            System.out.println(60);
            break;
            case 70:
            System.out.println(70);
            break;
            default:
            System.out.println(">70");
            break;
        }
	}
}
```
#### 循环结构(注意break和continue)
- for
```java
//计算0~100的和
public class Demo {
	public static void main(String[] args) {
		int sum = 0;
		for(int i=0;i<=100;i++) {
			sum += i;			
		}
		System.out.println(sum);
	}
}
```
- while do
```java
//计算0~100的和
public class Demo {
	public static void main(String[] args) {
		int i = 0;
		int sum = 0;
		while(i <= 100) {
			sum += i;
			i++;
		}
		System.out.println(sum);
	}
}
```
- do while
```java
//计算0~100的和
public class Demo {
	public static void main(String[] args) {
		int i = 0;
		int sum = 0;
		do {
			sum += i;
			i++;
		}while(i <= 100);
		System.out.println(sum);
	}
}
```

### 方法(函数)
**方法的重载（overload）**：

重载的方法，实际是完全不同的方法，只是名称相同而已!

构成方法重载的条件：
- 不同的含义：形参类型、形参个数、形参顺序（首先要类型不同）不同
- 只有返回值不同不构成方法的重载
- 只有形参的名称不同，不构成方法的重载

```java
public class Demo {
    public static void main(String[] args) {
        System.out.println(add(3, 5));// 8
        System.out.println(add(3, 5, 10));// 18
        System.out.println(add(3.0, 5));// 8.0
        System.out.println(add(3, 5.0));// 8.0
        // 我们已经见过的方法的重载
        System.out.println();// 0个参数
        System.out.println(1);// 参数是1个int
        System.out.println(3.0);// 参数是1个double
    }
    /** 求和的方法 */
    public static int add(int n1, int n2) {
        int sum = n1 + n2;
        return sum;
    }
    // 方法名相同，参数个数不同，构成重载
    public static int add(int n1, int n2, int n3) {
        int sum = n1 + n2 + n3;
        return sum;
    }
    // 方法名相同，参数类型不同，构成重载
    public static double add(double n1, int n2) {
        double sum = n1 + n2;
        return sum;
    }
    // 方法名相同，参数顺序不同，构成重载
    public static double add(int n1, double n2) {
        double sum = n1 + n2;
        return sum;
    }
    //编译错误：只有返回值不同，不构成方法的重载
    public static double add(int n1, int n2) {
        double sum = n1 + n2;
        return sum;
    }
    //编译错误：只有参数名称不同，不构成方法的重载
    public static int add(int n2, int n1) {
        double sum = n1 + n2;         
        return sum;
    }  
}
```
## 面向对象
### 三大特征：封装、继承、多态
#### 封装
作用：提高代码的安全性和复用性。

“高内聚，低耦合”高内聚就是类的内部数据操作细节自己完成，不允许外部干涉;低耦合是仅暴露少量的方法给外部使用，尽量方便外部调用

实现：使用修饰控制符private，default（不写默认是default），protected，public

1. private 只能被**本类**访问
2. default  没有修饰符修饰，被**本类以及同一个包中的其他类**访问
3. protected 被**本类、同一个包中的类、以及不同包中的子类**访问
4. public  被**该项目的所有包中的所有类**访问

一般是将属性私有，然后提供相应的set和get方法
```java
public class Student {
    //属性
    private int id;
    private String name;
    private int age;
    
    //构造器(不写,默认会有一个无参构造器,但是只要写了有参构造器则不会有无参构造器)
    //无参构造器
    public Student() {
    }
    //有参数构造器
    public Student(int id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    //set和get方法
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```
#### 继承
作用：容易实现类的扩展，提高代码的复用性。

注意：
- java中类只有单继承，接口有多继承
- 子类继承父类，可以得到父类的全部属性和方法 (除了父类的构造方法),但不见得可以直接访问(比如，父类私有的属性和方法)。
- Object类是所有Java类的根基类，也就意味着所有的Java对象都拥有Object类的属性和方法。如果在类的声明中未使用extends关键字指明其父类，则默认继承Object类
- instanceof运算符，左边是对象，右边是类；当对象是右面类或子类所创建对象时，返回true；否则，返回false。
- 注意方法重写和重载的区别。

```java

```

#### 多态

### 抽象方法、抽象类和接口
#### 抽象方法和抽象类

### 接口

### 内部类
## Java常用类
### 0.数组
#### 基本知识点
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
        int[][] b = {{1,2},{3,4,5},{6,7,8,9}};


        System.out.println(str);  //直接输出的是地址，可以循环输出或者使用Arrays工具类
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
#### 数组常用工具类:
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
### 1.String类
#### 基本知识点
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
#### StringBuilder和StringBuffer
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

#### String Pool
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
2. 对于引用数据类型,==比较的是两个的地址是否相同，如果要比较值是否相同，需要使用equals方法。

字符串常量池（String Pool）保存着所有字符串字面量（literal strings）,这些字面量在编译时期就确定。如果是以String str = "aaa"这种方式创建,字符串常量池中已经有了aaa则会将字符串常量池中的引用返回给str1和str2，自然两者相等。

使用String str3 = new String("aaa");这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 "aaa" 字符串对象）。

- "aaa" 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 "aaa" 字符串字面量；
- 而使用 new 的方式会在堆中创建一个字符串对象。

另外一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。
```java
String str4 = str3.intern();
System.out.println(str1==str4);  //true
```


### 2.包装类
在实际应用中经常需要将基本数据转化成对象，以便于操作，比如将数据放入Object数组中，所以Java中每个基本类型都对应有一个包装类型。

除了int--Integer和char--Character外，其他基本类型的包装类都是首字母大写

#### 自动装箱与拆箱
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
#### 缓存池
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

### 3.Object类
#### 概览
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

#### equals()
#### hashCode()
### 4.时间相关类

### 5.Math类

### 6.Random类

## 