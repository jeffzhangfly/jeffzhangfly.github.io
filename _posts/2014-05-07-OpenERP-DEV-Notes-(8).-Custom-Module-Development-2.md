---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}

#编写一个培训管理模块

参考: [oecn_training](http://www.openerp-china.org/index.php?page=developer)

##需求描述

1. 输入和查询课程,把信息储存到课程对象里
2. 课程包含以下信息：名称，价格，天数，开始日期，教师，学员
3. 每个课程可以有多个学员，要记录学员的姓名、电话、电子邮件
4. 课程可以添加教材和作业等文档附件
5. 用户可以设置默认值以加速输入
6. 可以按名称查询课程，也可以用其他信息查找课程，并保存常用查询条件
7. 可以导出课程信息到excel文件，并支持导入
8. 可以按日期查看课程，并调整课程时间
9. 老师只能看到自己的课程

##编写课程模块

###PyCharm中创建工程

PyCharm中创建一个工程my_training, 存放在OE addons目录下, 并在该工程中打开OE目录(见之前的文章).

###文件结构

模块名命名为"my_training", 目录下四个文件, 如下:

    |--my_training
         |--__init__.py
         |--__openerp__.py
         |--lesson.py
         |--lesson_view.xml

###\_\_init\_\_.py

Python模块描述文件, 每个OE模块也是一个Python模块.

    # -*- coding: utf-8 -*-
    import lesson

###\_\_openerp\_\_.py

    # -*- coding: utf-8 -*-
    {
        "name": "My Training", #模块名
        "version": "1.0",        #模块版本
        "description": 'My Training Demo', #模块说明
        "author": "Jeff",    #作者
        "website": "http://www.jeffzhang.cn", #网址
        "depends": [],                      #依赖的模块
        "data": ["lesson_view.xml"],
        "demo": [],
        "installable": True,                #可否安装
        'auto_install': False,
     }

###lesson.py

    # -*- coding: utf-8 >-*-

    from openerp.osv import fields, osv

    class my_training_lesson(osv.osv):
        _name = 'my.training.lesson'
        _discription = u'培训课程示例'
        _columns = {
            'name': fields.char(u'课程名',size=64, select=True),
            'date_start': fields.date(u'开始日期', select=True),
            'total_day': fields.float(u'总天数', digits=(16,1)),
            'teacher': fields.many2one('res.users', u'授课老师'),
            'students': fields.many2many('res.partner', string=u'学生'),
            'price': fields.float(u'价格', digits=(16,2)),
        }

    my_training_lesson()

###lesson_view.xml

    <?xml version="1.0" encoding="utf-8"?>
    <openerp>
        <data>
        <!--定义表单视图-->
            <record model="ir.ui.view" id="my_training_lesson_form_view">
                <field name="name">课程表单</field>
                <field name="type">form</field>
                <field name="model">my.training.lesson</field>
                <field name="arch" type="xml">
                    <form string="课程表单">
                        <field name="name"/>
                        <field name="date_start"/>
                        <field name="total_day"/>
                        <field name="price"/>
                        <field name="teacher"/>
                        <field name="students" colspan="4"/>
                    </form>
                </field>
            </record>
            <!--定义列表视图-->
            <record model="ir.ui.view" id="my_training_lesson_tree_view">
                <field name="name">课程列表</field>
                <field name="type">tree</field>
                <field name="model">my.training.lesson</field>
                <field name="arch" type="xml">
                    <tree string="课程列表">
                        <field name="name"/>
                        <field name="date_start"/>
                        <field name="teacher"/>
                        <field name="price" sum="合计"/>
                    </tree>
                </field>
            </record>
    <!--定义视图动作-->
        <record model="ir.actions.act_window"  id="action_my_training_lesson">
            <field name="name">课程</field>    <!--这个字符串即课程对应列表或编辑界面的标题-->
            <field name="res_model">my.training.lesson</field>
            <field name="view_type">form</field>
            <field name="view_mode">form,tree</field>
            <field name="view_id" ref="my_training_lesson_tree_view"/>
        </record>
    <!--定义菜单-->
        <menuitem id="my_menu" name="培训示例"/>
        <menuitem id="my_training_menu" name="培训" parent="my_menu"/>
        <menuitem id="my_training_lesson_menu" name="培训课程" parent="my_training_menu" action="action_my_training_lesson"/>
    </data>
    </openerp>

##通过继承修改模块

Custom Module Development第一部分中说明了怎么扩展一个系统模块, 这部分扩展刚才编写的课程模块.

###PyCharm中创建工程

PyCharm中创建一个工程my_training_classroom, 存放在OE addons目录下, 并在该工程中打开OE目录(见之前的文章).

###文件结构

模块名命名为"my_training_classroom", 目录下六个文件, 如下:

    |--my_training_classroom
         |--__init__.py
         |--__openerp__.py
         |--lesson.py  #继承my.training.lesson对象的文件
         |--classroom.py
         |--lesson_view.xml
         |--classroom_view.xml

###\_\_init\_\_.py

    # -*- coding: utf-8 -*-
    import lesson
    import classroom

###\_\_openerp\_\_.py

    # -*- coding: utf-8 -*-
    {
        "name": "My Training Classroom", #模块名
        "version": "1.0",        #模块版本
        "description": 'My Training Demo -- ClassRoom', #模块说明
        "author": "Jeff",    #作者
        "website": "http://www.jeffzhang.cn", #网址
        "depends": ["my_training"],                      #依赖的模块
        "data": [
            "lesson_view.xml",      # 需要继承的视图
            "classroom_view.xml",],   # 模块更新时读入的文件
        "demo": [],
        "installable": True,                #可否安装
        'auto_install': False,
     }

###classroom.py

    from openerp.osv import fields, osv

    class my_training_classroom(osv.osv):
        _name = 'my.training.classroom'
        _discription = u'教室'
        _columns = {
            'number': fields.char(u'编号', size=64, select=True),
            'capacity': fields.integer(u'容纳人数', select=True),
            'location': fields.char(u'地点', size=125, select=True),
        }
    my_training_classroom()

###lesson.py

    from openerp.osv import fields, osv

    class my_training_lesson(osv.osv):
        _name = 'my.training.lesson'  #本对象的名称
        _inherit = 'my.training.lesson'  #要继承的对象的_name
        _columns = {
           'classroom_id': fields.many2one('my.training.classroom', u'教室'), #添加一个教室属性，为多对一对象。
        }

    my_training_lesson()

###lesson_view.xml

    <?xml versi>on="1.0" encoding="utf-8"?>
    <openerp>
        <data>
            <!--教室-->
                <record model="ir.ui.view" id="my_training_lesson_from_inherit_classroom_view">
                <field name="name">课程教室继承视图</field>
                <field name="type">form</field>
                <field name="model">my.training.lesson</field>
                <field name="inherit_id" ref="my_training.my_training_lesson_from_view"/>
                <field name="arch" type="xml">
                    <field name="name" position="after">
                        <field name="classroom_id"/>
                    </field>
                </field>
            </record>
        </data>
    </openerp>

###classroom_view.xml

    <?xml version="1.0" encoding="utf-8"?>
    <openerp>
        <data>
            <!--教室-->
                <record model="ir.ui.view" id="my_training_classroom_from_view">
                <field name="name">教室</field>
                <field name="type">form</field>
                <field name="model">my.training.classroom</field>
                <field name="arch" type="xml">
                    <form string="教室表单">
                        <field name="number"/>
                        <field name="capacity"/>
                        <field name="location" />
                    </form>
                </field>
            </record>
             <!--定义列表视图-->
            <record model="ir.ui.view" id="my_training_classroom_tree_view">
                <field name="name">教室列表</field>
                <field name="type">tree</field>
                <field name="model">my.training.classroom</field>
                <field name="arch" type="xml">
                    <tree string="教室列表">
                        <field name="number"/>
                        <field name="capacity" />
                        <field name="location" />
                    </tree>
                </field>
            </record>
    <!--定义视图动作-->
        <record model="ir.actions.act_window"  id="action_my_training_classroom">
            <field name="name">教室</field>
            <field name="res_model">my.training.classroom</field>
            <field name="view_type">form</field>
            <field name="view_mode">form,tree</field>
            <field name="view_id" ref="my_training_classroom_tree_view"/>
        </record>
    <!--定义菜单-->
            <menuitem id="my_training_classroom_menu" name="教室" parent="my_training.my_training_menu" action="action_my_training_classroom"/>
        </data>
    </openerp>


