---
title: 关于vue-reource请求出错的坑
date: 2017-09-04 19:54:00
  - 技术
tags:
    - js
    - vue
    - vue-resource
---
摘要:
　　这次分享一个之前碰到的关于vue-resource post请求后台数据出错的坑，希望对大家有帮助，详细如下文。。。
    <div align=center style="overflow:hidden"><img src="../../../../img/2017-9/timg.jpg"  style="display:inline-block;vertical-align:top"></div>
正文:
<h1 style="color:#3a3a3a;">vue-resource与ajax还是有点不同的</h1>
　　分享一个之前碰到的关于vue-resource post请求后台数据出错的坑。在之前的开发工作中碰到了这么一个问题，在vue中使用vue-resource get请求一切正常，可是当使用post后台数据报错如下图：
<div align=left style="overflow:hidden"><img src="../../../../img/2017-9/1504526592.jpg"  style="display:inline-block;vertical-align:top"></div>
    然后使用ajax的post请求发现是没问题的？后来网上百度了半天才在这个<a href='http://www.jianshu.com/p/8d66070eac20'>http://www.jianshu.com/p/8d66070eac20</a>简书一个大牛的帖子中找到问题所在。vue-resource默认的post的data是request payload的形式，而我们一般的ajax与form表单的话的data都是formdata的形式，所以跟后台请求的时候有些出入，才导致了数据出错。
　　解决方案有两种：
　　1.当然是麻烦后台同学改一下接口的简荣星啦；
　　2.这才是我今天重点分享的干货，就是在vue里面的main.js中添加如下的代码：
~~~
Vue.config.productionTip = false;
Vue.http.options.emulateJSON = true;
Vue.http.options.emulateHTTP = true;
Vue.http.options.headers = {
  'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8'
};~~~

　　这样我们的vue-resource的data的格式就变成formdata啦，如下图所示：
<div align=left style="overflow:hidden"><img src="../../../../img/2017-9/1504527392.jpg"  style="display:inline-block;vertical-align:top"></div>
　　像这样就大功告成啦！然后还要给大家分享一个自己范的很低级的错误。。。。我之前data的请求格式变回来了，然后数据请求也成功了，但是拿不到后台返回的数据（返回为空）是什么鬼？请看如下代码：
~~~
this.$http.post('/app/mall/excharge', {
            goodsId: _this.goodsInfo.id,
            code: _this.goodsInfo.code,
            number: _this.goodsInfo.number,
            userId: _this.userInfo.userId,
            token: _this.userInfo.token
          })
          .then((res) => {
            _this.buyToggle = false;
            if (res.body.data.mall.status === 1) { // code为1购买成功
              const popConfig = {
                msg: res.body.data.mall.msg,
                popShow: true,
                singleBtn: true,
                types: 'success' // buyReq是购买请求
              };
              this.$emit('popToggle', popConfig);
            } else if (res.body.data.mall.status === 2) { // code为2商品已下架
              const popConfig = {
                msg: res.body.data.mall.msg,
                popShow: true,
                singleBtn: true,
                types: 'outUnder' // 商品已下架刷新按钮状态
              };
              this.$emit('popToggle', popConfig);
            } else if (res.body.data.mall.status === 3) { // code为3购买库存不足
              const popConfig = {
                msg: res.body.data.mall.msg,
                popShow: true,
                singleBtn: true,
                types: 'reload' // 购买数量大于库存时需刷新购买页面
              };
              this.$emit('popToggle', popConfig);
            } else if (res.body.data.mall.status === -100) { // 重复购买的时候不做提示
              return false;
            } else {
              const popConfig = {
                msg: res.body.data.mall.msg,
                popShow: true,
                singleBtn: true,
                types: 'false' // 购买失败直接返回文案
              };
              this.$emit('popToggle', popConfig);
            }
            _this.buyToggle = true;
          });
~~~
　　没错就是第8行，我不知道脑袋当时犯了什么抽抽，在es6的then方法前面换行了？？？而且es-lint居然还不报错有木有= =！！！然后then(res)里面的res接收到的返回值当时就是空啦。。。。。。所以小伙伴们千万不要学我犯这种低级的错误。嗯好了，今天分享就到这里了，希望有帮助的同学疯狂留言^_^。


