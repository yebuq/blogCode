---
title: 'eclipse配置项目可以被跨域访问:Access-Control-Allow-Origin:*'
date: 2017-03-01 10:20:39
categories:
  - 开发
tags:
    - 前端
    - eclipse
    - 跨域
---
摘要:
    　　这篇文章旨在分享一下eclipse如何配置项目可以被跨域访问到。因为网上看了很多，坑也踩了不少，而且身为一个前端的小白，有些地方忽略的我一头雾水有木有= =，所以我会图本并茂的讲解一下，大神请忽略= =

<div align=center>
<img src="../../../../img/2017-3/19_222949_9.jpg" width="500" height="500" />
</div>
<!-- more -->
正文:
　　这篇文章旨在分享一下eclipse如何配置项目可以被跨域访问到。因为网上看了很多，坑也踩了不少，而且身为一个前端的小白，有些地方忽略的我一头雾水有木有= =，所以我会图本并茂的讲解一下，大神请忽略= =
　　百度到的方法大多是教给我们利用cors，或者是在java里新建一个filter类，然后配置web.xml配置里新加一堆标签，可是我想说身为一个小白的我，表示怎么创建这个类，在哪创建都不知道啊。。后来研究了半天，又百度了不少，才算是弄明白一整套流程，也是最简单的方法：

　　1.编写拦截器代码，在项目目录下src/default package/下新建一个类CrossOriginFilter，实现Filter接口，代码如下：
```

package org.digdata.swustoj.filter;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletResponse;

/**
 *
 * @author wwhhf
 * @since 2016年5月30日
 * @comment 跨域过滤器
 */
public class CrossOriginFilter implements Filter {

    private FilterConfig config = null;

    @Override
    public void init(FilterConfig config) throws ServletException {
        this.config = config;
    }

    @Override
    public void destroy() {
        this.config = null;
    }

    /**
     *
     * @author wwhhf
     * @since 2016/5/30
     * @comment 跨域的设置
     */
    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException {
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        // 表明它允许"http://xxx"发起跨域请求
        httpResponse.setHeader("Access-Control-Allow-Origin",
                config.getInitParameter("AccessControlAllowOrigin"));
        // 表明在xxx秒内，不需要再发送预检验请求，可以缓存该结果
        httpResponse.setHeader("Access-Control-Allow-Methods",
                config.getInitParameter("AccessControlAllowMethods"));
        // 表明它允许xxx的外域请求
        httpResponse.setHeader("Access-Control-Max-Age",
                config.getInitParameter("AccessControlMaxAge"));
        // 表明它允许跨域请求包含xxx头
        httpResponse.setHeader("Access-Control-Allow-Headers",
                config.getInitParameter("AccessControlAllowHeaders"));
        chain.doFilter(request, response);
    }

}
```
　　2.WEB-INF/lib下找到web.xml，添加如下代码（有的人项目下会没有自动生成web.xml这个文件，请参考[http://jingyan.baidu.com/article/90808022c495d9fd91c80f15.html](http://jingyan.baidu.com/article/90808022c495d9fd91c80f15.html)）:
```
<filter>
        <filter-name>CrossOrigin</filter-name>
        <filter-class>org.digdata.swustoj.filter.CrossOriginFilter</filter-class>
         <init-param>
            <param-name>AccessControlAllowOrigin</param-name>
            <param-value>http://localhost:63342</param-value>//这个value是要换成你的请求网址，如果是*为可以被所有网址跨域请求
        </init-param>
        <init-param>
            <param-name>AccessControlAllowMethods</param-name>
            <param-value>POST, GET, DELETE, PUT</param-value>
        </init-param>
        <init-param>
            <param-name>AccessControlMaxAge</param-name>
            <param-value>3628800</param-value>
        </init-param>
        <init-param>
            <param-name>AccessControlAllowHeaders</param-name>
            <param-value>x-requested-with</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CrossOrigin</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```
　　3.就可以在WebContent目录下新建一个jsp页面，用来测试页面是否可以被跨域请求，jsp代码如下：
```
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
	<h1>tomcat 2.0</h1>
</body>
<html>
```
<div align=center>
<img src="../../../../img/2017-3/20170301120317.jpg" width="800" height="400" />
</div>

　　4.简单说一下跨域测试，用google浏览器就可以测试了，因为谷歌浏览器默认用ajax或者iframe加载本地文件也是跨域请求。所以你现在eclipse里面把这个页面项目在server里跑起来，就新建一个页面用ajax加载跑起来之后的网址，如果是就成功咯o(^-^)o
<div align=center>
<img src="../../../../img/2017-3/20170301155549.jpg" width="800" height="400" />
</div>
<div align=center>
<img src="../../../../img/2017-3/20170301155622.jpg" width="800" height="400" />
</div>
