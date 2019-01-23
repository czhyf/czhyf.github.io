---
layout: post
title: 'WebSocket读取log'
date: 2017-12-10
author: 男孩
tags: java
---
#### webSocket 读取linux日志，并显示到前端页面
刚才在帖子上看到这篇文章觉得很简单，写websocket的，在这记录一下。
##### 直接线程代码 后端
```css
package cn.worboy.websocket;

import javax.websocket.Session;
import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;

public class TailLongThread extends  Thread {
    private BufferedReader reader;
    private Session session;

    public TailLongThread(InputStream in,Session session){
        this.reader=new BufferedReader(new InputStreamReader(in));
        this.session=session;
    }

    @Override
    public void run() {
        String line;
        try{
            while ((line=reader.readLine())!=null){
                //将实时日志通过WebSocket发送给客户端，给每一行添加一个Html换行
                session.getBasicRemote().sendText(line+"<br>");
                try {
                    //每0.5秒执行一次
                    Thread.sleep(500);
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```
```css
package cn.worboy.websocket;

import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.io.InputStream;

@ServerEndpoint("/log")
public class LogWebHandle {
    private Process process;
    private InputStream inputStream;

    /**
     * 新的WebSocket请求开启
     */
    @OnOpen
    public void onOpen(Session session){
        try{
            //执行tail -f 命令 因为这块要读取linux上的数据，所以一定要部署到linux平台上
            process = Runtime.getRuntime().exec("tail -f /root/software/log/cloudera-scm-agent.log");
            inputStream =process.getInputStream();
            //一定要启动新的线程，防止InputStream阻塞处理WebSocket的线程
            TailLongThread thread=new TailLongThread(inputStream,session);
            thread.start();
        }catch (IOException e){
            e.printStackTrace();
        }
    }
    /**
     * WebSocket 请求关闭
     */
    @OnClose
    public void onClose(){
        try{
            if(inputStream!=null){
                inputStream.close();
            }
        }catch (Exception e){
            e.printStackTrace();
        }
        if (process!=null){
            process.destroy();
        }
    }

    /**
     * 发生异常
     */
    @OnError
    public void onErro(Throwable throwable){
        throwable.printStackTrace();
    }
}
```
#### 前端代码
```css
<html>
<head>
    <script src="//cdn.bootcss.com/jquery/2.1.4/jquery.js"></script>
</head>
<body>
<div id="log-container" style="height: 450px; overflow-y: scroll; background: #333; color: #aaa; padding: 10px;">
    <div>
    </div>
</div>

</body>
<script>
    $(document).ready(function() {
        // 指定websocket路径
        var websocket = new WebSocket('ws://192.168.1.23:8080/webSocketDemo_war/log');
        websocket.onmessage = function(event) {
            // 接收服务端的实时日志并添加到HTML页面中
            $("#log-container div").append(event.data);
            // 滚动条滚动到最低部
            $("#log-container").scrollTop($("#log-container div").height() - $("#log-container").height());
        };
    });
</script>
</html>
```
##### 将其打成war包，并放置到 tomcat上的webapps上，它自己会解读。
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/websocet/1.PNG)
##### 这儿显示的效果的是
<iframe width="560" height="400" src="http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/websocet/log.mp4" frameborder="0" allowfullscreen></iframe>
##### WebSocket的优缺点
ebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。
在 WebSocket API 中，浏览器和服务器只需要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。
现在，很多网站为了实现推送技术，所用的技术都是 Ajax 轮询。轮询是在特定的的时间间隔（如每1秒），由浏览器对服务器发出HTTP请求，然后由服务器返回最新的数据给客户端的浏览器。这种传统的模式带来很明显的缺点，即浏览器需要不断的向服务器发出请求，然而HTTP请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分，显然这样会浪费很多的带宽等资源。
HTML5 定义的 WebSocket 协议，能更好的节省服务器资源和带宽，并且能够更实时地进行通讯。
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/websocet/ws.png)

速度确实很快的。没问题 比只ajax轮询优秀很多。