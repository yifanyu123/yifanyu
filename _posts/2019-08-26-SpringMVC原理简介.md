---
layout: post
title: 'Spring MVC原理简介'
date: 2019-08-27
author: 小刺猬
color: '#83b582'
cover: '../assets/cover/11.JPG'
tags: spring coding java mvc
---
# Spring MVC原理简介
## 一次请求处理过程
1. 请求： 将请求提交给服务器，大多数框架都有一个调度程序（以servlet的形式）用来处理请求
2. 调用： DispatchServlet根据HTTP请求信息和Web应用程序配置将请求分配给适当的控制器
3. 服务调用：控制器与服务层交互
4. 填充模型：控制器使用从服务层获得的信息填充模型
5. 创建视图：根据模型创建视图
6. 响应

## ajax调用
1. 请求: 准备XMLHttpRequest并提交给服务器。 调度程序将请求分派给适当的控制器
2. 响应: 控制器与服务层交互，相应数据将被格式化并发送到浏览器，此时并不涉及任何试图。浏览器接收数据并对现有视图进行部分更新
