---
title: 手把手教你如何用node.js创建一个聊天室
date: 2017-04-06 09:55:28
categories:
  - 开发
tags:
    - 前端
    - 聊天室
    - node.js
---
摘要:
    　　node.js无疑是当下最火的前端构建后台的的工具，包括一些gulp，webpack，grunt，都是建立在node.js之上的。node的强大可以说可以帮前端光用JS就可以实现一些。今天就分享一波如何用node.js里面最简单的一些module实现一个简单的聊天室
    <div align=center style="height:200px;">
    <img src="../../../../img/2017-4/timg (1).jpg" width="490" height="132" />
    </div>
    <!-- more -->
正文:
　　话不多说，先上代码：
```
var net = require("net"); //应用模块
var userCount = 0;  //记录当前用户数量
var users = {};      //记录用户信息
var server = net.createServer(function(c){
   var nickname = "";  //创建一个变量，用于保存新建用户名
   userCount ++;
   var message ='';   //创建一个空字符串用于接收用户输入的信息片段
   c.setEncoding("utf8");  //设置新建连接的编码格式为utf-8
   c.on("data",function(data){  //绑定一个data事件，用于监听客户端的输入操作
       if(data !="\r\n"){   //判定如果输入的不是回车，则把输入的信息片段拼接起来
           message += data;
           return;
       }
       else{
           if(!nickname){  //判断如果此链接不存在昵称，则是首次联结聊天室
               if(users[message]) //如果用户输入的昵称已经存在，则重新输入
               {
                   c.write("the nick your putin had already exist , please again : \r\n");
                   return;
               }
               else{
                   for(var i in users){
                       users[i].write( message + " has already connected in ~\r\n"); //新用户链接，给其他用户发送公告
                   }
                   nickname = message;  //保存用户输入的昵称
                   users[message] = c;  //把每个新创建的连接添加到用户群组中
                   userCount ++;  //用户数加1
                   var curUsers = Object.keys(users);
                   console.log("current users : " + curUsers);
                   c.write("alright , your name is :" + message + "\r\n");
                   c.write("current users : " + curUsers +"\r\n");
                   c.write("please input your message:\r\n");
               }
           }
           else{
               c.write("your says:" + message +"\r\n"); //在当前客户端打印你输入的信息
               for(var i in users){
                  if (i != nickname){
                      users[i].write(nickname + " says : " + message +"\r\n");  //广播当前连接发送的信息
                  }
               }
           }
           message = "";
           return;
       }

   });
    c.on("close",function () {  //连接关闭删除用户
       delete users[nickname];
        userCount --;  //用户数量减减
        console.log(nickname + " quite !"); //服务端打印出退出的用户
    });
   c.write("hello~ please input your nickname and then press Enter: \r\n");
}).listen(3000,function(){  //监听端口3000
   console.log("server bound !");
});
```
　　怎么安装node就不详细介绍了，自己百度下吧。然后新建项目，创建一个index.js，然后把上述代码复制进去，然后再doc界面输入node index启动服务，这样服务端就大功告成了。然后客户端用windows跟mac都自带有的telnet来测试（这里需要注意，一般电脑上的telnet进程是默认关闭的，可以百度一下先把这个服务打开），然后再运行用输入telnet 127.0.0.1:3000，就能连接到我们的服务端了。
<div align=center>
    <img src="../../../../img/2017-4/QQ20170406110609.png" width="400" height="220" />
    </div>
　　看下代码逻辑：首先我们这里只用到了一个node里面叫"net"的模块，这个模块跟"http"模块相似，都可以用.createServer来创建服务，然后每个createServer里面接收的参数（c）就是我们可以操作的连接，每次新进来一个用户你就可以理解为新创建了一个c变量。然后我们监听每个连接的数据传送就是监听c的data事件，监听每个连接的关闭事件，就是监听c的close事件，连接的编码格式就是c.setEncoding("utf8")。这样讲就非常好理解了。 我们这里用了users[message] = c用了这样一句话把每个新建连接保存在一个对象之中，然后每次给所有用户广播消息的时候只需一个循环给对象里的每个连接都发送一次当前客户端发送的消息，就可以啦！
　　但是这里有一个点非常需要注意，我们监听c的data事件，是每次有变动就触发的。意思就是 if(data !="\r\n") 如果你不主动判断一下是否输入了回车字符的话，每次你输入一个字母，data事件就给我们返回一个字母，更像一种流传送的形式。所以我们这里用回车来截断一下，判定每次用户输入回车为一段完整字符串的输入，不然的话就把字符片段与之前的片段拼接在一块，这样每次看到的就是一段完整的话了。
　　好了，大概介绍这么多，有什么不懂的可以给我留言啦。
　　