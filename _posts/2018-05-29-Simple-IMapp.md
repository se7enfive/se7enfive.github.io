---
layout: post
title: 'Socket and ServerSocket'
date: 2017-04-18
author: 75
cover: 'http://imgsrc.baidu.com/imgad/pic/item/94cad1c8a786c91740b11605c23d70cf3bc7575f.jpg'
tags: 网络编程
---
>emmmm想开始做个简单的IM app，查了一下资料，发现要很多基础，所以把整理的资料发这里吧

###Socket 编程
套接字使用TCP提供了两台计算机之间的通信机制。 客户端程序创建一个套接字，并尝试连接服务器的套接字。
当连接建立时，服务器会创建一个 Socket 对象。客户端和服务器现在可以通过对 Socket 对象的写入和读取来进行通信。
java.net.Socket 类代表一个套接字，并且 java.net.ServerSocket 类为服务器程序提供了一种来监听客户端，并与他们建立连接的机制。
以下步骤在两台计算机之间使用套接字建立TCP连接时会出现：
•	服务器实例化一个 ServerSocket 对象，表示通过服务器上的端口通信。
•	服务器调用 ServerSocket 类的 accept() 方法，该方法将一直等待，直到客户端连接到服务器上给定的端口。
•	服务器正在等待时，一个客户端实例化一个 Socket 对象，指定服务器名称和端口号来请求连接。
•	Socket 类的构造函数试图将客户端连接到指定的服务器和端口号。如果通信被建立，则在客户端创建一个 Socket 对象能够与服务器进行通信。
•	在服务器端，accept() 方法返回服务器上一个新的 socket 引用，该 socket 连接到客户端的 socket。
连接建立后，通过使用 I/O 流在进行通信，每一个socket都有一个输出流和一个输入流，客户端的输出流连接到服务器端的输入流，而客户端的输入流连接到服务器端的输出流。
TCP 是一个双向的通信协议，因此数据可以通过两个数据流在同一时间发送.以下是一些类提供的一套完整的有用的方法来实现 socket。

###ServerSocket 类的方法
服务器应用程序通过使用 java.net.ServerSocket 类以获取一个端口,并且侦听客户端请求。
ServerSocket 类有四个构造方法：
序号	方法描述
1		public ServerSocket(int port) throws IOException	创建绑定到特定端口的服务器套接字。
2		public ServerSocket(int port, int backlog) throws IOException	利用指定的 backlog 创建服务器套接字并将其绑定到指定的本地端口号。
3		public ServerSocket(int port, int backlog, InetAddress address) throws IOException	使用指定的端口、侦听 backlog 和要绑定到的本地 IP 地址创建服务器。
4		public ServerSocket() throws IOException	创建非绑定服务器套接字。
创建非绑定服务器套接字。 如果 ServerSocket 构造方法没有抛出异常，就意味着你的应用程序已经成功绑定到指定的端口，并且侦听客户端请求。
这里有一些 ServerSocket 类的常用方法：
序号	方法描述
1		public int getLocalPort()	返回此套接字在其上侦听的端口。
2		public Socket accept() throws IOException	侦听并接受到此套接字的连接。
3		public void setSoTimeout(int timeout)	通过指定超时值启用/禁用 SO_TIMEOUT，以毫秒为单位。
4		public void bind(SocketAddress host, int backlog)	将 ServerSocket 绑定到特定地址（IP 地址和端口号）。



###Socket 类的方法
java.net.Socket 类代表客户端和服务器都用来互相沟通的套接字。客户端要获取一个 Socket 对象通过实例化 ，而 服务器获得一个 Socket 对象则通过 accept() 方法的返回值。
Socket 类有五个构造方法.
序号	方法描述
1		public Socket(String host, int port) throws UnknownHostException, IOException.	创建一个流套接字并将其连接到指定主机上的指定端口号。
2		public Socket(InetAddress host, int port) throws IOException	创建一个流套接字并将其连接到指定 IP 地址的指定端口号。
3		public Socket(String host, int port, InetAddress localAddress, int localPort) throws IOException.	创建一个套接字并将其连接到指定远程主机上的指定远程端口。
4		public Socket(InetAddress host, int port, InetAddress localAddress, int localPort) throws IOException.	创建一个套接字并将其连接到指定远程地址上的指定远程端口。
5		public Socket()	通过系统默认类型的 SocketImpl 创建未连接套接字
当 Socket 构造方法返回，并没有简单的实例化了一个 Socket 对象，它实际上会尝试连接到指定的服务器和端口。
下面列出了一些感兴趣的方法，注意客户端和服务器端都有一个 Socket 对象，所以无论客户端还是服务端都能够调用这些方法。
序号	方法描述
1		public void connect(SocketAddress host, int timeout) throws IOException	将此套接字连接到服务器，并指定一个超时值。
2		public InetAddress getInetAddress()	返回套接字连接的地址。
3		public int getPort()	返回此套接字连接到的远程端口。
4		public int getLocalPort()	返回此套接字绑定到的本地端口。
5		public SocketAddress getRemoteSocketAddress()	返回此套接字连接的端点的地址，如果未连接则返回 null。
6		public InputStream getInputStream() throws IOException	返回此套接字的输入流。
7		public OutputStream getOutputStream() throws IOException	返回此套接字的输出流。
8		public void close() throws IOException	关闭此套接字。

这就是最基本的网络编程功能介绍。下面是一个简单的网络客户端程序示例，该程序的作用是向服务器端发送一个字符串“Hello”，并将服务器端的反馈显示到控制台，数据交换只进行一次，当数据交换进行完成以后关闭网络连接，程序结束。实现的代码如下：



```css
	p { color: red }
```
package tcp;
import java.io.*;
import java.net.*;
/**
 * 简单的Socket客户端
 * 功能为：发送字符串“Hello”到服务器端，并打印出服务器端的反馈
 */
public class SimpleSocketClient {
         public static void main(String[] args) {
                   Socket socket = null;
                   InputStream is = null;
                   OutputStream os = null;
                   //服务器端IP地址
                   String serverIP = "127.0.0.1";
                   //服务器端端口号
                   int port = 10000;
                   //发送内容
                   String data = "Hello";
                   try {
                            //建立连接
                            socket = new Socket(serverIP,port);
                            //发送数据
                            os = socket.getOutputStream();
                            os.write(data.getBytes());
                            //接收数据
                            is = socket.getInputStream();
                            byte[] b = new byte[1024];
                            int n = is.read(b);
                            //输出反馈数据
                            System.out.println("服务器反馈：" + new String(b,0,n));
                   } catch (Exception e) {
                            e.printStackTrace(); //打印异常信息
                   }finally{
                            try {
                                     //关闭流和连接
                                     is.close();
                                     os.close();
                                     socket.close();
                            } catch (Exception e2) {}
                   }
         }
}

import java.io.*;

import java.net.*;

/**

 * echo服务器

 * 功能：将客户端发送的内容反馈给客户端

 */

public class SimpleSocketServer {

         public static void main(String[] args) {

                   ServerSocket serverSocket = null;

                   Socket socket = null;

                   OutputStream os = null;

                   InputStream is = null;

                   //监听端口号

                   int port = 10000;

                   try {

                            //建立连接

                            serverSocket = new ServerSocket(port);

                            //获得连接

                            socket = serverSocket.accept();

                            //接收客户端发送内容

                            is = socket.getInputStream();

                            byte[] b = new byte[1024];

                            int n = is.read(b);

                            //输出

                            System.out.println("客户端发送内容为：" + new String(b,0,n));

                            //向客户端发送反馈内容

                            os = socket.getOutputStream();

                            os.write(b, 0, n);

                   } catch (Exception e) {

                            e.printStackTrace();

                   }finally{

                            try{

                                     //关闭流和连接

                                     os.close();

                                     is.close();

                                     socket.close();

                                     serverSocket.close();

                            }catch(Exception e){}

                   }

         }

}