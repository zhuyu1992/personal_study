# java学习

## 1.IO

io的四种，基于字节，基于字符，基于文件，基于网络

```
/**
 * @Author:zzzgyu
 * 创建文件夹
 * mkdirs()创建文件夹以及所有父文件夹
 * mkdir()创建文件夹
 */

public class CreateDir {
    public static void main(String[] args) {
        File file = new File("F:\\love\\java\\b.text");
        boolean b = file.mkdirs();
        System.out.println(b);
    }
}

```

```
public class ScannerDemo {
    public static void main(String[] args) {
        /**
         * next() 与 nextLine() 区别
         * next() 不能得到带有空格的字符串
         * nextLine()以Enter为结束符,也就是说 nextLine()方法返回的是输入回车之前的所有字符
         */
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入：");
//        if (scanner.hasNext()) {
//            String next = scanner.next();
//            System.out.println(next);
//        }
//        scanner.close();
//        if (scanner.hasNextLine()) {
//            String s = scanner.nextLine();
//            System.out.println(s);
//        }
        if (scanner.hasNextInt()) {
            int i = scanner.nextInt();
            System.out.println(i);
        }else {
            System.out.println("不是整数");
        }
        scanner.close();
    }
}
```

![59127127278](C:\Users\Administrator\Desktop\安装\arrest\1591271272782.png)



```
public class FileIO {
    public static void main(String[] args) throws IOException {
        File file = new File("F:\\love\\java\\day\\aaa.txt");
        FileInputStream fileInputStream = new FileInputStream(file);
        BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
        /**
         * 读取文件，并输出到控制台
         */
//        Scanner scanner = new Scanner(fileInputStream);
//        while (scanner.hasNextLine()) {
//            String s = scanner.nextLine();
//            System.out.println(s);
//        }
        File file1 = new File("F:\\love\\bbb.txt");
        FileOutputStream fileOutputStream = new FileOutputStream(file1);
        BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(fileOutputStream);
         try {
             byte[] bytes = new byte[24];
             int len;
             while ((len = bufferedInputStream.read(bytes))!=-1){
                 System.out.println(len);
                 bufferedOutputStream.write(bytes,0,len);
             }
          } catch (Exception e) {
              e.printStackTrace();

          }finally {
             bufferedInputStream.close();
             bufferedOutputStream.close();
         }




    }
}
```

## 2.NIO(Buffer,channel,selector)

在NIO中，我们处理I/O操作的**通道**和**缓冲区**。在基于通道的NIO中，数据传输的基本单位是缓冲器缓冲区具有确定其可以包含的数据的上限的固定容量。

在基于通道的I/O中，我们将数据写入缓冲区，并将该缓冲区传递到写入数据的通道。

为了从数据源读取数据，我们向一个通道传递一个缓冲区。通道将数据从数据源读入缓冲区。

![59128706318](C:\Users\Administrator\Desktop\安装\arrest\1591287063186.png)

### 2.1Buffer

![59127154512](C:\Users\Administrator\Desktop\安装\arrest\1591271545124.png)

![59127156943](C:\Users\Administrator\Desktop\安装\arrest\1591271569431.png)

```
/**
 * @Author:zzzgyu
 * 缓冲区（七种）
 * ByteBuffer
 * CharBuffer
 * ShortBuffer
 * IntBuffer
 * LongBuffer
 * FloatBuffer
 * DoubleBuffer
 *
 * 属性
 * Capacity（容量）
 * Position
 * Limit
 * Mark
 */

public class NioDemo01 {
    public static void main(String[] args) {
        ByteBuffer byteBuffer = ByteBuffer.allocate(8);
        System.out.println(byteBuffer.capacity());
        System.out.println(byteBuffer.position());
        System.out.println(byteBuffer.limit());
        System.out.println(byteBuffer.mark());
        System.out.println("-----------------------------");
        for (int i=60;i<68;i++){
            byteBuffer.put((byte) i);
        }
        System.out.println(byteBuffer.capacity());
        System.out.println(byteBuffer.position());
        System.out.println(byteBuffer.limit());
        System.out.println(byteBuffer.mark());
        for (int i = 0; i < byteBuffer.limit(); i++) {

            System.out.println(byteBuffer.get(i)+"\n");
        }
        System.out.println("+++++++++++++++++++++++");
        byteBuffer.flip();
        byteBuffer.position(3);
        System.out.println(byteBuffer.get());

    }
}
```



### 2.2Channel

```
 FileChannel fileChannel = fileInputStream.getChannel();
 
 FileChannel channel = outputStream.getChannel();
```

```
/**
 * @Author:zzzgyu
 * 通过通道和缓冲区读取数据
 */

public class NioDemo02 {
    public static void main(String[] args) {
        File file = new File("F:\\love\\java\\day\\aaa.txt");
         try {
             FileInputStream fileInputStream = new FileInputStream(file);
             FileChannel fileChannel = fileInputStream.getChannel();
             ByteBuffer byteBuffer = ByteBuffer.allocate(10);

//             int i = fileChannel.read(byteBuffer);
//             int position = byteBuffer.position();
//             System.out.println(position);
//             System.out.println(i);
             while (fileChannel.read(byteBuffer)>0) {
                 System.out.println("第一层循环");
                 byteBuffer.flip();
                 while (byteBuffer.hasRemaining()) {
                     System.out.println("第二层循环");
                     byte b = byteBuffer.get();
                     System.out.println((char)b);
                 }
                 byteBuffer.clear();
             }

         } catch (Exception e) {
             e.printStackTrace();

         }
    }
}
```

```
/**
 * @Author:zzzgyu
 * 通过通道和缓冲取写数据到文件
 */

public class NioDemo03 {
    public static void main(String[] args) {

        File file = new File("F:\\love\\bbb.txt");
        try {

            FileOutputStream fileOutputStream = new FileOutputStream(file);
            FileChannel outputStreamChannel = fileOutputStream.getChannel();
            String text ="aaaaaabbbbbbbbbbcccccccccc中国人";
            byte[] bytes = text.getBytes("UTF-8");
//            int length = bytes.length;
//            for (int i = 0; i < length; i++) {
//                System.out.println((char) bytes[i]);
//            }
            ByteBuffer byteBuffer = ByteBuffer.wrap(bytes);
            outputStreamChannel.write(byteBuffer);
            outputStreamChannel.close();

        } catch (Exception e) {
            e.printStackTrace();

        }

    }
}
```

```
/**
 * @Author:zzzgyu
 * 文件复制
 */

public class NioDemo04 {
    public static void main(String[] args) {
        File infile = new File("F:\\love\\java\\day\\aaa.txt");
        File outfile = new File("F:\\love\\java\\day\\bbb.txt");
         try {
             FileInputStream fileInputStream = new FileInputStream(infile);
             FileOutputStream fileOutputStream = new FileOutputStream(outfile);
             FileChannel inputStreamChannel = fileInputStream.getChannel();
             FileChannel outputStreamChannel = fileOutputStream.getChannel();

            inputStreamChannel.transferTo(0,inputStreamChannel.size(),outputStreamChannel);

         } catch (Exception e) {
             e.printStackTrace();

         }

    }
}
```



### 2.3Selector

Channel注册到Selector，Selector能够检测到Channel是否有事件发生。如果有事件发生，则进行相应的处理。这样可以实现一个线程管理多个Channel（即多个连接和请求）

服务器端

```
/**
 * @Author:zzzgyu
 */

public class NioServer {
    public static void main(String[] args) {
        try {
            //获取serverSocketChannel
            ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
            //获取selector
            Selector selector = Selector.open();
            //绑定ip端口
            serverSocketChannel.socket().bind(new InetSocketAddress(8888));
            // 设置为不阻塞
            serverSocketChannel.configureBlocking(false);
            // 将ServerSocketChannel注册到selector。指定关心的事件为OP_ACCEPT，
            // 当有关心的事件发生时，会返回这个SelectionKey，通过SelectionKey可以拿到Channel
            serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
            while (true) {
                // Selector监听，等于0说明此时没有事件发生。
                if (selector.select(1000) == 0) {
                    System.out.println("Selector监听了一秒");
                    continue;
                }
                Set<SelectionKey> keys = selector.selectedKeys();
                Iterator<SelectionKey> keyIterator = keys.iterator();
                while (keyIterator.hasNext()) {
                    SelectionKey key = keyIterator.next();
                    if (key.isAcceptable()) {
                        // 获得SocketChannel，此处的accept不会阻塞
                        SocketChannel socketChannel = serverSocketChannel.accept();
                        // 此处socketChannel也要设置为非阻塞模式
                        socketChannel.configureBlocking(false);
                        // 注册Selector。第三个参数是连接的对象，通过SelectionKey可以连接到这个对象
                        socketChannel.register(selector, SelectionKey.OP_READ, ByteBuffer.allocate(1024));
                    }
                    if (key.isReadable()) {
                        SocketChannel channel = (SocketChannel) key.channel();
                        ByteBuffer buffer = (ByteBuffer) key.attachment();
                        int read = channel.read(buffer);
                        System.out.println("客户端 : " + new String(buffer.array(), 0, read));
                    }
                    // 手动删除避免重复
                    keyIterator.remove();
                }
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

客户端

```
public class NioClient {
    public static void main(String[] args) {
        try {
            SocketChannel socketChannel = SocketChannel.open();

            socketChannel.configureBlocking(false);

            boolean connect = socketChannel.connect(new InetSocketAddress("127.0.0.1", 8888));
            if (!connect) {
                while (!socketChannel.finishConnect()) {
                    System.out.println("因为连接需要时间，客户端不会阻塞，可以做一些其他工作");
                }
            }
            ByteBuffer byteBuffer = ByteBuffer.wrap("我是一个中国人".getBytes("UTF-8"));
            socketChannel.write(byteBuffer);
            System.in.read();


        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

![59133590150](C:\Users\Administrator\Desktop\安装\arrest\1591335901509.png)





![59133581907](C:\Users\Administrator\Desktop\安装\arrest\1591335819078.png)





## 3.修饰符

![59038272251](C:\Users\Administrator\Desktop\安装\arrest\1590382722510.png)

![59038320627](C:\Users\Administrator\Desktop\安装\arrest\1590383206270.png)

## 4.netty

### 4.1Netty 快速入门

**(BOOTSTRAP,CHANNEL,CHANNELHANDLER,CHANNELPIPELINE,EVENTLOOP,CHANNELFUTURE)**

下文中介绍的是Netty应用程序的全部基本构建模块，其中客户端和服务器也包含在内。

1.BOOTSTRAP
Netty 应用程序通过设置 bootstrap（引导）类的开始，该类提供了一个 用于应用程序网络层配置的容器。

2.CHANNEL
底层网络传输 API 必须提供给应用 I/O操作的接口，如读，写，连接，绑定等等。对于我们来说，这是结构几乎总是会成为一个“socket”。 Netty 中的接口 Channel 定义了与 socket 丰富交互的操作集：bind, close, config, connect, isActive, isOpen, isWritable, read, write 等等。 Netty 提供大量的 Channel 实现来专门使用。这些包括 AbstractChannel，AbstractNioByteChannel，AbstractNioChannel，EmbeddedChannel， LocalServerChannel，NioSocketChannel 等等。

3.CHANNELHANDLER
ChannelHandler 支持很多协议，并且提供用于数据处理的容器。我们已经知道 ChannelHandler 由特定事件触发。 ChannelHandler 可专用于几乎所有的动作，包括将一个对象转为字节（或相反），执行过程中抛出的异常处理。

常用的一个接口是 ChannelInboundHandler，这个类型接收到入站事件（包括接收到的数据）可以处理应用程序逻辑。当你需要提供响应时，你也可以从 ChannelInboundHandler 冲刷数据。一句话，业务逻辑经常存活于一个或者多个 ChannelInboundHandler。

4.CHANNELPIPELINE
ChannelPipeline 提供了一个容器给 ChannelHandler 链并提供了一个API 用于管理沿着链入站和出站事件的流动。每个 Channel 都有自己的ChannelPipeline，当 Channel 创建时自动创建的。 ChannelHandler 是如何安装在 ChannelPipeline？ 主要是实现了ChannelHandler 的抽象 ChannelInitializer。ChannelInitializer子类 通过 ServerBootstrap 进行注册。当它的方法 initChannel() 被调用时，这个对象将安装自定义的 ChannelHandler 集到 pipeline。当这个操作完成时，ChannelInitializer 子类则 从 ChannelPipeline 自动删除自身。

5.EVENTLOOP
EventLoop 用于处理 Channel 的 I/O 操作。一个单一的 EventLoop通常会处理多个 Channel 事件。一个 EventLoopGroup 可以含有多于一个的 EventLoop 和 提供了一种迭代用于检索清单中的下一个。

6.CHANNELFUTURE
Netty 所有的 I/O 操作都是异步。因为一个操作可能无法立即返回，我们需要有一种方法在以后确定它的结果。出于这个目的，Netty 提供了接口 ChannelFuture,它的 addListener 方法注册了一个 ChannelFutureListener ，当操作完成时，可以被通知（不管成功与否）。

![59137349388](C:\Users\Administrator\Desktop\安装\arrest\1591373493886.png)

```
<dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.1.20.Final</version>
        </dependency>

```











## 5.java8全新stream（对集合的操作）



![59214498157](C:\Users\Administrator\Desktop\安装\arrest\1592144981576.png)

![59214500245](C:\Users\Administrator\Desktop\安装\arrest\1592145002453.png)

![59214501794](C:\Users\Administrator\Desktop\安装\arrest\1592145017944.png)

![59214503325](C:\Users\Administrator\Desktop\安装\arrest\1592145033251.png)

![59214504725](C:\Users\Administrator\Desktop\安装\arrest\1592145047256.png)

![59214506274](C:\Users\Administrator\Desktop\安装\arrest\1592145062740.png)

![59214507504](C:\Users\Administrator\Desktop\安装\arrest\1592145075042.png)

![59214508827](C:\Users\Administrator\Desktop\安装\arrest\1592145088278.png)



## 6.对象实例化过程

![60237993688](C:\Users\Administrator\Desktop\安装\arrest\1602379936888.png)

![60237988734](C:\Users\Administrator\Desktop\安装\arrest\1602379887341.png)

## 7.字符串创建

![60238986068](C:\Users\Administrator\Desktop\安装\arrest\1602389860684.png)











# Java高级

## 1.redis实现分布式锁（redis单线程）

![59214900440](C:\Users\Administrator\Desktop\安装\arrest\1592149004401.png)

## 2.利用redission实现分布式锁

![59214934144](C:\Users\Administrator\Desktop\安装\arrest\1592149341443.png)

![59214959914](C:\Users\Administrator\Desktop\安装\arrest\1592149599141.png)



![59214952383](C:\Users\Administrator\Desktop\安装\arrest\1592149523837.png)



```java
//获得锁
RLock redissonLock = redisson.getLock(lockKey);
//加锁
redissonLock.lock();  

//解锁
redissonLock.unlock();
```

## 3.redis缓存穿透

![59262069375](C:\Users\Administrator\Desktop\安装\arrest\1592620693750.png)



![59262335949](C:\Users\Administrator\Desktop\安装\arrest\1592623359494.png)















# JUC(java多线程)

![59222927417](C:\Users\Administrator\Desktop\安装\arrest\1592229274179.png)

进程与线程

进程：qq 线程：你打开的聊天框

并发与并行

并发：多个线程抢同一资源，抢票

并行：

并发同一个时间段内多个线程交替，并行一个时间点多个线程同时执行。

```java

public class ThreadDemo01 extends Thread {
    @Override
    public void run() {

        System.out.println(Thread.currentThread().getName()+"------------"+"干活啦！！！！！！！");
    }
}



public class ThreadDemo02 implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"--------------"+"!!!!!!!!!!!!!");
    }

}



public class Test {
    public static void main(String[] args) {
        //1.继承Thread类实现多线程
       /* ThreadDemo01 threadDemo01 = new ThreadDemo01();
        ThreadDemo01 threadDemo02 = new ThreadDemo01();
        ThreadDemo01 threadDemo03 = new ThreadDemo01();
        threadDemo01.start();
        threadDemo02.start();
        threadDemo03.start();*/
        //2.实现runnable接口，多线程
       /* ThreadDemo02 threadDemo02 = new ThreadDemo02();
        Thread thread = new Thread(threadDemo02);
        Thread thread2 = new Thread(threadDemo02);
        Thread thread3 = new Thread(threadDemo02);
        thread.start();
        thread2.start();
        thread3.start();*/

    }
}


```



## 1.买票问题

**多线程编程口诀： 线程    操作  资源类**

![59223765230](C:\Users\Administrator\Desktop\安装\arrest\1592237652300.png)

线程状态

wait 释放锁， sleep 不释放锁

```
NEW,RUNNABLE,BLOCKED WAITING TIMED_WAITING TERMINATED
```

```java
/**
 * @Author:zzzgyu 线程   操作   资源类
 * 3个售票员   30张票 
 */

class Ticket {//资源类
    private static int t = 30;

    private Lock lock = new ReentrantLock(); //加锁

    public void saleTicket() {

        lock.lock();
        try {
            if (t > 0) {
                System.out.println(Thread.currentThread().getName() + "\n" + "卖出了第" + (t--) + "还剩下：" + t);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }


    }
}


public class SaleTicket {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();

        new Thread(() -> {for(int i=1;i<40;i++) ticket.saleTicket();},"A").start();
        new Thread(() -> {for(int i=1;i<40;i++) ticket.saleTicket();},"B").start();
        new Thread(() -> {for(int i=1;i<40;i++) ticket.saleTicket();},"C").start();
        /*new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 40; i++) {
                    ticket.saleTicket();
                }
            }
        }, "A").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 40; i++) {
                    ticket.saleTicket();
                }
            }
        }, "B").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 40; i++) {
                    ticket.saleTicket();
                }
            }
        }, "C").start();*/
    }
}

```

## 2.lambda表达式(重要)

```java
/**
 * @Author:zzzgyu
 *
 * 口决：拷贝小括号，写死右箭头，落地大括号
 *
 * @FunctionalInterface
 *
 * 用default 修饰，可以在接口中写方法语句；
 * 
 * 静态方法实现
 */
@FunctionalInterface
interface Foo{  //函数式接口，有且仅有一个方法
   //void sayHello();
    void sayHai(int x,int y);

    default void div(int x, int y) {
        System.out.println(x/y);
    }

    public static void mav(int x, int y) {
        System.out.println(x*y);
    }
}

public class LambdaExpressDemo {
    public static void main(String[] args) {
        /*Foo foo = new Foo() {
            @Override
            public void sayHello() {
                System.out.println("--------------");
            }
        };
        foo.sayHello();*/
        //Foo foo = () -> System.out.println("-----------");
        //foo.sayHello();
        //有参数
        Foo foo = (x,y) -> System.out.println(x+y);
        foo.sayHai(1,2);
        //用default 修饰，可以在接口中写方法语句；
        foo.div(8,4);
        //静态方法实现
        Foo.mav(4,5);
    }

}

```

## 3.生产者与消费者问题

![59227215626](C:\Users\Administrator\Desktop\安装\arrest\1592272156263.png)



```java
/**
 * @auther: zzzgyu
 * 线程间通信问题
 * 判断   干活   通知
 * 防止虚假唤醒，多线程判断用while,不用if
 */
class Num {
    private static int t = 0;

    public synchronized void add() throws InterruptedException {
        //判断
        while (t!=0){
            this.wait();
        }
        //干活
        t++;
        System.out.println(Thread.currentThread().getName()+"--------------"+t);
        //通知
        this.notifyAll();
    }

    public synchronized void reduce() throws InterruptedException {
        while (t==0){
            this.wait();
        }
        t--;
        System.out.println(Thread.currentThread().getName()+"--------------"+t);
        this.notifyAll();
    }
}

public class ThreadNotifyDemo1 {

    public static void main(String[] args) {

        Num num = new Num();

        new Thread(() ->{for (int i=0;i<10;i++) {
            try {
                num.add();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        },"A").start();
        new Thread(() ->{for (int i =0; i<10;i++) {
            try {
                num.reduce();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        },"B").start();
        new Thread(() ->{for (int i=0;i<10;i++) {
            try {
                num.add();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        },"C").start();
        new Thread(() ->{for (int i =0; i<10;i++) {
            try {
                num.reduce();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        },"D").start();
    }
}

```

## 4.生产者与消费者问题（新写法）



![59227404902](C:\Users\Administrator\Desktop\安装\arrest\1592274049023.png)



```java
package cn.allweing.juc.ThreadNotify;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @auther: zzzgyu
 */
class Num2 {
    private static int t = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();


    public void add() throws InterruptedException {
        lock.lock();
        try {
            while (t != 0) {
                //this.wait();
                condition.await();
            }
            //干活
            t++;
            System.out.println(Thread.currentThread().getName() + "--------------" + t);
            //通知
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
        /*//判断
        while (t != 0) {
            this.wait();
        }
        //干活
        t++;
        System.out.println(Thread.currentThread().getName() + "--------------" + t);
        //通知
        this.notifyAll();*/
    }

    public void reduce() throws InterruptedException {
        lock.lock();
        try {
            while (t == 0) {
                condition.await();
            }
            t--;
            System.out.println(Thread.currentThread().getName() + "--------------" + t);
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
           lock.unlock();
        }
        /*while (t == 0) {
            this.wait();
        }
        t--;
        System.out.println(Thread.currentThread().getName() + "--------------" + t);
        this.notifyAll();*/
    }
}

public class ThreadNotifyDemo2 {
    public static void main(String[] args) {

        Num2 num = new Num2();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    num.add();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    num.reduce();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    num.add();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "C").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    num.reduce();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "D").start();
    }
}

```

## 5多线程顺序调用

![59227543082](C:\Users\Administrator\Desktop\安装\arrest\1592275430820.png)



```java
/**
 * @auther: zzzgyu
 * 标志位
 * 线程之间顺序调用
 */
class Num3{

    private int mark = 1;//标志位

    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();
    public void print5() {
        lock.lock();
        try {

            while (mark != 1) {
                try {
                    condition1.await();
                } catch (InterruptedException ex) {
                    ex.printStackTrace();
                }
            }

            for (int i = 0; i <= 5; i++) {

                System.out.println(Thread.currentThread().getName()+"--------------555555555");
            }
            mark=2;
            condition2.signal();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void print10() {
        lock.lock();
        try {

            while (mark != 2) {
                try {
                    condition1.await();
                } catch (InterruptedException ex) {
                    ex.printStackTrace();
                }
            }

            for (int i = 0; i <= 10; i++) {

                System.out.println(Thread.currentThread().getName()+"--------------10101010");
            }
            mark=3;
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void print15() {
        lock.lock();
        try {

            while (mark != 3) {
                try {
                    condition1.await();
                } catch (InterruptedException ex) {
                    ex.printStackTrace();
                }
            }

            for (int i = 0; i <= 15; i++) {

                System.out.println(Thread.currentThread().getName()+"--------------1515151515515");
            }
            mark=1;
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
public class ThreadNotifyDemo3 {
    public static void main(String[] args) {
        Num3 num3 = new Num3();

        new Thread(()->{num3.print5();},"AA").start();
        new Thread(()->{num3.print10();},"BB").start();
        new Thread(()->{num3.print15();},"CC").start();

    }
}
```

## 6八锁多线程

![59229786672](C:\Users\Administrator\Desktop\安装\arrest\1592297866721.png)





![59229880790](C:\Users\Administrator\Desktop\安装\arrest\1592298807907.png)

```
synchronized 锁当前this对象

static synchronized 锁的是class对象
```

```
class Phone {
    public synchronized void sendSms() {
        try { TimeUnit.SECONDS.sleep(4); } catch (InterruptedException e) { e.printStackTrace();}
        //try { TimeUnit.SECONDS.sleep(4); } catch (InterruptedException e) { e.printStackTrace();}
        //Thread.sleep(4000);
        System.out.println("Sms");
    }

    public synchronized void sendEmail() {
        System.out.println("Email");
    }

    public void sayHello() {
        System.out.println(Thread.currentThread().getName()+"hello");
    }
}

public class Lock8 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            phone.sendSms();
        }, "AA").start();
        Thread.sleep(2000);
        new Thread(() -> {
            //phone.call();
            //phone.sayHello();
            phone2.sendEmail();
        }, "BB").start();
    }
}
```

## 7.集合不安全(list,set,map)

```java
public class NotSafeDemo1 {

    public static void main(String[] args) {

        List<String> list =new CopyOnWriteArrayList<>(); //Collections.synchronizedList(new ArrayList<>()); //new Vector<>(); //new ArrayList<>();

        for (int i = 1; i <= 30; i++) {
            new Thread(()->{list.add(UUID.randomUUID().toString().substring(0,8));
                System.out.println(Thread.currentThread().getName()+"\t"+list);},String.valueOf(i)).start();
            //try { TimeUnit.SECONDS.sleep(1);} catch (InterruptedException e) {e.printStackTrace();}

        }
    }
}

```

![59231462077](C:\Users\Administrator\Desktop\安装\arrest\1592314620771.png)

set:

```java
 Set<String> hashSet = new CopyOnWriteArraySet<String>();  //new HashSet<>();

hashset 底层是hashmap K健是存的值，v是固定对象object
```

map:

```java
Map<String, String> concurrentHashMap = new ConcurrentHashMap<>();

hashMap 底层：node类型数组，链表，红黑树


```

## 8.多线程第三种方式callable

![59237269105](C:\Users\Administrator\Desktop\安装\arrest\1592372691055.png)



```java
class Call implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        System.out.println("...............come in");
        return 1024;
    }
}

public class ThreadDemo3 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask futureTask = new FutureTask(new Call());

        new Thread(futureTask,"A").start();//只调用一次，结果复用
        new Thread(futureTask,"b").start();
        System.out.println(".............main");
        System.out.println(futureTask.get());  //放在最后
    }
}

```

![59238374575](C:\Users\Administrator\Desktop\安装\arrest\1592383745754.png)



## 9.JUC工具类

###9.1CountDownLatch

```java
/**
 * @auther: zzzgyu
 * 实现main最后离开教室
 */

public class CountDownLachDemo {
    public static void main(String[] args) {

        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"离开了教室");
            }).start();
        }
        System.out.println(Thread.currentThread().getName()+"离开教室");

    }
}
```

![59238460715](C:\Users\Administrator\Desktop\安装\arrest\1592384607155.png)

```java
public class CountDownLachDemo {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"离开了教室");
                countDownLatch.countDown();
            }).start();
        }
        countDownLatch.await();
        System.out.println(Thread.currentThread().getName()+"离开教室");

    }
}
```

![59238482027](C:\Users\Administrator\Desktop\安装\arrest\1592384820276.png)



### 9.2CyclicBarrie

```java
/**
 * @auther: zzzgyu
 * 等人到齐了才开会
 */

public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("*********召唤神龙");
        });
        for (int i = 1; i <= 7; i++) {
            final int tem = i;
            new Thread(()->{
                System.out.println("收集到第"+tem+"龙珠");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }


    }
}

```

###9.3Semaphore

```java
/**
 * @auther: zzzgyu
 * 抢车位
 */

public class SemaphoreDemo {

    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);  //设置三个车位
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire(); //抢了一个车位
                    System.out.println(Thread.currentThread().getName()+"抢到了车位");
                    try { TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
                    System.out.println(Thread.currentThread().getName()+"离开了车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();//释放车位，其他线程又抢
                }
            },String.valueOf(i)).start();
        }
    }
}

```

### 9.4ReadWriteLock

![59240638268](C:\Users\Administrator\Desktop\安装\arrest\1592406382685.png)

```java
/**
 * @auther: zzzgyu
 * 读写锁
 */
class MyCache{
    private volatile Map<String, Object> hashMap = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void put(String k, Object v) {

        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"正在写入数据"+k);
            hashMap.put(k, v);
            System.out.println(Thread.currentThread().getName()+"写入数据完成");
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            readWriteLock.writeLock().unlock();
        }


    }

    public void get(String k) {
        readWriteLock.readLock().lock();

        try {
            System.out.println(Thread.currentThread().getName()+"正在读取数据");
            Object o = hashMap.get(k);
            System.out.println(Thread.currentThread().getName()+"读取数据完成"+o);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            readWriteLock.readLock().unlock();
        }

    }

}

public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        for (int i = 0; i < 11; i++) {
            final int tem=i;
            new Thread(()->{
                myCache.put(tem+"",tem+"sss");
            }).start();
        }

        for (int i = 0; i < 11; i++) {
            final int tem=i;
            new Thread(()->{
                myCache.get(tem+"");
            }).start();
        }
    }
}

```

### 9.5阻塞队列

![59240689793](C:\Users\Administrator\Desktop\安装\arrest\1592406897934.png)



![59240692074](C:\Users\Administrator\Desktop\安装\arrest\1592406920740.png)



![59240852360](C:\Users\Administrator\Desktop\安装\arrest\1592408523609.png)

```java
/**
 * @auther: zzzgyu
 */

public class BlockQueueDemo {
    public static void main(String[] args) throws InterruptedException {

        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);
        /**
        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));

        //System.out.println(blockingQueue.add("x")); //抛出异常
        System.out.println(blockingQueue.element());

        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());

        //System.out.println(blockingQueue.remove()); //抛出异常

        */

        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("a",3l, TimeUnit.SECONDS)); //true true true 3s后返回false


    }
}
```

## 10.TestTransferValue

(基本类型传值，其他类型传引用，String 为不可变)

![59240962114](C:\Users\Administrator\Desktop\安装\arrest\1592409621149.png)

## 11.(重点)ThreadPool(线程池)

```java
public class ThreadPoolDemo {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(5);//固定
        ExecutorService executorService1 = Executors.newSingleThreadExecutor();//一个
        ExecutorService executorService2 = Executors.newCachedThreadPool();//缓存


        for (int i = 1; i <= 10; i++) {
            //try { TimeUnit.SECONDS.sleep(1);} catch (InterruptedException e) {e.printStackTrace();}
            executorService2.execute(()->{
                System.out.println(Thread.currentThread().getName()+"办理业务");
            });
        }
    }
}
```

实际上就一个ThreadPoolExecutor（七大参数）

```java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = System.getSecurityManager() == null ?
                null :
                AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

![59244533739](C:\Users\Administrator\Desktop\安装\arrest\1592445337396.png)



```
/**
 * @auther: zzzgyu
 */

public class ThreadPoolDemo {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(5);//固定
        ExecutorService executorService1 = Executors.newSingleThreadExecutor();//一个
        ExecutorService executorService2 = Executors.newCachedThreadPool();//缓存


        for (int i = 1; i <= 10; i++) {
            //try { TimeUnit.SECONDS.sleep(1);} catch (InterruptedException e) {e.printStackTrace();}
            executorService2.execute(()->{
                System.out.println(Thread.currentThread().getName()+"办理业务");
            });
        }
    }
}

```



```java
public class ThreadPoolDemo2 {
    public static void main(String[] args) {
        System.out.println(Runtime.getRuntime().availableProcessors());//获取cpu核数
        ExecutorService executorService = new ThreadPoolExecutor(2,
                5,//一般cpu核数加1
                3L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3), Executors.defaultThreadFactory(),new ThreadPoolExecutor.DiscardOldestPolicy());//四个拒绝策略

                try {
                    for (int i = 0; i < 15; i++) {
                        executorService.execute(()->{

                            System.out.println(Thread.currentThread().getName()+"办理业务");

                        });
                    };
                } catch (Exception e) {
                    e.printStackTrace();
                }finally {
                    executorService.shutdown();
                }

    }
}
```

![59245651410](C:\Users\Administrator\Desktop\安装\arrest\1592456514107.png)



## 12.四大函数式接口

![59246322898](C:\Users\Administrator\Desktop\安装\arrest\1592463228982.png)

```java
 Predicate<String> predicate = new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return false;
            }
        };
        System.out.println(predicate.test("mm"));


  Function<String,String> function = new Function<String, String>() {
            @Override
            public String apply(String s) {

                return "mmm";
            }
        };
        System.out.println(function.apply("qq"));


Consumer<String> consumer = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        consumer.accept("a");


 Supplier<String> supplier = new Supplier<String>() {
            @Override
            public String get() {
                return "mm";

            }
        };
```

## 13.异步回调

```java
/**
 * @auther: zzzgyu
 * 异步回调
 */

public class CompletableFutureDemo {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
       /* CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "没有返回值");
        });
        completableFuture.get();*/


        CompletableFuture<Integer> completableFuture1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "有返回值************");
            //int a=10/0;
            return 1024;
        });
        CompletableFuture<Integer> exceptionally = completableFuture1.whenComplete((t, u) -> {
            System.out.println("成功：" + t); //成功 t 返回 1024 u 为null
            System.out.println("fail:"+u);   //失败 t 返回 null u 返回异常
        }).exceptionally(f -> {
            System.out.println("失败：" + f); //失败 返回自定义 444
            return 444;
        });
        System.out.println(exceptionally.get());
    }
}

```



# jvm(java虚拟机)

## 1.jvm架构图

![59249789438](C:\Users\Administrator\Desktop\安装\arrest\1592497894380.png)

**方法区，堆，线程共享**

**Java栈，本地方法栈，程序计数器，线程私有**

## 2.类装载器

![59249802305](C:\Users\Administrator\Desktop\安装\arrest\1592498023054.png)

方法区：类的模板

![59249897872](C:\Users\Administrator\Desktop\安装\arrest\1592498978722.png)

```java
//类装载器
BootstrapClassLoader //加载rt.jar下的.class文件  启动类加载器
ExtClassLoader  //加载ext/*.jar下的.class文件    扩展类加载器
AppClassLoader //加载自己写的.class文件          应用程序类加载器 
```

## 3.双亲委派机制

“我爸是李刚，有事找我爹”

类装载顺序

```java
BootstrapClassLoader -> ExtClassLoader->AppClassLoader
```

![59250021766](C:\Users\Administrator\Desktop\安装\arrest\1592500217660.png)

沙箱安全，不让自己写的代码污染java源代码

## 4.Native Interface 本地方法接口 本地方法栈

![59252906055](C:\Users\Administrator\Desktop\安装\arrest\1592529060556.png)

## 5程序计数器（pc寄存器）

指针，上一个方法运行完了，下一个运行方法是谁，指向下一条运行的指令

![59252995942](C:\Users\Administrator\Desktop\安装\arrest\1592529959424.png)

## 6.方法区

![59253135155](C:\Users\Administrator\Desktop\安装\arrest\1592531351559.png)

## 7.Java栈（栈管运行，堆管储存）、

![59253550097](C:\Users\Administrator\Desktop\安装\arrest\1592535500972.png)

![59253599494](C:\Users\Administrator\Desktop\安装\arrest\1592535994940.png)

![59254411693](C:\Users\Administrator\Desktop\安装\arrest\1592544116937.png)

![59254449487](C:\Users\Administrator\Desktop\安装\arrest\1592544494876.png)

## 8.堆（heap）

![59254634305](C:\Users\Administrator\Desktop\安装\arrest\1592546343051.png)



![59254827887](C:\Users\Administrator\Desktop\安装\arrest\1592548278870.png)

![59254832730](C:\Users\Administrator\Desktop\安装\arrest\1592548327300.png)

**永久区/元空间是方法区的实现**

![59254929388](C:\Users\Administrator\Desktop\安装\arrest\1592549293881.png)





![59254932306](C:\Users\Administrator\Desktop\安装\arrest\1592549323063.png)





## 9jvm调优

![59254987024](C:\Users\Administrator\Desktop\安装\arrest\1592549870245.png)

![59255062537](C:\Users\Administrator\Desktop\安装\arrest\1592550625373.png)

```java
 public static void main(String[] args) {
        long maxMemory = Runtime.getRuntime().maxMemory();  //java虚拟机最大内存 1/4
        long totalMemory = Runtime.getRuntime().totalMemory();//java虚拟机内存总量 1/64

        System.out.println("MAX_MEMORY"+maxMemory+"(字节)"+(maxMemory/(double)1024/1024)+"MB");
        System.out.println("TOTAL_ME<ORY"+totalMemory+"(字节)"+(totalMemory/(double)1024/1024)+"MB");


    }
```

```
-Xms1024m -Xmx1024m -XX:+PrintGCDetails
```

![59255156061](C:\Users\Administrator\Desktop\安装\arrest\1592551560617.png)

```java

        String s = "zzzzzzzzzzzzzzzzzzzzz";

        while (true) {
            s+= s+new Random().nextInt(888888888)+new Random().nextInt(99999999);
        }

```

![59255191776](C:\Users\Administrator\Desktop\安装\arrest\1592551917760.png)

gc日志分析

```java
[GC (Allocation Failure) [PSYoungGen: 2014K->504K(2560K)] 2014K->755K(9728K), 0.0047924 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
```

![59255212561](C:\Users\Administrator\Desktop\安装\arrest\1592552125618.png)

![59255258950](C:\Users\Administrator\Desktop\安装\arrest\1592552589503.png)

## 10.gc四大算法

![59255313579](C:\Users\Administrator\Desktop\安装\arrest\1592553135791.png)

![59255685076](C:\Users\Administrator\Desktop\安装\arrest\1592556850761.png)

![59255820166](C:\Users\Administrator\Desktop\安装\arrest\1592558201668.png)

引用计数算法：不用

复制算法缺点：浪费空间

标记清除缺点： 耗时，产生碎片

标记压缩缺点：耗时最长

## 11.jmm(java内存模型)

![59256527116](C:\Users\Administrator\Desktop\安装\arrest\1592565271168.png)

![59257634260](C:\Users\Administrator\Desktop\安装\arrest\1592576342602.png)



```java
两个线程访问主内存中共享变量，将主内存的变量拷贝一份到自己的工作内存中，改完后在放回主内存
问题：如何通知另一个内存主内存中的值已经改了呢？

解决方法：在共享变量加volatile

volatile三大特性：保证可见性，不保证原子性，禁止指令重排


class MyNum {
    volatile int i =10; //加上volatile 顺利打印main************
    public void add1205() {
        this.i = 1205;
    }
}
public class T2 {
    public static void main(String[] args) {
        MyNum myNum = new MyNum();

        new Thread(()->{
            try { TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
            myNum.add1205();
        }).start();

        while (myNum.i == 10) {

        }
        System.out.println("main********************");

    }
}

```

**静态先行，加载一次static**

**静态块 > 构造快>构造方法**

```java
/**
 * @auther: zzzgyu        加载顺序：   静态块 > 构造快>构造方法   静态先行，static只加载一次
 */
class Code{
    public Code() {
        System.out.println("Code******************666666666666666666");
    }

    static {
        System.out.println("Code*********************4444444444444");
    }
    {
        System.out.println("Code**********************555555555555");
    }
}
public class T3 {
    static {
        System.out.println("T3*****static*****2222222222222222");
    }
    {
        System.out.println("T3*****wu****3333333333333333333");
    }
    public static void main(String[] args) {
        System.out.println("T3********************11111111111111");
        new Code();
        System.out.println("----------------------");
        new Code();
        System.out.println("-----------------------");
        new T3();
    }
}
```







