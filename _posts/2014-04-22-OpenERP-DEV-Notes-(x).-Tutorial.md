---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}

##1. 创建项目

1.1 PyCharm中新建一个名为*openacademy*的项目;

1.2 添加__init__.py文件, 文件内容:

    # -*- coding: utf-8 -*-
    import openacademy

1.3 添加__openerp__.py文件, 文件内容:

    # -*- coding: utf-8 -*-

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

1.4 添加OpenERP源码

"File"=>"Open Directory..."选择OpenERP源码目录, 拷贝openerp-serer.conf到根目录并配置, 在openerp根目录下的openerp-server上右键并选择"Run 'openerp-server'"启动服务器(注意之前先启动PostgreSQL服务), 访问http://localhost:8069/, 确认成功.

1.5
