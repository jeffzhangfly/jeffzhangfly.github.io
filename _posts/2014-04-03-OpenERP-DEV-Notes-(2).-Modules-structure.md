---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}


##1.3 Modules

###1.3.1 模块结构

一个模块包含以下要素:

业务对象: 定义为派生自osv.Model的Python类, 这些资源的持久化由OpenERP的ORM管理.

数据: 保存元数据(视图和工作流定义), 配置数据(模块参数)和demo数据(可选但推荐测试用)的XML/CSV文件.

报表: RML(XML 格式)文件. HTML/MAKO或OpenOffice报表模板, 可以与任意类型的业务数据合并, 并产生HTML, ODT或PDF报表.

![OE Module composition](https://lh4.googleusercontent.com/-TUpcoLCQCiw/Uz0bz5OzNiI/AAAAAAAAATg/cei0vdpreug/s800/OE_module_composition.png)

例如, 创建一个'OpenAcademy'模块示例, 需要下列步骤:

* 在source/addons目录下创建一个*openacademy*子目录;
* 创建一个模块import文件__init__.py文件;
* 创建一个模块清单(manifest)文件__openerp__.py;
* 创建包含对象的Python文件s;
* 创建xml文件定义模块数据, 例如视图, 菜单项或demo数据;
* 选择性地创建报表和工作流定义文件;

####\_\_init\_\_.py

Python的import文件, OpenERP模块也是一个标准的Python模块, 这个文件导入该模块所有其他的python文件和子模块. 例如, 如果一个模块仅包含一个名为openacademy.py的文件, \_\_init\_\_.py文件的内容应该是:

    import openacademy

####\_\_openerp\_\_.py

每个模块目录下必须有这个文件, 这个文件中, 用python的字典确定:

* 服务器初始化时需要解析的XML文件;
* 该模块的依赖项;
* 定义额外的元数据.

这个文件包含的字典中需要有这些key:

>***name***:             The name of the module in English.

>***version***:          The version of the module.

>***summary***:          Short description or keywords

>***description***:      The module description (text).

>***category***:         The categrory of the module

>***author***:           The author of the module.

>***website***:          URL of the website of the module.

>***license***:          The license of the module (default: AGPL-3).

>***depends***:          List of modules on which this module depends beside base.

>***data***:             List of .xml files to load when the module is installed or updated.

>***demo***:             List of additional .xml files to load when the module is installed or updated and demo flag is active.

>***installable***:      True or False. Determines whether the module is installable or not.

>***auto_install***:     True or False (default: False). If set to ‘‘True‘‘, the module is a link module. It will be installed as soon as all its dependencies are installed.

例如, **openacademy**模块的\_\_openerp\_\_.py中的字典如下:

    {
        "name" : "OpenAcademy",
        "version" : "1.0",
        "author" : "OpenERP SA",
        "category" : "Tools",
        "depends" : ["mail"],
        "data" : [
            "openacademy_view.xml",
            "openacademy_data.xml",
            "report/module_report.xml",
            "wizard/module_wizard.xml",
        ],
        "demo" : [
                "openacademy_demo.xml"
        ],
            "installable": True,
    }

####对象(Objects)

OpenERP中, 所有的资源都是对象(Objects), 例如: 发票, partners等. 元数据也是对象: 菜单(menus), 动作(actions), 报表(reports)......对象命名是分层的, 例如:

* account.transfer: a money transfer
* account.invoice: 一个发票
* account.invoice.line: 一个发票行

一般情况下, 第一个单词是模块名(account, stock, sale), 这些对象定义为osv.Model的派生类.

可以直接使用SQL语句来操作PostgreSQL数据库, 但是这样做事危险的, 可能会导致数据不一致等等错误.

####XML文件

模块目录下的xml文件用于模块安装/更新时初始化/更新数据库, 例如:

* 定义初始和示例数据;
* 定义视图;
* 定义报表;
* 定义工作流;

关于xml文件结构的更多细节见***TODO:换成文章链接***.

####Record Tag
