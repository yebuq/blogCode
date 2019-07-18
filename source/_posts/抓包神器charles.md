---
title: 抓包神器charles
date: 2018-05-08 17:50:49
categories:
    - 开发
tags:
  - 抓包
  - 抓包charles
---
摘要：
　　好久没有更新博客了，还是不能懈怠啊。今天想分享一个很牛×的抓包工具--------charles。
    <div align=center style="overflow:hidden"><img src="../../../../img/2018-5/timg.png"  style="display:inline-block;vertical-align:top"></div>
    <!-- more -->
     正文:
      　　众所周知，现在的app里面一般都会通过webview嵌入一些H5的前端页面。有些时候我们在本地开发环境跑的好好的代码，不知道怎么的在app的webkit里面就出了一些莫名其妙的问题。比如样式不对啊，js不跑呀什么的。但是让我们一个前端人员在app里面debug实在捉襟见肘啊。。完了还不能总麻烦人家移动端。那么这个时候你就拿到这个charles这个神器就ok了。
      　　首先现在网上找到一个charles的资源下载安装，这个应该都不用我放图了把。。。然后呢，安装完毕，我们打开的界面是如下的：
      <div align=center style="overflow:hidden"><img src="../../../../img/2018-5/1525775256.jpg"  style="display:inline-block;vertical-align:top"></div>
      　　然后我们首先点开左上角菜单栏的proxy，然后把windows proxy的对钩去掉。这个是抓我们pc端的一些请求的。我们想要的是移动端的，所以把pc端的过滤掉能找的更清楚。做完这些以后我们需要设置一下端口，还是点左上角菜单栏的proxy，然后选proxy setting，然后弹出如下窗口：
      <div align=center style="overflow:hidden"><img src="../../../../img/2018-5/85266614bb2f4a64314877e50892f3c.png"  style="display:inline-block;vertical-align:top"></div>
      　　这里我们需要设置一下charles的端口号，实际上每次我们一打开charles，charles就会起一个本地的服务，然后我们所有的请求都是走这个这个服务，所以我们才能抓到这些请求。所以这里要设置一下服务的端口号。端口号要尽量避免与别的服务冲突，这里我用的端口号是9999。
      　　接下来就需要我们的移动设备连上我们的charles的服务了，首先要保证我们的移动设备跟charles所在的机器是在同一个局域网。然后查看我们pc端的ip，右键开始菜单-运行-cmd打开dos窗口，然后输入ipconfig，然后打开如下的窗口：
      <div align=center style="overflow:hidden"><img src="../../../../img/2018-5/8bf765024470c01caf71d88dd1ae3f4.png"  style="display:inline-block;vertical-align:top"></div>
      　　我们要的是ipv4地址，然后记下这个我们的局域网ip地址，打开手机配置代理ip为这个ip，端口号为我们刚刚配置的端口号，比如我们的就是9999。我的是ios手机，所以拿ios来举个例：设置-无线局域网，找到我们所连的无线网，然后点网络名字右边那个小叹号，选最下面-配置代理，然后打开如下界面：
      <div align=center style="overflow:hidden"><img src="../../../../img/2018-5/82e6afc98cec467d2279e3869b05f50.png"  style="display:inline-block;vertical-align:top"></div>
      　　我的ip地址已经填上了，然后端口号是之前在charles里面设置的9999，然后点右上角存储。然后这时候charles第一次接入设备会有个提示，问题你允许设备接入么，这里我们点allow。然后就大功告成啦！如下图：
      <div align=center style="overflow:hidden"><img src="../../../../img/2018-5/43b75cce3d13e0b9d4c6fd95d84496c.png"  style="display:inline-block;vertical-align:top"></div>
      　　首先找到我们熟悉的抓包地址的域名，然后一层层点开，可以看到，包括请求服务器地址，请求状态请求头都可以看见，包括contents里面的返回数据我们都可以看见，如下图：
      <div align=center style="overflow:hidden"><img src="../../../../img/2018-5/f0a5608ddda04926087847e421d9483.png"  style="display:inline-block;vertical-align:top"></div>
      　　怎么样！是不是很赞！这样有的时候我怀疑我请求的js与css文件的服务器的地址不对不是测试服务器地址的话就可以用这个抓来看看。还有你抓到了一个游戏请求地址跟请求content的话，而正好这个请求又是个加金币的什么的。。那你重复发这个请求。。。。能懂吧？？哈哈哈。。。
      　　当然charles还不只这个妙用，之前我们不是提过charles不是就相当与在你本地起了个服务吗？嗯对，现在只要跟你连接一个局域网的小伙伴都可以通过配置代理来访问你的本地文件。比如你想让设计小伙伴看你下你本地的页面，没问题，给他发你的ip端口号，完了你这边点一下allow，然后把你本地访问的链接给他就ok了，比如你起了个本地服务127.0.0.1就是你的页面主页，然后加上端口号127.0.0.1:9999设计们就可以访问到啦。最牛逼的我们通过host改的一些映射都会生效！当然这些延伸的有点深了。。不过你会用起来的时候真是很神器呀，哈哈。
      　　好了，今天的分享就到这里了。欢迎大家踊跃留言~