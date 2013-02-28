---
layout: post
title: "Backbone学习1"
date: 2013-01-28 12:50
comments: true
categories: [Backbone]
---
## Backbone 简介

Backbone 是一个前端 JS 代码 MVC 框架，被著名的 [37signals](http://37signals.com/) 用来构建他们的移动客户端。它不可取代 Jquery，不可取代现有的 template 库。而是和这些结合起来构建复杂的 web 前端交互应用。

如果项目涉及大量的 javascript 代码，实现很多复杂的前端交互功能，首先你会想到把数据和展示分离。使用 Jquery 的 selector 和 callback 可以轻松做到这点。但是对于富客户端的WEB应用大量代码的结构化组织非常必要。

Backbone 就提供了 javascript 代码的组织的功能。Backbone 主要包括 models, collections, views 和 events, controller 。

Models 用来创建数据，校验数据，存储数据到服务器端， Collections 包含你创建的 functions ，Views 用来展示数据。

Models 还可以绑定事件。比如用户动作变化触发 models 的 change 事件，所有展示此model 数据的 views 都会接收到 这个 change 事件，进行重绘。

Backbone托管在github上,地址:[Backbone](https://github.com/documentcloud/backbone)

### 例子
`
