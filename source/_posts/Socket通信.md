---
title: Socket通信
date: 2018-08-29 19:48:46
tags: Java I/O
categories: Java I/O
---
最开始学习过Socket编程，半年没看有所遗忘，在这里简单总结一下。

<!-- more -->

# 一. 总览

## TCP 
TCP 协议提供面向连接的服务，通过它建立的是可靠地连接。

Java 为 TCP 协议提供了两个类：Socke 类和 ServerSocket 类。一个 Socket 实例代表了 TCP 连接的一个客户端，而一个 ServerSocket 实例代表了 TCP 连接的一个服务器端。

##  TCP Socket 
一般在 TCP Socket 编程中，**客户端有多个，而服务器端只有一个**，客户端 TCP 向服务器端 TCP 发送连接请求，服务器端的 ServerSocket 实例则监听来自客户端的 TCP 连接请求，并为每个请求创建新的 Socket 实例，由于服务端在调用`accept（）`等待客户端的连接请求时会阻塞，直到收到客户端发送的连接请求才会继续往下执行代码，因此要为每个 Socket 连接开启一个线程。

**服务器端要同时处理 ServerSocket 实例和 Socket 实例，而客户端只需要使用 Socket 实例**。另外，每个 Socket 实例会关联一个 InputStream 和 OutputStream 对象，我们通过将字节写入套接字的 OutputStream 来发送数据，并通过从 InputStream 来接收数据。

# 二. TCP Socket步骤

## 客户端
客户端向服务器端发送连接请求后，就被动地等待服务器的响应。

1. 创建一个 Socket **实例**
`Socket client = new Socket("127.0.0.1", 20006); `
构造函数向指定的远程主机和端口建立一个 TCP 连接
2. 通过套接字的 **I/O 流**与服务端通信

```java
BufferedReader input = new BufferedReader(new InputStreamReader(System.in));  
PrintStream out = new PrintStream(client.getOutputStream()); 
BufferedReader buf =  new 
              BufferedReader(new InputStreamReader(client.getInputStream()));  
```

3. 关闭连接
`client.close();`

## 服务端
服务端的工作是建立一个通信终端，并被动地等待客户端的连接。

1. 创建一个 ServerSocket 实例并指定本地端口，用来监听客户端在该端口发送的 TCP 连接请求
` ServerSocket server = new ServerSocket(20006);`
2. 重复执行：
2.1 调用 ServerSocket 的 accept（）方法以获取客户端连接，并通过其返回值创建一个 Socket 实例
`client = server.accept();`
2.2 为返回的 Socket 实例开启新的线程，并使用返回的 Socket 实例的 I/O 流与客户端通信
`new Thread(new ServerThread(client)).start();  `
3.  通信完成后，使用 Socket 类的 close（）方法关闭该客户端的套接字连接
` server.close();`

![步骤](http://upload-images.jianshu.io/upload_images/11861611-4b0817d7fd5610c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 三. 例子

## Client1

```java
import java.io.BufferedReader;  
import java.io.IOException;  
import java.io.InputStreamReader;  
import java.io.PrintStream;  
import java.net.Socket;  
import java.net.SocketTimeoutException;  

public class Client1 {  
    public static void main(String[] args) throws IOException {  
        //客户端请求与本机在20006端口建立TCP连接   
        Socket client = new Socket("127.0.0.1", 20006);  
        client.setSoTimeout(10000);  
        //获取键盘输入   
        BufferedReader input = new BufferedReader(new InputStreamReader(System.in));  
        //获取Socket的输出流，用来发送数据到服务端    
        PrintStream out = new PrintStream(client.getOutputStream());  
        //获取Socket的输入流，用来接收从服务端发送过来的数据    
        BufferedReader buf =  new BufferedReader(new InputStreamReader(client.getInputStream()));  
        boolean flag = true;  
        while(flag){  
            System.out.print("输入信息：");  
            String str = input.readLine();  
            //发送数据到服务端    
            out.println(str);  
            if("bye".equals(str)){  
                flag = false;  
            }else{  
                try{  
                    //从服务器端接收数据有个时间限制（系统自设，也可以自己设置），超过了这个时间，便会抛出该异常  
                    String echo = buf.readLine();  
                    System.out.println(echo);  
                }catch(SocketTimeoutException e){  
                    System.out.println("Time out, No response");  
                }  
            }  
        }  
        input.close();  
        if(client != null){  
            //如果构造函数建立起了连接，则关闭套接字，如果没有建立起连接，自然不用关闭  
            client.close(); //只关闭socket，其关联的输入输出流也会被关闭  
        }  
    }  
}  
```

## Server1

```java
import java.net.ServerSocket;  
import java.net.Socket;  

public class Server1 {  
    public static void main(String[] args) throws Exception{  
        //服务端在20006端口监听客户端请求的TCP连接  
        ServerSocket server = new ServerSocket(20006);  
        Socket client = null;  
        boolean f = true;  
        while(f){  
            //等待客户端的连接，如果没有获取连接  
            client = server.accept();  
            System.out.println("与客户端连接成功！");  
            //为每个客户端连接开启一个线程  
            new Thread(new ServerThread(client)).start();  
        }  
        server.close();  
    }  
} 
```

## ServerThread(该类为多线程类，用于服务端)

```
import java.io.BufferedReader;  
import java.io.InputStreamReader;  
import java.io.PrintStream;  
import java.net.Socket;  

public class ServerThread implements Runnable {  

    private Socket client = null;  
    public ServerThread(Socket client){  
        this.client = client;  
    }  

    @Override  
    public void run() {  
        try{  
            //获取Socket的输出流，用来向客户端发送数据  
            PrintStream out = new PrintStream(client.getOutputStream());  
            //获取Socket的输入流，用来接收从客户端发送过来的数据  
            BufferedReader buf = new BufferedReader(new InputStreamReader(client.getInputStream()));  
            boolean flag =true;  
            while(flag){  
                //接收从客户端发送过来的数据  
                String str =  buf.readLine();  
                if(str == null || "".equals(str)){  
                    flag = false;  
                }else{  
                    if("bye".equals(str)){  
                        flag = false;  
                    }else{  
                        //将接收到的字符串前面加上echo，发送到对应的客户端  
                        out.println("echo:" + str);  
                    }  
                }  
            }  
            out.close();  
            client.close();  
        }catch(Exception e){  
            e.printStackTrace();  
        }  
    }  
}  
```

![image](http://upload-images.jianshu.io/upload_images/11861611-b5af117cd9f078b5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考文章
[极客学院](http://wiki.jikexueyuan.com/project/java-socket/tcp.html)
[Java Socket编程基础及深入讲解](https://www.cnblogs.com/yiwangzhibujian/p/7107785.html)
