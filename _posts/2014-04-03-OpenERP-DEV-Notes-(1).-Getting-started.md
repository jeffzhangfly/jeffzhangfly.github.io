---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}


##1.1 起步

###1.1.1 从源码安装

安装各种依赖包, 然后bzr安装, 具体见*OpenERP Technical Memento*.

###1.1.2 命令行参数

./openerp-server --help

###1.1.3 Configuration

两个配置文件: *~/.openerprc*; *~/.openerp_serverrc*. 如果没有发现, 说明服务器和客户端用默认配置启动. 服务端配置文件可以使用*./openerp-server -s*或*./openerp-server --save*创建.

还可以通过命令行参数指定配置文件: *-c CONFIG*; *-config=CONFIG*.

####配置addons位置

addons默认在*server/bin/addons*, 源码安装的在*openerp/addons*.

* 在addons目录创建连接;
* 启动server时用命令行参数设定: *--addons-path=ADDONS_PATH*.

###1.1.4 Start-up脚本

##1.2 架构

###1.2.1 多端三层架构的OpenERP

* 数据库层用于数据存储
* 应用层处理各种功能性工作
* 表示层提供用户接口

OpenERP系统由三部分组成:

* 包含所有OpenERP数据库的PostgreSQL数据库服务器. 数据库中保存了所有的应用数据, 以及很多OpenERP系统配置数据. 可以部署为集群数据库;
* OpenERP服务器, 包括所有的企业逻辑并确保OpenERP runs optimally. OEM引擎用于与PostgreSQL数据库交互, Web层用于服务器与web浏览器之间交互. 可以部署多服务器, 例如使用负载平衡的多服务器互联;
* client是一个javascript应用, 运行在web浏览器中.

**Server - ORM**

ORM层是OpenERP的一个非常突出的特性. 在PostgreSQL之上提供了必须及额外的功能. 数据模型使用Python描述, OpenERP使用ORM来创建下层的数据库表. RDBMS的特性, 例如唯一性约束, 有效的查询等, 都可以通过Python灵活地完成, 例如Python编写的约束可以添加到任意的模型上.

利用ORM可以获得:

* 强力的有效性检查从而保证了一致性;
* 对象级别的接口, 从而能够更有效地设计和实现模块;
* 各个用户和组的低级别上的安全保证;
* 资源组的复杂动作;
* 提供继承服务从而使新的资源能够更好地建模.

 **Server - Web**

现在Web层是一个基于*werkzeug*的WSGI兼容应用.

**Modules**

OpenERP服务器是核心, 对于企业来说, OpenERP的价值在于它的各个功能模块. 模块用来实现各种商务需求.

###1.2.2 MVC架构

略...

###1.2.3 网络通信及WSGI

OpenERP是一个HTTP的web服务, 也可以部署为一个WSGI兼容应用.

WSGI是Python生态系统中用于编写HTTP服务, 应用及中间件的解决方案标准.

