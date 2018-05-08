---
title: 在node.js下用mongoose简单的操作mongoDB面向文档
date: 2017-05-02 10:56:47
categories:
  - 开发
tags:
    - 前端
    - mongoDB
    - node.js
---
摘要:
    　　MongoDB是一个面向文档的数据库，它非常适合Node.js应用以及云端部署，因为MySql以及PostgreSql是根据固定的结构设计将数据存储在不同的表之中，而MongoDB可以将任意类型的文档数据储存到集合之中，这也是MongoDB最有意思的特性之一。所以今天就安利一波如何用node.js操作MongoDB数据库。
    <div align=center style="height:200px;">
    <img src="../../../../img/2017-5/timg.jpg" width="585" height="192" />
    </div>
    <!-- more -->
正文:
　　要在node.js中操作MongoDB，我们首先要选择一个方便我们操作的node的插件，比较常用的插件有两种，分别是'mongodb'与'mongoose'。这里我推荐使用mongoose，因为他是根据mongodb封装的一个插件，使用起来更方便，操作起来也更简洁。
　　首先我们要在node中安装mongoose，在跟git bash中输入
```
npm install mongoose --save
```
当然如果你安装了淘宝镜像，那么使用cnpm更好。接下来我们在node.js项目中新建一个server.js文件，然后在其中输入依赖：
```
var mongoose = require('mongoose');
```
这样我们就引入了mongoose这个插件，接下来我们需要利用这个插件来连接数据库
```
mongoose.connect('mongodb://localhost:my-website')
```
这里注意了，'my-website'是你在MongoDB中的数据库的名称，如果数据库中没有这个名字的话，那么MongoDB会自动新建一个名字为'my-website'的数据库并连接。
然后我们建立一个数据模型，这个模型建立可以在文件任意位置定义，无所谓Mongoose是否已经与数据库连接,这里我们模拟建立一个用户注册的模型，包含用户的名字，信箱，以及密码信息。
```
var Schema = mongoose.Schema;
var user = mongoose.model('User',new Schema({
    first:String,
    last:String,
    email:{type:String, unique:true,
    password:{type:String, index:true}}}))
```
这里我们设置了所有的字段类型都是string类型，email的字段我们设置为唯一不可重复。然后添加新的数据到MongoDB,我们调用save的方法：
```
var user = new User(req.body.user).save(function (err) {
        if(err) return next(err);
        console.log(user);
        //成功存入后的方法;
    })
```
这里要注意我们传的参数req.body.user里面的字段要与我们创建的模型User里面的字段名字相匹配。接下来是查询的方法findOne：
```
 User.findOne({email:req.body.user.email,password:req.body.user.password},function (err, doc) {
        if(err) throw err;
        if(!doc) return res.send("<p>user is not found! please go back an try again</p>")
        console.log(doc);
        req.session.loggedIn = doc._id;
        res.redirect('/');
    })
```
如果查询成功，函数会成功返回一个doc，里面包含了我们查询到的相关信息，这里我要重点给大家介绍一下doc_id这个东西，doc_id是在存储的时候MongoDB自动创建的唯一标识id，方便于我们操作，有一点要注意这个ID是ObjectID类型的。具体查询方法如下：
```
 User.findById(req.session.loggedIn , function (err, doc) {
            if(err) return next(err)
            console.log(doc);
            app.locals={me:doc};
            next();
        })
```
这里req.session.loggedIn就是我们存储的doc_id，根据这个id查询出的信息都是唯一的。

下面给大家分享一段我一个模拟登陆注册的server.js的代码：
```
var express = require('express'),
    bodyParer = require('body-parser'),
    jade = require('jade'),
    cookieParser = require('cookie-parser'),
    mongodb = require('mongodb'),
    http = require('http'),
    session = require('express-session'),
    ObjectID = require('mongodb').ObjectID,
    mongoose = require('mongoose');
/**
 * 创建中间件以及一些中间件需要用到的插件
 */
var app = express();

app.use(bodyParer.urlencoded({extended:true}));
app.use(cookieParser())
app.use(session({
    resave:false,
    saveUninitialized:false,
    secret: 'keyboard cat'
}))

/**
 * 定义模板引擎
 */
app.set("view engine","jade");
app.set('views',"./views")

/**
 * 连接数据库
 */
mongoose.connect('mongodb://localhost/my-website')

/**
 * 定义模板
 */
var Schema = mongoose.Schema;
var User = mongoose.model('user',new Schema({
    first:String,
    last:String,
    email:{type:String,unique:true},
    password:{type:String,index:true}
}))

/**
 * 登陆状态的验证的通用路由
 */
app.use(function (req, res, next) {
    if(req.session.loggedIn){
        res.locals = {authenticated:true}
        console.log(req.session.loggedIn)
       User.findById(req.session.loggedIn , function (err, doc) {
            if(err) return next(err)
            console.log(doc);
            app.locals={me:doc};
            next();
        })
    }
    else{
        app.locals = {authenticated:false}
        next();
    }
})
/**
 * 主页路由
 */
app.get('/',function (req, res, next) {
    res.render('index');
})
app.get('/login/:signupEmail',function (req, res, next) { //这里允许主页接收一个参数
    res.render('login',{signupEmail:req.params.signupEmail})
})
/**
 * 登陆路由模块
 */
app.get('/login/',function (req, res, next) {
    res.render('login')
})
app.post('/login',function (req, res,next) {
    User.findOne({email:req.body.user.email,password:req.body.user.password},function (err, doc) {//验证登陆账号密码
        if(err) throw err;
        if(!doc) return res.send("<p>user is not found! please go back an try again</p>")
        console.log(doc);
        req.session.loggedIn = doc._id;
        res.redirect('/');
    })
})
/**
 * 注册路由模块
 */
app.get('/signup',function (req, res) {
    res.render('signup')
})
app.post('/signup',function (req, res) {
    console.log(req.body);
    var user = new User(req.body.user).save(function (err) {//注册信息保存到MongoDB数据库
        if(err) return next(err);
        console.log(user);
        res.redirect('/login/' + req.body.user.email);
    })
})
/**
 * 登出操作
 */
app.get('/logout',function (req, res) {
    req.session.loggedIn = null;
    res.redirect('/')
})

http.createServer(app).listen(3000);
```
　　希望我的分享对大家有帮助哦o(^▽^)o本文原创欢迎转载~~~~~~~~~~~~

　　