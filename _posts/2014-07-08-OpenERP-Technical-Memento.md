---
layout: post
category : OpenERP
tagline:
tags : [OpenERP, Python]
---
{% include JB/setup %}

#使用开源OpenERP7.0进行RAD

OpenERP是一个在AGPL协议下开源发布的现代商务应用集, 基于一个用Python编写的模块化, 可伸缩, 直观易用的RAD框架.

OpenERP为快速构建应用提供一个完整的, 模块化的工具箱, 包括: 集成ORM支持; 基于模板的MVC接口; 报表系统; 自动国际化; 等等.

##OpenERP安装

###源码安装

1. 安装bzr;
2. bzr cat -d lp:~openerp-dev/openerp-tools/trunk setup.sh | sh # Get Installer
3. make init-v70    # Install OpenERP 7.0
4. make server      # Start OpenERP Server with embedded Web

###创建数据库

启动server后, 访问http://localhost:8069. 将直接打开数据库管理页面, 需要首先创建一个数据库. 每个数据库有它自己的模块和配置, 也可以创建一个代demo数据的数据库.

##创建一个OpenERP模块: idea

此文档中使用一个假想的idea模块作为例子, 这个模块的功能设计为帮助激发minds, 突然想到的思路很容易忘掉, 所以应该记录下来. 这个模块用于记录这些点子, 并且能够对它们进行排序和打分.

    模块开发
    OpenERP使用模块作为特性的包装单元, 这样可以促进可维护和鲁棒的开发. 每个模块以一种适当级别的抽象和明确的MVC模式提供独立的特性.

###模块的组成

一个模块可能包含下列要素:

* 业务对象: 声明为一个派生自osv.Model的类, 它的持久化在OpenERP管理下完成;
* 数据: 携带元数据(views和workflows的声明)的XML/CSV文件, 配置数据(模块参数化), demo数据;
* 向导: 用于帮助用户的有状态的交互试forms, 经常见于资源上的关联操作;
* 报表: RML(XML格式), MAKO或OpenOffice报表模板, 可以与任意数据合并后产生HTML, ODT或PDF格式的报表.

###典型的模块结构
