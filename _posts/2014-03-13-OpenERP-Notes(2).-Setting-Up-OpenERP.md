---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}

##了解OperERP架构

###PostgreSQL
开源, 跨平台的ORDMS(object relational database management system), 在BSD风格许可证下发行. 更多信息点击[这里](http://zh.wikipedia.org/wiki/PostgreSQL "中文wiki PostgreSQL词条")查看.

###Writing code with Python

###Following the MVC design

###Designing models

数据库结构由OpenERP模块定义, 当安装模块时, OpenERP框架根据实体模型定义自动地创建所需的表结构. 并且, OpenERP提供一个web接口, 通过它不需要修改OpenERP源代码就可以扩展数据模型.

###Rendering views

OpenERP中的每个view都是使用XML定义的, OpenERP框架负责根据XML内容在浏览器上渲染页面.

###Authoring controllers
架构中的控制器组件可以实现OpenERP应用的业务逻辑和工作流规则. OpenERP中的控制器组件使用Python编写, 保存为OpenERP模块中的一个对象.

##安装

###安装包安装

简单, 省略.

###源码安装

首先安装OpenERP的依赖, 例如PostgreSQL, Python及需要的Python库. 然后就可以开始源码安装.

1. 安装bzr:

    $ sudo apt-get install bzr      (Linux)

    $ sudo brew install bzr         (Mac)

2. 获取Installer并执行:

    $ bzr cat -d lp:~openerp-dev/openerp-tools/trunk setup.sh \| sh

3. 安装OpenERP 7.0源码

    $ make init-v70
