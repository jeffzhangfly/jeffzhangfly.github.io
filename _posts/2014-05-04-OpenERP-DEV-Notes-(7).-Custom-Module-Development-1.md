---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}

##目标

* OpenERP模块基本结构
* 使用模块添加额外的字段(Date Required和Rush Order)
* 扩展视图, 让OpenERP能够显示新的字段
* 修改用于OpenERP工作流的可用状态

##创建第一个OpenERP模块
不修改OE源码情况下写自己的模块扩展OE是OpenERP框架一个很好的特性. 我们所做的所有修改都放在我们自己的目录, 自己的文件中.

每个OE模块能够被OE框架正确认识并安装, 一旦成功安装了我们的模块, OE框架将会用该模块的功能扩展OE.

##指定一个目录保存我们的OE模块

两种方式:

* 在OE的addons目录下保存我们的模块;
* 创建一个独立的目录保存我们的模块, 这种方式的有点是能够将OE标准模块与我们的模块分开.

对于第二种方式, 需要修改OE的配置文件:

    [options]
    admin_passwd = admin
    db_user = jeff
    db_password = greater
    addons_path = /home/openerp/server/addons,/home/mymodules

这样, home目录下的mymodules目录中保存我们定制的OE模块.

##模块目录内容

在OE的addos目录下(addons_path中设定)创建一个目录silkworm保存我们的silkworm模块. 在silkworm目录下创建两个文件:

* \_\_init\_\_.py
* \_\_openerp\_\_.py

\_\_init\_\_.py文件的目的是指定该模块中想要包含的Python文件(不用管文件的扩展名).

    import silkworm

\_\_openerp\_\_.py文件是OE模块的清单文件, 向OE框架说明该模块的一些属性. 这个文件有时也被称为模块描述文件, 用Python中的字典(dictionary)描述.

    {
           'name': 'Screen Printing',
           'version': '1.0',
           'description': """
           This module adds functionality for
                screen printing companies
           """,
           'author': 'Greg Moss',
           'depends': ['base','sale'],
           'data': ['silkworm_view.xml'],
           'demo': [],
           'installable': True,
           'auto_install': False,
    }

在安装OpenERP模块时, \_\_openerp\_\_.py文件给出框架配置该模块所需的各种信息.

* name: 显示在OpenERP模块列表中;
* version: 模块版本号;
* description: 安装模块时显示;
* author: 作者;
* depends: 前面几项(name, version等)是用于文档的说明性的元素, depends告诉框架这个模块是建构在哪些其他模块基础上的. 至少要依赖于base, 本例中因为扩展了sales order系统, 所以也要依赖于sale模块;
* data: 指定模块的xml视图文件. 如果希望改变OE的forms或UI, 创建一个视图文件. 其他类型的数据文件也可以在这里指定, 例如包含初始化数据或访问权限的数据. 本例中只有一个文件silkworm_view.xml文件;
* demo: OE提供了一种方便的方式来包含定制模块的说明数据, 本例中留空, 但是如果希望在模块安装时可以添加一些说明数据, 用这项指定;
* installable: 使用此项可以临时禁止一个模块的安装;
* auto_install: 如果此项为True, OE将会在该模块的所有依赖项安装好之后自动安装这个模块, 大部分情况下不会指定自动安装.

##silkworm.py中扩展OpenERP模型

为了向我们的sales order添加Date Required和Rush Order字段, 首先创建silkworm.py文件, 然后添加下面的代码到silkworm.py文件中:

    from openerp.osv import osv, fields

    class silkworm_sale_order(osv.Model):
        _inherit = 'sale.order'
        _columns = {
           'x_daterequired': fields.date('Date Required', required=True),
           'x_rush': fields.boolean('Rush Order'),
            }

###继承sales order

代码中, 创建了一个silkworm_sale_order类, 派生自osv.Model, Python的OO继承.

\_inherit\_ = 'sale.order', 这一句指出这个类继承了sale.order, OpenERP的继承.

\_columns字典列出了我们向sales order模块添加的两个自定义字段.

###将自定义字段添加到sales order视图中

\_\_openerp\_\_.py文件中的'data'指定了视图xml文件, silkworm_view.xml.

    <?xml version="1.0" encoding="utf-8"?>
       <openerp>
           <data>
              <record id="sale_view_order_form" model="ir.ui.view">
                <field name="model">sale.order</field>
                <field name="inherit_id" ref="sale.view_order_form"/>
                <field name="arch" type="xml">
                  <field name="client_order_ref" position="after">
                    <field name="x_daterequired"/>
                    <field name="x_rush"/>
                  </field>
                </field>
           </record>
        </data>
    </openerp>

OE中的视图定义使用XML语法. OE视图文件用openerp标记包裹, 下一级是data标记. 为了在自定义的OE模块中修改或添加视图, 添加record标记.

    <record id="sale_view_order_form" model="ir.ui.view">

每一个record必须有一个id属性, 另外这里我们指定了这个record的model属性为ir.ui.view.

    <field name="model">sale.order</field>

接下来, 应该指定这个record对应的model, 本例中为sale.order, 因为我们为sale.order添加了字段. 如果我们为purchase order添加了字段, 那这里就应该是purchase.order.

> 在OE的开发模式下, 鼠标移动到字段上可以查看该字段所属的模型; 在开发模式下的Manage Views中, 也可以发现想要使用的view的名字. 这些功能可以方便OpenERP的模块开发.

    <field name="inherit_id" ref="sale.view_order_form"/>

类似于我们的模型派生自sale.model, 我们的视图也应该从sale.view\_order\_form视图派生而来, 这样我们可以增加一些自定义的字段. 开发模式下使用"Manage Views"查看这些信息.

    <field name="arch" type="xml">

*arch*项是所有视图改变都需要的, 不需要知道为什么, 用吧.

接下来就到了给新增的自定义字段指定显示位置了.

    <field name="client_order_ref" position="after">

添加字段到form上时, 应该明确地确定这个字段应该放在什么位置. 例如上面的那行代码, 告诉OpenERP框架首先找到*client_order_ref*字段, 然后在它的后面(*position="after"*)显示增加的两个字段.

*client_order_ref*字段怎么找到的? 只需要激活OE的开发者模式(OE的关于对话框中激活), 打开相应的view, 然后将鼠标悬停在字段的label上即可在tooltip上显示出来.

    <field name="x_daterequired"/>
    <field name="x_rush"/>

上面这两句向form中添加我们新增的字段显示.

##安装模块

这个例子很简单, 仅仅把两个字段添加到了sales order form中, 这个模块目录中有四个文件:

* \_\_init\_\_.py
* \_\_openerp\_\_.py
* silkworm.py
* silkworm_view.xml

###参数设定

    OpenERP addons_path设定
    首先./openerp-server -s, 在$HOME/下生成.openerp-serverrc文件, 修改其中的addons_path选项, 增加addons目录. 这里要注意, 多个路径之间用逗号分隔并且不能有空格.

###安装

需要该用户启用了技术特性(设置->用户->编辑->访问权限->勾选"技术特性").

首先"设置->更新模块列表", 然后输入过滤关键字找到模块安装. 安装后打开一个sale order(销售订单), 可以看到在"客户关联单号(client_order_ref)"后增加了两个新的字段.

    注意: 安装模块时, 不要使用Modules菜单节中的Apps项, 该项仅显示OpenERP的app repository中的标准模块.

