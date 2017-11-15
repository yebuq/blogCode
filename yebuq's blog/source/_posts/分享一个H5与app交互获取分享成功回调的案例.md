---
title: 分享一个H5与app交互获取分享成功回调的案例
date: 2017-09-28 15:48:02
categories:
  - 开发
tags:
    - js
    - 业务
---
摘要:
　　最近忙的要死，因为赶上十一，今年的十一又和中秋重叠了（所以放那么多天假。。），所以公司一下主推了两个活动，这可真是把我忙坏了。。但是忙归忙，还是有一些小功能很有意思，这次就给大家主要分享一下跟app交互，获取用户分享成功的案例把。
    <div align=center style="overflow:hidden"><img src="../../../../img/2017-9/thinking.jpg"  style="display:inline-block;vertical-align:top"></div>
    <!-- more -->
<h1 style="color:#3a3a3a;">正文：</h1><h3 style="color:#3a3a3a;">案例场景：</h3>

　　场景大概是这样的：公司做一个抽奖活动，抽奖次数呢是需要用户成功分享到微信或者朋友圈，这个时候才能获得一次抽奖机会。而我们的H5是在嵌在原生app里面的webview，分享的方法也是调用原生的方法，所以分享成功的回调其实是需要app端来传给我们的。那么好，问题来了，‘分享成功’这个要怎么判定呢？当时给出了以下几种方案：
　　1.第一个方案是给分享出的页面带上用户的id与token，用户点开页面与后台交互加一次抽奖次数。但是这种方案很快被产品否定了，因为这样的话分享前就需要用户登录才能给用户分享出去的页面带用户信息。
　　2.第二个方案是用户点击分享按钮就算分享成功，加一次分享次数，这种方法直接被我据了，这么low说真的我连做的的欲望都没有
　　3.第三个方案就是用户点击H5的分享按钮，然后弹出app原生的分享到微信还是qq的弹层，点击弹层里面的qq或者微信的图标才算分享成功。我觉得这个这个还不错，而且app端人员也提出了一个很让我惊喜的事情，就是他们可以手动调用我们的js方法，只要我们在浏览器里的控制台里面能直接调用的方法，app都可以调用。所以解决方案就是：点击微信或者qq的图标，app端就调用我们的js方法，方法内是我们H5告知后台分享成功的ajax请求。
　　光说不太直观，上个图把：
<div align=center style="overflow:hidden"><img src="../../../../img/2017-9/20170928170434.jpg"  style="display:inline-block;vertical-align:top;max-width:375px;"></div>
    如上图，遮罩后面的页面是原生H5的，前面那个分享的弹层是原生app的，这样看就比较直观了把。
　　但是到最后逼逼叨叨了半天，还是选了第二种我觉得很low的方案，因为产品提出了我们忽略的一个重大问题：第二方案个需要app发版，用户不更新怎么办？不更新怎么办。。。。怎么办。。。此刻我的内心如下图。。。
<div align=center style="overflow:hidden"><img src="../../../../img/2017-9/20170928174329.jpg"  style="display:inline-block;vertical-align:top"></div>
　　卒。。。。
　　最后还有一个很偏门的问题分享给大家：你们之前有没有碰见过很奇葩的微信分享的icon莫名失效的，像下图：
<div align=left style="overflow:hidden"><img src="../../../../img/2017-9/20170928174908.jpg"  style="display:inline-block;vertical-align:top"></div>
　　我们都要上线了，突然发现这个问题，一只排查代码到晚上十一二点，后来发现不是代码问题？居然是分享的title跟content有问题。。。。这是微信的限制，如果分享内容有敏感词汇的话，图片上传就出问题了。。。这个问题简直神踏马。。。。。
　　好吧，这次分享内容就到这，没什么代码干货，纯逻辑，希望对各位同学有帮助。最后放上一张咋滴了囧图震楼，哈哈哈哈哈
<div align=left style="overflow:hidden"><img src="../../../../img/2017-9/20170928191842.jpg"  style="display:inline-block;vertical-align:top"></div>

