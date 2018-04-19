title: java网络编程
author: gsal
tags:
  - 区块链
  - Java
categories:
  - Java
  - ''
date: 2018-04-04 19:42:00
---
### 网络编程三要素
#### ip地址
InetAddress功能
子类 Inet4Address Inet6Address
类中有静态方法返回该类的对象
成员方法：getByName(String host) 根据主机名或者ip地址字符得到ip地址对象
address.getHostName()
address.getHostAddress()
<!--more-->
#### 端口
标识进程。有效端口0-65535 0-1024系统使用或保留

#### 协议

### 过程
#### socket
创建发送端socket对象  
创建数据，并把数据打包  
调用socket对象的发送方法发送数据包  
释放资源  

##### UDP
DatagramSocket(发送和接受都是同一个)，内容包是DatagramPacket
###### 发送
创建发送端Socket对象
```  
DatagramSocket ds = new DatagramSocket();
```  
调用socket对象的发送方法发送数据包  
```
DatagramPacket(byte[] buf, int length, InetAddress address, int port)
```  
创建数据并把数据打包
```
ds.send(ds);
```  
释放资源  
```  
ds.close();
```
###### 接受
创建发送端socket对象  
创建接收端datagrampacket作为接受容器  
调用socket对象的接收方法接收数据
解析数据包 并显示在控制台  
释放资源

### 实现简易聊天
基于udp，java的net包和io包实现。采用两个线程处理聊天过程，一个线程负责接收信息，另一个线程负责发送信息。
- Chatroom类  

主要的类，管理ip、port和两个线程的启动  

``` java      
package cn.kvmial.chatroom;

import java.util.Scanner;

public class Chatroom {
    public static void main(String[] args) {
        
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入目标ip(10.131.4.20):");
        String ip = "10.131.4.20";
        
        String inputip = sc.nextLine();
        if (inputip.equals("")) {
            System.out.println("使用默认值10.131.4.20");
        } else {
            ip = inputip;
        }
        System.out.println("请输入目标ip(25535):");
        int port = 25535;
        String nextint = sc.nextLine();
        if (!nextint.equals("")) {
            port = Integer.parseInt(nextint);
        }
        
        Thread r = new Thread(new ReceiveThread(port));
        Thread s = new Thread(new SendThread(ip,port));
        
        r.start();
        s.start();

    }
}

```

- SendThread类    

发送消息的线程，读取键盘输入并发送  

``` java
package cn.kvmial.chatroom;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.SocketException;

public class SendThread implements Runnable{
    private String ip;
    private int port;
    
    public SendThread(String ip, int port) {
        this.ip = ip;
        this.port = port;
    }
    @Override
    public void run() {
        System.out.println("Initializing...");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            // TODO Auto-generated catch block
            e1.printStackTrace();
        }
        System.out.println("Initialized.");
        DatagramSocket ds = null;
        try {
            ds = new DatagramSocket();
        } catch (SocketException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String line = null;
        try {
            while((line = br.readLine())!= null) {

                byte[] bys = new String(line).getBytes();
                DatagramPacket dp = new DatagramPacket(bys,bys.length, InetAddress.getByName(ip), port);
                ds.send(dp);
                if("!exit".equals(line)) {
                    break;
                }
            }
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        
        ds.close();
    }

}

```

- ReceiveThread  
接收消息的线程  

``` java
package cn.kvmial.chatroom;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

public class ReceiveThread implements Runnable {
    private int port;
    public ReceiveThread(int port) {
        this.port = port;
    }
    @Override
    public void run() {
        DatagramSocket ds = null;
        try {
            ds = new DatagramSocket(port);
        } catch (SocketException e) {
            e.printStackTrace();
        }
        while(true) {
            byte[] bys = new byte[1024];
            DatagramPacket dp = new DatagramPacket(bys,1024);
            try {
                ds.receive(dp);
            } catch (IOException e) {
                e.printStackTrace();
            }
            String message =new String(dp.getData(),0,dp.getLength());
            if(message.equals("!exit")) {
                break;
            }
            System.out.println(message);
        }
        ds.close();
    }
}


```