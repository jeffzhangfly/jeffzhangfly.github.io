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

###\_\_init\_\_.py

Python的import文件, OpenERP模块也是一个标准的Python模块, 这个文件导入该模块所有其他的python文件和子模块. 例如, 如果一个模块仅包含一个名为openacademy.py的文件, \_\_init\_\_.py文件的内容应该是:

    import openacademy

###\_\_openerp\_\_.py

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

###对象(Objects)

OpenERP中, 所有的资源都是对象(Objects), 例如: 发票, partners等. 元数据也是对象: 菜单(menus), 动作(actions), 报表(reports)......对象命名是分层的, 例如:

* account.transfer: a money transfer
* account.invoice: 一个发票
* account.invoice.line: 一个发票行

一般情况下, 第一个单词是模块名(account, stock, sale), 这些对象定义为osv.Model的派生类.

可以直接使用SQL语句来操作PostgreSQL数据库, 但是这样做事危险的, 可能会导致数据不一致等等错误.

###XML文件

模块目录下的xml文件用于模块安装/更新时初始化/更新数据库, 例如:

* 定义初始和示例数据;
* 定义视图;
* 定义报表;
* 定义工作流;

关于xml文件结构的更多细节见***xml-serialization section TODO:换成文章链接***.

OpenERP对象可以通过使用该对象相应的XML文件插入到相应的PostgreSQL表中, 一个OpenERP XML文件的一般结构如下:

    <?xml version="1.0"?>
    <openerp>
        <data>
            <record model="model.name_1" id="id_name_1">
                <field name="field1"> "field1 content" </field>
                <field name="field2"> "field2 content" </field>
                (...)
            </record>
            <record model="model.name_2" id="id_name_2">
                (...)
            </record>
            (...)
        </data>
    </openerp>


###Record Tag

Record标记描述新数据, 它有一个必需的属性model, 它的值应该是一个将执行插入数据的对象名; record标记还可以有一个可选的id属性, 如果这个属性赋值了, 那么这个id值可以在这个文件的后面作为这个资源的引用标示符(ID)来使用.

一个record标记可以包含多个field标记, 指出这些字段的值, 如果没有给出字段值则使用默认值.

###The Record Field tag

field标记的属性如下:

* name: 必需的, 字段名
* eval: 可选的, 一个python表达式, 通过它计算值
* ref: 对本文件中出现过的id的引用
* model: 搜索时使用的模型
* search: 一个查询

**示例**:

    <record model="ir.actions.report.xml" id="l0">
        <field name="model">account.invoice</field>
        <field name="name">Invoices List</field>
        <field name="report_name">account.invoice.list</field>
        <field name="report_xsl">account/report/invoice.xsl</field>
        <field name="report_xml">account/report/invoice.xml</field>
    </record>

**下面是一个OpenERP源码中的示例(base模块中的base\_demo.xml)**:

    <record model="res.company" id="main_company">
        <field name="name">Tiny sprl</field>
        <field name="partner_id" ref="main_partner"/>
        <field name="currency_id" ref="EUR"/>
    </record>
    <record model="res.users" id="user_admin">
        <field name="login">admin</field>
        <field name="password">admin</field>
        <field name="name">Administrator</field>
        <field name="signature">Administrator</field>
        <field name="action_id" ref="action_menu_admin"/>
        <field name="menu_id" ref="action_menu_admin"/>
        <field name="address_id" ref="main_address"/>
        <field name="groups_id" eval="[(6,0,[group_admin])]"/>
        <field name="company_id" ref="main_company"/>
    </record>

后一条记录定义了admin用户:

1 login, password等字段简单地直接给出值

2 ref属性允许在记录间建立联系:

    <field name="company_id" ref="main_company"/>

**company_id**字段是一个从user对象到公司对象的many-to-one关系, **main_company**是关联的ID.

3 eval属性允许在xml中放置一些python代码:

    <field name="groups_id" eval="[(6,0,[group_admin])]"/>

这里的groups_id字段是一个many2many字段, "[(6,0,[group_admin])]"表示: 移除所有当前user的groups关联并使用一个[group_admin]列表作为新的groups关联, 这里group_admin也是另一个record的id.

4 search属性可以在不知道xml中id的情况下, 通过搜索发现关联的记录. 可以给出一个特定的查询条件来查找记录. 查询条件是一个查询条件元组的列表.

    <field name="partner_id" search="[]" model="res.partner"/>

以上xml代码是一个demo数据中的典型例子: 因为不关心到底使用哪个partner, 所以搜索条件列表为空, 注意此时**model**属性是必需的.

###Function tag

函数表几种可以包含其他函数标记, 属性如下:

* model: 必需, 涉及的model
* name: 必填, 函数名
* eval: 计算表达式
**示例**:

    <function model="ir.ui.menu" name="search" eval="[[(’name’,’=’,’Operations’)]]"/>

###Views

Views是一种在客户端展现对象的方式, Views指出如何在屏幕上摆放从对象获得的数据. Views有两类:

* form views
* tree views

Lists是一种简单的tree views的特例.

一个对象可能有多种views, 第一个定义的一类view(tree, form等)将作为这类的默认view. 这样就可以有一个默认的tree view(作为one2many的view)和一个特定用于显示多些或少些信息的view, 后者在双击菜单项是显示. 例如, 根据不同产品, products有多个views.

views用XML描述.

如果某个对象没有定义view, 那么这个对象可以产生一个默认的基本view来显示自己, 这样可以减小工作量.

###使用示例

打开一个发票时的操作链:

* 一个action请求打开the invoice, 给定了相应的对象数据(account.invoice), 视图以及domain(例如仅显示为支付的invoices)
* 客户端询问(通过XML-RPC)服务器这个invoice对象定义了哪些views, 以及哪些数据必须显示
* 客户端显示相应的view

###To develop new objects

设计新的对象的最小工作: 创建对象, 可选地创建相应的views. PostgreSQL表不需要手动操作, 因为对象可以自动创建它们(或调整已存在的表).

**Reports**: OpenERP使用一个强力且灵活的报表系统, 可以生成PDF或HTML格式的报表. 更多报表信息参见**TODO:修改这里为 报表章节链接**

**Workflow**: 使用对象和views可以方便地定义新的forms, lists/trees, 及它们之间的交互, 但是还需要能够定义这些对象动态特性.

例如:

* 为了确认一个销售订单, 必须产生相对应的发票
* 特定情况下, 支付发票后应该启动发货

工作流用图的形式描述这些交互, 对象可能关联到一个或多个工作流, 但工作流不是必需的, 有些对象没有相关的工作流.

下面是sale orders相关的工作流例子:

在图中, 节点表示要进行的actions, 例如:

* 创建一个发票
* 取消销售单
* 生成发运单

箭头表示条件, 例如:

* 等待订单验证
* 支付发票
* 点击取消按钮

方块节点表示其他工作流, 例如:

* 发票
* 发运

**i18n**: 每个模块都有自己的i18n文件夹. 另外, OpenERP还可以用.po文件来导入/导出. 已安装的翻译文件在安装或升级模块时自动加载.

