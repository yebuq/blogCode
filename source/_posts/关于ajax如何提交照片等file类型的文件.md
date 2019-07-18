---
title: 关于ajax如何提交照片等file类型的文件
date: 2017-03-29 10:54:50
categories:
  - 开发
tags:
    - 前端
    - 文件上传
    - ajax
---
摘要:
    　　好久没有更新博文了。。。感觉是时候分享一波小技术了o(^▽^)o，嗯今天就分享一个关于项目中如何用ajax上传图片的小知识。。你准备好了吗。。。？？
    <div align=center>
    <img src="../../../../img/2017-3/6aa2976egx6CHgSChhS88&690.jpg" width="700" height="500" />
    </div>
    <!-- more -->
正文：
    　　问题是这样的，项目里遇到一个表格里添加一行的操作，所以这个没有办法用表单提交，别的都好好说，用ajax传一个json字符串给后台就好了，但是这个图片就没办法跟json字符串一块传过去了。所以网上找到了这个方法，觉得写的很好很常用，所以还是写在自己的博客里方便分享。话不多说直接上代码：
```
     var formData = new FormData();
     formData.append('file', $('#up_img_add')[0].files[0]);
     $.ajax({
         url: "../../rest/group/upload",
         method: "POST",
         async: false,
         contentType: false,
         processData: false, //必须false才会自动加上正确的Content-Type,
         data: formData,
         success: function (data) {
             console.log(data);
             equipGroup.image = data;
         }
     });

```
　　这里首先创建一个FormData()对象，一开始不知道，百度一下才知道FormData()这个对象真的是个好东西，可以保存的东西有名为"username"，"accountnum"，"userfile" 以及 "webmasterfile" 的字段名，字段 "userfile" 和 "webmasterfile" 的值都包含了一个文件，"accountnum" 的数字被自动转换为字符，"webmasterfile" 字段的值不是一个字符串,还是一个 Blob 对象。例：
```
     var oMyForm = new FormData();

     oMyForm.append("username", "Groucho");
     oMyForm.append("accountnum", 123456); // 数字123456被立即转换成字符串"123456"

     // fileInputElement中已经包含了用户所选择的文件
     oMyForm.append("userfile", fileInputElement.files[0]);

     var oFileBody = "<a id="a"><b id="b">hey!</b></a>"; // Blob对象包含的文件内容
     var oBlob = new Blob([oFileBody], { type: "text/xml"});

     oMyForm.append("webmasterfile", oBlob);
```
　　用 formData.append('file', $('#up_img_add')[0].files[0])这个给创建的formData对象插入你要上传的input标签的files值，然后就可以用ajax上传啦！但是上传的时候要注意两点：
　　1.contentType: false, processData: false, 这两句话一定要加上，不然jquery的ajax会默认给你加上错误的头部type类型。
　　2.data这个字段是不需要定义的，加上之后会发生意想不到的错误。
　　然后后台接收到图片以后会保存在一个目录下面，然后把这个目录的url地址返回给我们，我们就可以调用啦！大功告成~
　　<div align=center>
    <img src="../../../../img/2017-3/ChMkJlcZuB-ILohjABj4B9nSpLsAAQZdAG1MEUAGPgf978.jpg" width="700" height="500" />
    </div>
