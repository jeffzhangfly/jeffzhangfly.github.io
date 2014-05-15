---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}

###导入OpenERP
创建一个空的工程作为开发OE模块使用的工程, 然后菜单"File"=>"Open Directory..."选择解压后的OpenERP目录, 目录结构如下图所示.

![OE Tree](https://lh5.googleusercontent.com/-SuINzxaUs20/Uzon9hnAMwI/AAAAAAAAASE/2ygcEVRN5mg/s800/oe_tree.png "OE Tree")

在PyCharm中展开工程, 将install子目录下的"openerp-server.conf"拷贝到新加入的openerp根目录下, 并打开进行相应的修改.

###启动运行
在工程目录树根上找到"openerp-server", 右键选择"Run 'openerp-server'"启动服务器, 如果弹出错误, 可能是PostgreSQL没有启动或依赖的Python模块没有安装.

###调试Python
在工程目录树根上找到"openerp-server", 右键选择"Debug 'openerp-server'"以调试模式启动服务器. 如果设置了断点, 就会在运行到断点处停下来.
注意Run和Debug前一定要确定只有一个OE服务器实例启动.

###调试JavaScript
PyCharm里选择"Edit Configurations...".

![Debug JavaScript 1](https://lh3.googleusercontent.com/-3st6TTwlwrs/UzowxGPghnI/AAAAAAAAATI/-WI5KCYOs6Y/s800/oe_debug_js_1.png)

然后在打开的窗口中选择"+"增加一个JavaScript的Debug设置.

![Debug JavaScript 2](https://lh3.googleusercontent.com/-V9UEgj2xfeE/UzowxBmEH_I/AAAAAAAAATI/YfsVMWMudyg/s800/oe_debug_js_2.png)

然后按下图中设定.

![Debug JavaScript 3](https://lh4.googleusercontent.com/-J8tF6m38H3A/UzowxEvpZNI/AAAAAAAAATI/qCB8pmaXH9Y/s800/oe_debug_js_3.png)

设定好这些后, 启动openerp-server(Run或Debug均可), 然后选择js debug启动debug.  提示安装Chrome的JetBrains IDE Support支持插件, 安装之. 安装后的Chrome打开OE后如下图所示.

![Debug JavaScript 4](https://lh3.googleusercontent.com/-hgR7chEljWs/UzowzMYMBGI/AAAAAAAAATI/xxQxga7dypY/s800/oe_debug_js_4.png)


