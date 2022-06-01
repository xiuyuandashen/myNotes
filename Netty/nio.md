# NIO

>  Nio，同步非阻塞IO。有三大核心 **Channel（通道）、Buffer（缓冲区）、Selector（选择器）。**NIO是面向缓冲区，或者面向块编程的。**数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式的高伸缩性网络。**

**Client传输或读取数据是通过Channel对应的Buffer进行操作，而不是操作Channel。Channel会与Buffer进行数据交互。**

![image-20220528224730421](D:\笔记\Netty\nio.assets\image-20220528224730421.png)



> Java NIO的非阻塞模式，使一个线程从某通道发送请求或者读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取，而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。

相当于，Selector会处理活跃通道的事情（哪个通道活跃处理哪个，Selector这个线程不会阻塞）。

- **每个Channel对应一个Buffer。**

- **一个Selector对应一个线程，一个线程对应多个Channel。**
- **下图反应了有三个Channel注册到了Selector程序。**
- **程序切换到哪个Channel是由事件决定的，Event是一个重要的概念。**
- **Selector根据不同的事件，在各个通道上切换。**
- **Buffer是一个内存块，底层是有一个数组。**
- **数据的读取写入是通过Buffer，这个与BIO是本质不同的 ，BIO中要么是输入流，要么是输出流，无法双向。而Buffer是支持读和写的，仅需flip方法进行切换模式。**
- **Channel也是双向的，可以返回底层操作系统的情况，比如Linux，底层的操作系统通道也是双向的。**

![image-20220528233454273](D:\笔记\Netty\nio.assets\image-20220528233454273.png)

## Buffer基本使用

> 缓冲区(Buffer)：缓冲区本质上是一个可以读写数据的内存块，可以理解成是一个容器对象（含数组），该对象提供了一组方法，可以更轻松地使用内存块，，缓冲区对象内置了一些机制，能够跟踪和记录缓冲区的状态变化情况。**Channel提供从文件、网络读取数据的渠道，但是读取或写入的数据都必须经由Buffer**。

```Java
public static void main(String[] args) {
        // Buffer的使用
        // 创建Buffer
        // 创建一个intBuffer，可以存放5个int类型
        IntBuffer intBuffer = IntBuffer.allocate(5);
        // 存放数据
        for (int i = 0; i < intBuffer.capacity(); i++) {
            intBuffer.put(i * 2);
        }
        // 读取数据
        // 将Buffer读写切换 flip,这个很重要
        intBuffer.flip();
        while (intBuffer.hasRemaining()){
            // 每get一次内部索引往后移动
            System.out.println(intBuffer.get());
        }
}
```

Buffer类型：CharBuffer、FloatBuffer、IntBuffer、DoubleBuffer、ShortBuffer、ShortBuffer、ShortBuffer、LongBuffer、ByteBuffer。

![image-20220528231625896](D:\笔记\Netty\nio.assets\image-20220528231625896.png)



Buffer类定义了所有的缓冲区都具有的四个属性来提供关于其所包含的数据元素的信息。

| 属性     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| capacity | 容量，即可以容的最大数据量；在缓冲区创建时被设定并且不能改变 |
| limit    | 表示缓冲区的当前终点，不能对缓冲区超过极限的位进行读写操作。且极限是可以修改的 |
| position | 位置，下一个要被读或写的元素的索引，每次读写缓冲☒数据时都会改变改值，为下次读写作准备 |
| mark     | 标记                                                         |

Buffer的读写是靠position来控制的，当写入时，position会增加。当读取时，需要做一步flip进行转换读写模式。原理是将limit = position，意思是将读取的边界控制到写入时的位置+1。然后将position设置为0，从0开始读取。所有可以通过设置position和limit来控制读写位置。

![image-20220529003116562](D:\笔记\Netty\nio.assets\image-20220529003116562.png)

相关方法

![](D:\笔记\Netty\nio.assets\image-20220529145316844.png)

ByteBuffer相关方法，ByteBuffer是用的最多的Buffer类。

![image-20220529145552760](D:\笔记\Netty\nio.assets\image-20220529145552760.png)



### ByteBuffer 类型化以及只读

类型化

```java
byteBuffer.putInt(10);
byteBuffer.putDouble(10.2);
byteBuffer.putChar('a');
byteBuffer.flip();
// 按类型进行读取，否则会可能会报异常 BufferUnderflowException
System.out.println(byteBuffer.getInt());
System.out.println(byteBuffer.getDouble());
System.out.println(byteBuffer.getChar());
```

只读

```java
byteBuffer.putInt(10);
byteBuffer.putDouble(10.2);
byteBuffer.putChar('a');
byteBuffer.flip();
System.out.println(byteBuffer.getInt());
System.out.println(byteBuffer.getDouble());
System.out.println(byteBuffer.getChar());
//        byteBuffer.flip();
// 获取只读Buffer
ByteBuffer readOnlyBuffer = byteBuffer.asReadOnlyBuffer();
readOnlyBuffer.flip();
System.out.println(readOnlyBuffer.getInt());
readOnlyBuffer.put((byte)10); // 报异常 ReadOnlyBufferException
```



### MappedByteBuffer

MappedByteBuffer可以直接在堆外内存（内存）中修改数据，操作系统无需拷贝一次。

```java
public static void main(String[] args) throws Exception {
        RandomAccessFile randomAccessFile = new RandomAccessFile("E:\\file.txt","rw");
        FileChannel channel = randomAccessFile.getChannel();
        /**
         * 参数1： FileChannel.MapMode.READ_WRITE 读写模式
         * 参数2： 0 可以修改的起始地址
         * 参数3： 5 映射到内存的大小，即文件中从 0-5的字节映射到内存中
         * 这样就可以进行修改啦
         */
        MappedByteBuffer mappedByteBuffer = channel.map(FileChannel.MapMode.READ_WRITE, 0, 5);
        // 第一个字节改为H
        mappedByteBuffer.put(0,(byte) 'H');
        // 第三个字节改为x
        mappedByteBuffer.put(2,(byte)'c');
        randomAccessFile.close();
}
```



### Buffer的分散和聚集

- Scattering:将数据写入到buffer时，可以采用buffer数组，依次写入[分散]
- Gathering:从buffer读取数据时，可以采用buffer数组，依次读

```java
public static void main(String[] args) throws IOException {
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        serverSocketChannel.bind(new InetSocketAddress(7000)); // 监听7000端口
        ByteBuffer[] byteBuffers = new ByteBuffer[2];
        byteBuffers[0] = ByteBuffer.allocate(5);
        byteBuffers[1] = ByteBuffer.allocate(3);
        // 监听到与客户端链接的通道
        SocketChannel socketChannel = serverSocketChannel.accept();
        //  循环读取，不知道客户端发送多少数据
        long messageLength = 8; // 假定从客户端接收8个字节
        while (true){
            int byteRead = 0;
            while (byteRead < messageLength){
                // 分散从通道读取数据，会自动分散到多个buffer
                long l = socketChannel.read(byteBuffers);
                byteRead += l;
                System.out.println("byteRead："+ byteRead);
                // 查看buffer的limit和position
                Arrays.stream(byteBuffers).map(byteBuffer -> "limit:"+byteBuffer.limit()+" position:"+byteBuffer.position())
                        .forEach(System.out::println);

            }
            // 读写反转
            Arrays.stream(byteBuffers).forEach(byteBuffer -> byteBuffer.flip());
            // 将数据读出回显给客户端

            long byteWrite = 0;
            while (byteWrite < messageLength){
                // 分散写入通道
                long l = socketChannel.write(byteBuffers);
                byteWrite += l;
            }
            // 进行clear操作进行复位
            Arrays.stream(byteBuffers).forEach(byteBuffer -> byteBuffer.clear());
            System.out.println("byteRead: "+ byteRead + " byteWrite: "+byteWrite + " messageLength: "+messageLength);
        }
    }

```

使用telnet 进行测试

telnet 127.0.0.1 7000

![image-20220531143255757](D:\笔记\Netty\nio.assets\image-20220531143255757.png)



## NIO和BIO的比较

- BIO以流的方式处理数据，而NIO以块的方式处理数据，块IO的效率比流IO高很多
- BIO是阻塞的，NIO则是非阻塞的
- BIO基于字节流和字符流进行操作，而NIO基于Channel（通道）和Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲☒写入到通道中。**Selector(选择器)用于监听多个通道的事件（比如：连接请求，数据到达等)，因此使用单个线程就可以监听多个客户端通道**。



## Channel



NIO的通道类似于流，但有些区别如下：

- 通道可以同时进行读写，而流只能读或者写。
- 通道可以实现异步读写数据。
- 通道可以从缓冲读取数据，也可以写数据到缓冲。

![image-20220529145953459](D:\笔记\Netty\nio.assets\image-20220529145953459.png)



Channel是一个接口，有许多实现：FileChannel、SocketChannel、ServerSocketChannel、DatagramChannel等等。

FIleChannel用于文件的读写，DatagramChannel是用于UDP数据的读写。SocketChannel、ServerSocketChannel分别类似BIO中的Socket和ServerSocket。

![image-20220529151248739](D:\笔记\Netty\nio.assets\image-20220529151248739.png)

比如当服务器端使用ServerSocketChannel会在客户端对应生成一个SocketChannel进行交互。如下图所示：

其中ServerSocketChannel和SockegtChannel都是抽象类，实际上是由他们的实现类进行提供功能。也就是**ServerSocketChannelImpl**和**SockegtChannelImpl**。

![image-20220529152022996](D:\笔记\Netty\nio.assets\image-20220529152022996.png)

### FileChannel

常用的几个方法：

- public int read(ByteBuffer dst),从通道读取数据并放到缓冲区中
- public int write(ByteBuffer src)),把缓冲区的数据写到通道中
- public long transferFrom(ReadableByteChannel src,long position,long count),从目标通道
  中复制数据到当前通道
- public long transferTo(long position,long count,WritableByteChannel target),把数据从当
  前通道复制给目标通道

案例1 写入文件

```java
public static void main(String[] args) throws Exception{
        String str = "hello,word";
    	// 创建输出流
        FileOutputStream fileOutputStream = new FileOutputStream("E:\\file.txt");
    	// 从输出流中创建对应的channel
        FileChannel channel = fileOutputStream.getChannel();
    	// 创建ByteBuffer
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        byteBuffer.put(str.getBytes());
    	// 读写反转
        byteBuffer.flip();
    	// 写入文件
        channel.write(byteBuffer);
}
```



案例2 读取数据

```java
public static void main(String[] args) throws Exception{
        File file = new File("E:\\file.txt");
        FileInputStream inputStream = new FileInputStream(file);
        FileChannel channel = inputStream.getChannel();
        ByteBuffer byteBuffer = ByteBuffer.allocate((int)file.length());
        // 将通道中的数据读入byteBuffer
        channel.read(byteBuffer);
        // 读取数据
        System.out.println(new String(byteBuffer.array()));
        inputStream.close();
}
```

案例3 拷贝文件

两种方式

​	1.

```java
public static void main(String[] args) throws Exception{
        FileInputStream inputStream =new FileInputStream("E:\\file.txt");
        FileOutputStream outputStream = new FileOutputStream("E:\\file2.txt");
        FileChannel inputChannel = inputStream.getChannel();
        FileChannel outputChannel = outputStream.getChannel();
        ByteBuffer byteBuffer = ByteBuffer.allocate(5);
        int i = -1;
        while ((i =inputChannel.read(byteBuffer))!=-1){
            byteBuffer.flip();
            outputChannel.write(byteBuffer);
            // 重置标志位，这样如果buffer满了重新从0开始读取
            // 如果不clear 当position = limit read返回 0（无法读取） 会陷入死循环
            byteBuffer.clear();
        }
        inputChannel.close();
        outputChannel.close();
    }
```

2. 

```java
public static void main(String[] args) throws Exception{
		FileInputStream inputStream  = new FileInputStream("D:\\简历.pdf");
        FileOutputStream outputStream = new FileOutputStream("D:\\简历2.pdf");
        FileChannel sourceCh = inputStream.getChannel();
        FileChannel destCh = outputStream.getChannel();
        // 将 souceCh channel的数据复制到deestCh
        destCh.transferFrom(sourceCh,0,sourceCh.size());
        inputStream.close();
        outputStream.close();
}
```



## Selector

### 基本介绍

> Java的NIO，用非阻塞的方式的IO方式，可以使用一个线程，处理多个客户端的连接，使用的就是Selector。
>
> Selector能够检测多个注册的通道上是否有事件发生（**多个Channel可以以事件的方式注册到同一个Selector**），如果有事件发生，便获取事件然后针对每个事件进行处理。这样就可以使用一个单线程去管理多个通道，也就是管理多个连接和请求。
>
> 只有在连接真正有读写事件发生时，才会进行读写操作，就大大减少了系统资源的开销，并且不必为每个连接都创建一个线程，不用去维护多个线程。
>
> 避免了多线程之间上下文切换的开销。

大致描述一下，省却很多细节

![image-20220531151231295](D:\笔记\Netty\nio.assets\image-20220531151231295.png)





![image-20220531160515849](D:\笔记\Netty\nio.assets\image-20220531160515849.png)



其中 select()方法是阻塞的，直到监听到事件。select(long timeout)与select()一样是阻塞的，但最长阻塞时间为 timeout毫秒。selectNow()不阻塞只要有通道事件就**立刻返回**。



### 关系梳理

1. 当客户端连接时，会通过ServerSocketChannel得到SocketChannel(accept方法)
2. 将SokectChannel注册到Selector上（SokectChannel继承了父类的reginster，注册时需要指定事件类型，返回值为SelectionKey）
3. channel注册到Selector上会返回SelectionKey，会和该Selector关联（被加入到集合中）
4. Selector进行监听select方法，返回有事件发送的通道的个数
5. 进一步得到各个SelectionKey（使用selector的selectedKeys()方法）
6. 通过SelectionKey可以获取对应的channel通道(channel()方法)
7. 通过得到channel处理响应事件

事件类型：

- 可读：SelectionKey.OP_READ
- 可写：SelectionKey.OP_WRITE
- 连接：SelectionKey.OP_CONNECT
- 接收：SelectionKey.OP_ACCEPT

**基本用法**

下面案例有bug

服务器端

```java
public static void main(String[] args) throws IOException {
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        Selector selector = Selector.open();
        // 切换到非阻塞模式
        serverSocketChannel.configureBlocking(false);

        serverSocketChannel.bind(new InetSocketAddress(6666));
        // 注册到selector，设置监听事件为接收
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

        // 阻塞
        while (selector.select() > 0){
            // 获取活跃的通道
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            Iterator<SelectionKey> selectionKeyIterator = selectionKeys.iterator();
            while (selectionKeyIterator.hasNext()){
                SelectionKey selectionKey = selectionKeyIterator.next();
                // 如果当前通道事件类型为 接收,则将客户端连接通道注册到selector
                if(selectionKey.isAcceptable()){
                    // 客户端通道
                    SocketChannel socketChannel = serverSocketChannel.accept();
                    // 切换非阻塞模式
                    socketChannel.configureBlocking(false);
                    // 注册通道，设置通道事件为读
                    socketChannel.register(selector,SelectionKey.OP_READ);
                    // 注册通道，设置通道事件为读 ，设置一个buffer,这样对应的SelectionKey可以通过 attachment 获取
//                    socketChannel.register(selector,SelectionKey.OP_READ, ByteBuffer.allocate(1024));
                }
                // 可读事件
                if(selectionKey.isReadable()){
                    SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
//                    ByteBuffer byteBuffer = (ByteBuffer) selectionKey.attachment();
                    socketChannel.read(byteBuffer);
                    System.out.println("接收客户端message："+new String(byteBuffer.array()));
                    // 切换事件类型为 可写
                    selectionKey.interestOps(SelectionKey.OP_WRITE);
                }
                // 可写事件
                if(selectionKey.isWritable()){
                    SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
//                    ByteBuffer byteBuffer = (ByteBuffer) selectionKey.attachment();
//                    byteBuffer.clear();
                    byteBuffer.put("回复：hello".getBytes());
                    socketChannel.write(byteBuffer);
                    selectionKey.interestOps(SelectionKey.OP_READ);
                }
                // 不用要要移除
                selectionKeyIterator.remove();

            }
        }
}
```

客户端

```java
public static void main(String[] args) throws Exception{
        // 创建客户端监听6666
        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress(6666));
        socketChannel.configureBlocking(false);
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        String str = "当前时间："+ new Date() + " ";
        byteBuffer.put(str.getBytes());
        byteBuffer.flip();
        // 将byteBuffer内容写入channel
        socketChannel.write(byteBuffer);
        // 重置
        byteBuffer.clear();
        // 关闭
        socketChannel.close();
}
```



