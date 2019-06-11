
# Java IO
## 概述


## 四大抽象基类
### InputStream

常用方法
```java
read() 读取单个字节 返回值为读取内容 返回类型int 结束返回-1
read(byte[])  读取一段byte数组长度的字节 返回值为读取长度   返回类型int  结束返回-1
read(byte b[], int off, int len) 从off处开始读取len长度字节数组 返回类型int  结束返回-1
```
因为InputStream是抽象类，我们就用它的子类FileInputStream进行测试
```java
package com.company.test;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;

public class testInputStream {
    public static void main(String[] args) {
        InputStream is = null;
        try {
            is = new FileInputStream("src/abc.txt");
            byte[] b = new byte[1024];  //每次读取1024个字节即1KB
            int len = -1;
            while((len=is.read(b))!=-1){
              String str = new String(b,0,b.length);  //解码 将字节数组转为字符串
              System.out.println(str);
            }

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            if(is!=null){
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}

```
### OutputStream
常用方法
```java
write() 写出单个字节 无返回值
write(byte[]) 写出字节数组 无返回值
write(byte[],int off,int len)  从off开始写出len长度字节
```
同样适用FileOutputStream进行测试
```java
package com.company.test;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

public class testOutputStream {
    public static void main(String[] args) {
        OutputStream os = null;
        try {
            os = new FileOutputStream("src/abc.txt");//默认是覆盖模式会覆盖原文件中的内容
            //OutputStream os = new FileOutputStream(dest,true);追加模式
            String str = "Hello IO";
            byte[] b = str.getBytes();  //编码 将字符串转为字节数组
            os.write(b);
            os.flush();  //记得手动flush
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            if(os!=null){
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

}
```

将两个流对接实现文件复制
```java
package com.company.test;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

public class FileCopy {
    public static void main(String[] args) {
        InputStream is = null;
        OutputStream os = null;
        try {
            is = new FileInputStream("src/abc.txt");
            os = new FileOutputStream("src/abccopy.txt");
            byte[] b = new byte[1024];
            int len = -1;
            while ((len=is.read(b))!=-1){
                os.write(b);
                os.flush();
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            if(is!=null){
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(os!=null){
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```
### Reader
```java
read() 读取单个字符 返回值为读取内容 返回类型int 结束返回-1
read(char[])  读取一段char数组长度的字符 返回值为读取长度  返回类型int  结束返回-1
read(char[] cbuf, int off, int len) 
```
适用FileReader进行测试 
```java
package com.company.test;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.io.Reader;

public class testReader {
    public static void main(String[] args) {
        Reader r = null;
        try {
            r = new FileReader("src/abc.txt");
            char[] c = new char[1024];
            int len = -1;
            while((r.read(c))!=-1){
                String str = new String(c,0,c.length);
                System.out.println(str);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            if(r!=null){
                try {
                    r.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```
### Writer
常用方法：
```java
write(int c) 写出单个字符 无返回值
write(byte[])  写出字符数组  无返回值
write(byte[],int off,int len)
//与字节输出流相比增加了写字符串方法
write(String str)  写一个字符串
write(String str, int off, int len) 

```
使用FileWriter测试：
```java
package com.company.test;

import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class testWriter {
    public static void main(String[] args) {
        Writer w = null;
        try {
            w = new FileWriter("src/abc1.txt");
            String str = "Hello 哈哈哈";
            char[] c = str.toCharArray();
            w.write(c);
            w.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            if(w!=null){
                try {
                    w.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```
字符流对接实现文件复制
```java
package com.company.test;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Reader;
import java.io.Writer;

public class FileCopy2 {
    public static void main(String[] args) {
        Reader r = null;
        Writer w = null;
        try {
            r = new FileReader("src/abc.txt");
            w = new FileWriter("src/abc_copy.txt");
            char[] c = new char[1024];
            int len = -1;
            while((len=r.read(c))!=-1){
                w.write(c);
                w.flush();
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            if(r!=null){
                try {
                    r.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(w!=null){
                try {
                    w.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```
## 
## CommonsIO
Apache-commons工具包中提供了IOUtils/FileUtils，封装了大量的IO流操作，可以让我们非常方便的对文件和目录进行操作

下载地址：http://commons.apache.org/proper/commons-io/      用的时候直接查API即可



# Java NIO
## 概述
Java NIO (New IO)指的是新的输入/输出库，是在 JDK 1.4 中引入的，弥补了原来的 I/O 的不足，提供了高速的、面向块的 I/O。

NIO主要有三大核心部分：**Channel(通道)**，**Buffer(缓冲区)**,**Selector(选择器)**。传统IO基于字节流和字符流进行操作，而NIO基于Channel和Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。Selector(选择区)用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个线程可以监听多个数据通道。

## NIO三大核心
### Channel
Channel和IO中的Stream(流)是差不多一个等级的。只不过Stream是单向的，譬如：InputStream, OutputStream.而Channel是双向的，既可以用来进行读操作，又可以用来进行写操作。

![Channel](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/Channel.png)

NIO中的Channel的主要实现有：
- FileChannel 从文件中读写数据

- DatagramChannel 能通过UDP读写网络中的数据

- SocketChannel 能通过TCP读写网络中的数据

- ServerSocketChannel 监听新进来的TCP连接，对每一个新进来的连接都会创建一个SocketChannel



### Buffer
Buffer可以理解为一块内存区域，可以写入数据，并且在之后读取它。这块内存被包装成NIO buffer对象，它提供了一些方法来更简单地操作内存。

NIO中的关键Buffer实现有：
- ByteBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer
- CharBuffer

分别对应基本数据类型: byte, char, double, float, int, long, short。当然NIO中还有MappedByteBuffer, HeapByteBuffer, DirectByteBuffer等这里先不进行陈述。

### Selector

Selector运行单线程处理多个Channel，如果你的应用打开了多个通道，但每个连接的流量都很低，使用Selector就会很方便。

![Selector](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/javaSE/img/Selector.png)

## IO和NIO的主要区别
1. **IO是面向流的而NIO是面向缓冲区的** 

Java IO面向流意味着每次从流中读一个或多个字节，直至读取所有字节，它们没有被缓存在任何地方。此外，它不能前后移动流中的数据。如果需要前后移动从流中读取的数据，需要先将它缓存到一个缓冲区。

NIO中数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动。这就增加了处理过程中的灵活性。但是，还需要检查是否该缓冲区中包含所有您需要处理的数据。而且，需确保当更多的数据读入缓冲区时，不要覆盖缓冲区里尚未处理的数据。

2. **IO是同步阻塞的而NIO是同步非阻塞的**

IO的各种流是阻塞的。这意味着，当一个线程调用read() 或 write()时，该线程被阻塞，直到有一些数据被读取，或数据完全写入。该线程在此期间不能再干任何事情了。

NIO的非阻塞模式，使一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取。而不是保持线程阻塞，所以直至数据变得可以读取之前，该线程可以继续做其他的事情。 非阻塞写也是如此。一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。 线程通常将非阻塞IO的空闲时间用于在其它通道上执行IO操作，所以一个单独的线程现在可以管理多个输入和输出通道（channel）。

3. **NIO是多路复用**
NIO的Selector选择器可以实现多路复用。多路复用是指使用单线程也可以通过轮询监控的方式实现多线程类似的效果。简单的说就是，通过选择机制，使用一个单独的线程很容易来管理多个通道。

## 