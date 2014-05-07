---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}


##1.3 Modules

###1.3.2 对象, 字段和方法

###OpenERP Objects

OpenERP中, 所有数据资源通过objects来访问.

注意, 每一类资源对应一个object, 而不是每个资源对应一个对象. 例如, res.partner管理所有的partner, 而不是一个partner对应一个res.partner. 用OO的话来说, 一个object是一个类而不是一个对象.

这样, 对象的每个方法有一个"ids"参数, 用来确定这个方法应用到的资源.

例如, 如果两个partner, id分别为1和5, 那么向他们发送email的方法"send_email"方法应该这样调用:

    res_partner.send_email(..., [1,5], ...)

对于开发者来说:

* OpenERP的"objects"对应OO中的类
* 一个OpenERP的"resource"对应OO中的类的实例

一个OpenERP的"resource"可以通过"browser"类方法(OpenERP的对象方法)转换为一个Python对象.

###ORM

OpenERP中, ORM为objects和PostgreSQL中的数据架起一座桥梁.

###OpenERP对象属性

####对象介绍

为了定一个新的对象, 必须定义一个新的Python类并实例化它, 这个类必须派生自osv类(osv module中).

####对象定义

使用一些预定义名称的域来定义对象, 其中两个是必须的(_name和_columns), 其他是可选的.

####预定义域

**_auto**: 确定是否根据对象自动创建PostgreSQL表. 例如, 当处理由PostgreSQL的视图产生的对象时, 将此域设置为False. 查看"从PostgreSQL视图生成报表"获得更多信息.

**_columns(必需)**: 对象字段定义, 查看"字段"部分获取更多信息.

**_constraints**: 对象约束, 查看"约束"部分获取更多信息.

**_sql_constraints**: 对象的SQL约束, 查看"SQL约束"部分获取更多信息.

**_default**: 对象一些字段的默认值.

**_inherit**: 当前对象派生自的osv对象的名字. 查看"对象继承"部分获取更多信息.

**_inherits**: 当前对象派生自的osv对象列表, 这个列表必需是一个下列形式的python字典: {‘name_of_the_parent_object’: ‘name_of_the_field’, ...}, 默认值为{}. 查看"对象继承"部分获取更多信息.

**_log_access**: 确定资源写操作是否记入日志. 如果为true, 四个字段将会添加到表中: create_uid, create_date, write_uid, write_date. 这些字段用于保存记录的创建人id和时间, 以及修改人id和时间. 这些数据可以通过perm_read方法获得.

**_name(必需)**: 对象名, 默认值: None.

**_order**: 用于排序检索或读方法结果的fields. 默认值: 'id'. 示例:

    _order = "name"
    _order = "date_order desc"

**_rec_name**: 资源名所保存的字段名, 默认值为:'name'. 注意, 默认情况下"name_get"方法简单地返回这个字段的内容.

**_sequence**: 管理对象ids的SQL序列名称, 默认值: None.

**_sql**: 对象创建时执行的sql代码, 只有_auto设置为True有效. 当表创建后这段代码将被执行.

**_table**: SQL表名, 默认值为_name域的值(用下划线'\_'替换_name中的'.')

####对象继承 - _inherit

添加/修改对象的字段, 用继承挺好.

####扩展一个对象

两种方式, 每种都创建一类新的数据, 新的数据保持了原有的字段和行为并且具有了新的字段和行为, 但是这两种方法的后续编程方法有很大的不同.

示例1创建一个新的子类"custom_material", 它可以被处理"network.material"的view或tree看到或使用, 示例2与此不同.

示例1:

    class custom_material(osv.osv):
        _name = ’network.material’
        _inherit = ’network.material’
        _columns = {
            ’manuf_warranty’: fields.boolean(’Manufacturer warranty?’),
        }
        _defaults = {
            ’manuf_warranty’: lambda *a: False,
        }

    custom_material()

**示例1注意: _name == _inherit**

示例1中, 'curtom_material'相比与'network.material'新增了一个字段'manuf_warranty'. 新的类实例能够被能处理"network.material"的view或tree识别.

这种继承在OO中被称为"类继承", 子类继承了父类的数据(fields)和行为(functions).

示例2:

    class other_material(osv.osv):
        _name = ’other.material’
        _inherit = ’network.material’
        _columns = {
                ’manuf_warranty’: fields.boolean(’Manufacturer warranty?’),
        }
        _defaults = {
            ’manuf_warranty’: lambda *a: False,
        }

    other_material()

**示例2注意: _name != _inherit**

示例2中, 'other_material'保留所有'network.material'的字段, 并且增加了一个新的字段'manuf_warranty'. 所有这些字段是'other.material'表的一部分. 这个类的新实例不能被'network.material'表上的view和tree识别.

这种继承被称为"原型继承"(Javascript就是这种继承), 因为新创建的子类从父类(原型prototype)拷贝了所有字段. 子类继承了其父类的所有数据和行为.

####委托继承

语法:

    class tiny_object(osv.osv)
        _name = ’tiny.object’
        _table = ’tiny_object’
        _inherits = {
            ’tiny.object_a’: ’object_a_id’,
            ’tiny.object_b’: ’object_b_id’,
            ... ,
            ’tiny.object_n’: ’object_n_id’
        }
        (...)

对象tiny_object从n个对象(tiny.object_a, tiny.object_b,...,tiny.object_n)继承了所有的列以及所有的方法.

为了从多个表继承, 每个被继承的对象在tiny_object表中增加一列, 该列保存一个外键. object_a_id, object_b_id,...,object_n_id是类型字符串, 并且确定了外键列的标题, 外键列保存从tiny.object_a, tiny.object_b,...,tiny.object_n而来的外键.

这种继承机制被称为实例继承或值继承, 一个资源(实例)具有其parents的VALUES.

####字段介绍

对象包含不同类型的字段, 可以分为三类: 简单类型, 关系类型和函数字段. 简单类型是整形, 浮点, boolean, 字符串等; 关系类型用于表示对象间的关系, 例如one2one, one2many, many2one等. 函数字段是特殊的字段, 因为它们并不保存在数据库中, 而是在使用时由其他字段实时计算得到他们的值.

OpenERP的类初始化代码(\_\_init\_\_()):

    def __init__(self, string=’unknown’, required=False, readonly=False,
                 domain=None, context="", states=None, priority=0, change_default=False, size=None,
                 ondelete="set null", translate=False, select=False, **args) :

可选参数列表如下:

* change_default: 这个字段的值决定用户是否可以为其他字段的默认值. 默认值需要定义在ir.values表中.
* help: 一段说明字段如何使用的文本描述, 当鼠标滑过字段上方时, 这些文字将在tooltip显示出来.
* ondelete: 删除时如何处理相关的记录, 允许的值为: restrict, no action, cascade, set null, set default.
* priority: Not used?
* readonly: 该字段是否只读, 也即不能编辑.
* required: 是否必须.
* size: number characters or digits.
* states:
* string: 显示在标签或列头的文本. 如果包含非ASCII字符, 那么必须用python的unicode字符串. 例如: fields.boolean(u'测试').
* translate: 布尔值, 指示该字段是否翻译.

其他一些可选的参数:

* context:
* domain:
* invisible: 在forms上隐藏字段值, 例如密码.
* on_change:
* relation:
* select:

###字段类型

####基本类型

**boolean**:

    fields.boolean(’Field Name’ [, Optional Parameters]),

**integer**:

    fields.integer(’Field Name’ [, Optional Parameters]),

**float**:

    fields.float(’Field Name’ [, Optional Parameters]),

>注意: 可选参数digits定义数字的精度(包括小数点前后总有效位数)和小数位数(小数点后位数). 如果此参数没有定义, 此字段是一个双精度浮点数. 警告: 浮点数字可能是不准确的, 会导致误差, 所以对于金额之类的精确数字, 应该定义参数.

**char**:

    fields.char(
            ’Field Name’,
            size=n [,
            Optional Parameters]), # where ’’n’’ is an integer.

    示例: ’city’ : fields.char(’City Name’, size=30, required=True),

**text**:

    fields.text(’Field Name’ [, Optional Parameters]),

**date**:

    fields.date(’Field Name’ [, Optional Parameters]),

**datetime**:

    fields.datetime(’Field Name’ [, Optional Parameters]),

**binary**: 二进制链

**selection**: 一个允许用户在一些预定义值中选择的字段

    fields.selection(((’n’,’Unconfirmed’), (’c’,’Confirmed’)),
                   ’Field Name’ [, Optional Parameters]),

>注意: 参数格式为: 字符串元组的元组
>((’key_or_value’, ’string_to_display’), ... )

>注意: 也可以指定一个用来返回元组的函数, 例如

    def _get_selection(self, cursor, user_id, context=None): return (
           (’choice1’, ’This is the choice 1’),
           (’choice2’, ’This is the choice 2’))
    _columns = {
       ’sel’ : fields.selection(
    }

**示例**: 在selection使用many2one的关系字段, 字段定义中添加以下内容:

    ...,
    ’my_field’: fields.many2one(
            ’mymodule.relation.model’,
            ’Title’,
            selection=_sel_func),
    ...,

其中_sel_func定义为:

    def _sel_func(self, cr, uid, context=None):
        obj = self.pool.get(’mymodule.relation.model’)
        ids = obj.search(cr, uid, [])
        res = obj.read(cr, uid, ids, [’name’, ’id’], context) res = [(r[’id’], r[’name’]) for r in res]
        return res

###关系类型

**one2one**: 表示两个对象间1对一的关系, 现在已经不再使用, 用many2one代替之.

    fields.one2one(’other.object.name’, ’Field Name’)

**many2one**: 对象通过这个字段关联父对象, 例如, 员工与所属部门是多对一关系, 多个员工属于一个部门.

    fields.many2one(
            ’other.object.name’,
            ’Field Name’,
            optional parameters)

可选参数:

* ondelete: 当字段对应的资源被删时做什么, 可以选择的操作: 预定义值(cascade, set null, restrict, no action set default)和默认值(set null)
* required: 是否必填
* readonly: 是否只读
* select: 在外键字段创建一个索引

示例:

    ’commercial’: fields.many2one(
            ’res.users’,
            ’Commercial’,
            ondelete=’cascade’),

**one2many**:

    fields.one2many(
            ’other.object.name’,
            ’Field relation id’,
            ’Fieldname’,
            optional parameter)

可选参数:

* invisible:
* states:
* readonly:

示例:

    ’address’: fields.one2many(
            ’res.partner.address’,
            ’partner_id’,
            ’Contacts’),

**many2many**:

    fields.many2many(’other.object.name’,
                     ’relation object’,
                     ’actual.object.id’,
                     ’other.object.id’,
                     ’Field Name’)

其中:

* other.object.name是属于这个关联关系的另一个对象
* relation object是关联表
* actual.object.id和other.object.id是关联表中的字段名

示例:

    ’category_ids’:
       fields.many2many(
        ’res.partner.category’,
        ’res_partner_category_rel’,
        ’partner_id’,
        ’category_id’,
        ’Categories’),

为了双向访问(也即在另一个对象中创建一个字段):

    class other_object_name2(osv.osv):
        _inherit = ’other.object.name’
        _columns = {
            ’other_fields’: fields.many2many(
                ’actual.object.name’,
                ’relation object’,
                ’actual.object.id’,
                ’other.object.id’,
                ’Other Field Name’),
    other_object_name2()

示例:

    class res_partner_category2(osv.osv):
        _inherit = ’res.partner.category’
        _columns = {
            ’partner_ids’: fields.many2many(
                ’res.partner’,
                ’res_partner_category_rel’,
                ’category_id’,
                ’partner_id’,
                ’Partners’),
        }
    res_partner_category2()

**related**: 有时需要引用关系的关系. 例如, 假设有城市->省->国家的关系, 如果需要从城市引用国家, 那么就需要在城市对象上定义一个字段, 如下:

    ’country_id’: fields.related(
        ’state_id’,
        ’country_id’,
        type="many2one",
        relation="res.country",
        string="Country",
        store=False)

其中:

* 第一组参数是字段的引用链, 引用链的最后是希望引用的字段.
* type是希望引用的字段的类型
* 如果希望字段仍然是某种引用, 那就使用relation. relation用于查询表中引用.

###函数字段


