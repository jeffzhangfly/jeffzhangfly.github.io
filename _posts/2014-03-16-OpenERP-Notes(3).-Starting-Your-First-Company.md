---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}

##概述
首先查看业务需求, 决定需要哪些模块, 并创建一个OpenERP数据库并配置需要的公司信息.
然后看看如何创建, 查看信息, 完成一个SO流程.

###模块化的实现
初次安装完成OpenERP后, 仅有一个基本的消息系统, OpenERP的更多功能模块, 可以在随后的对需求更明确的情况下逐步地安装到系统中.

不要一开始就把所有模块都安装上, 应该把部署过程分为一个个小的阶段.

###背景介绍

Silkworm公司是一个开展屏幕打印, 刺绣(embroidery)和图形设计业务的公司. 将以此公司为例, 首先实现SO(Sales Order)模块, 将产品(T恤)的销售纳入管理中.

##创建数据库
![创建数据库](https://lh5.googleusercontent.com/-znSvr6T_6J4/UyWUxi1Zs5I/AAAAAAAAAOc/k6uwDyRtC3g/s800/OE_create_db.png "创建数据库")

##安装销售管理(Sales Management)模块
在模块列表中找到销售管理模块, 安装之.
![模块列表](https://lh6.googleusercontent.com/-DFXuwiFC57U/UyWUxjYuNqI/AAAAAAAAAOc/0ZsMWcDYsUE/s800/OE_modules_list.png "模块列表")
经过一会的等待, 销售管理安装完成.

##配置会计数据
销售管理模块安装后, 显示如下的配置会计数据提示.
![配置会计数据](https://lh4.googleusercontent.com/-PxujzUQ9m1A/UyWWlAaes3I/AAAAAAAAAO0/a0_ibeEqEew/s800/OE_config_accounting.png "配置会计数据")
选择中国会计科目表, 点击继续, 一会后弹出会计选项配置. 还可以选择"小企业会计科目表".
![会计选项](https://lh3.googleusercontent.com/-8mkKo0ZnUIU/UyWYLbkQ0PI/AAAAAAAAAPM/_RHDs4deLFk/s800/OE_accounting_option.png "会计选项")

##创建第一位客户

销售模块安装完成后, 自动打开创建客户功能, 并提示你开始创建第一位客户.
![创建客户](https://lh4.googleusercontent.com/-NGkAr0fcWY0/UyWdKZr9dyI/AAAAAAAAAP0/yehuD-56qtc/s800/OE_create_customer.png "创建客户")

###"是一个公司?"
如果选择了"是一个公司", 下方将会在显示额外的信息填写选项.

###编辑客户信息--销售与采购页

客户信息页面的地步分为几个tabs和pages, 这里填写更多的信息. 在销售与采购页, 可以填写这些信息, 例如销售员和各种Email.

![销售与采购](https://lh6.googleusercontent.com/-SRJuTpGjrDg/UyWgE4eJNJI/AAAAAAAAAQM/-XW6IdOXg5I/s800/OE_create_customer_salesNpurchases.png "销售与采购")

**1 销售员(Salesperson)**

通过设定销售员可以为这位客户制定一个销售员. 这个字段不是必须的, 如果使用CRM模块的话, 这个字段一般会用到.

**2 编号(Reference)**

编号字段用于处理已有信息系统中的客户编号, 如果没有历史遗留客户数据问题, 这个字段可以用于其他目的, 或者直接留空.

**3 语言(Language)**


**4 日期(Date)**

系统使用这里填写的日期作为得到这个用户的日期, 也可以将这个字段留空.

**5 客户(Customer)**

OpenERP使用一种唯一的方法保存人员相关的数据, 所有个体数据保存在同样的表中(res_partner), 无论他们是客户还是供应商. 张三是一个客户, 所以勾选客户checkbox.

**6 供应商(Supplier)**

一个个体可以即是客户又是供应商.

**7 有效(Active)**

如果想不显示一个客户, 但是又不想从DB中删除他, 那么可以用这个"有效"字段来控制.

    OpenERP的很多类型的数据中, 都有这个"有效"字段, 可以方便地无效某条数据但不从DB中删除它.

**8 通过Email接收消息(Receive Messages by Email)**

设定与客户交流的水平, 以及什么情况下客户收到电子邮件通知.

**9 Opt-Out**

Opt-Out标志允许你阻止任何自动的或市场活动相关的电子邮件发送到客户email地址.

###编辑客户信息--会计页

因为添加新客户时必须提供一些潜在的基本信息, 所以需要填写会计页页面的信息. 只有"应收科目"和"应付帐"是必填的.
![会计页](https://lh4.googleusercontent.com/-OOW3nfv2PU4/UyZm517St3I/AAAAAAAAAQo/et5sPGlg3Mc/s800/OE_SO_customer_accounting.png "会计页")

**1 财务结构(Fiscal Position)**

税务状态. OpenERP中有两种: 正常(Normal Taxes)和免税(Tax Exempt). 这个字段不是必须的, 并且在生成sales order时可以重载此项.

**2 应收科目(Account Receivable)**

为这个客户指定默认收账帐号类型. 此字段必填.  <font color="yellow">TODO:了解相关的财务知识.</font>

This field is required, and the account will be automatically debited when a customer is invoiced. When the invoice is paid, then the accounts receivable account will be credited.

**3 客户付款条款(Customer Payment Term)**

一般不同的客户有不同的付款条款(期限). 默认提供: 马上, 15天, 30天.

**4 应收款合计(Total Receivable)**

新客户是0.

**5 信用额度(Credit Limit)**

允许对客户建立信用机制, 例如对于新客户, 要求立刻付款, 所以这里的信用额度也是0.

**6 Latest Full Reconciliation Date**
 <font color="yellow">TODO:</font>

**7 应付款(Account Payable)**

如果该客户是一个供货商, 那么使用这里.

**8 供应商付款条款(Supplier Payment Term)**

用于供货商客户.

**9 应付款合计(Total Payable)**

用于供货商客户.

**10 银行账号(Bank Accounts)**

屏幕底部, 可以为客户增加银行账号, 点击"添加一个帐号", 弹出添加银行账号界面进行添加.


##创建一个产品

###产品名称(Product Name)

Medium White T-Shirt

###种类(Category)

默认会添加到"所有产品"(All products). 可以自定义产品分类.

###可销售(Can be Sole) checkbox

类似"有效"(Active).

###创建一个产品--信息页

**1 产品类型**

分为"消耗品"和"服务". 服务类型的产品不会再购买订单中创建采购项, 消耗品类型是真实可卖的, 并且将会产生销售订单.

**2 销售价格**

16.50

**3 编号(Internal Reference)**

选填, 可以用于企业内部对产品进行编号的机制.

**4 EAN13 条码**

###创建一个产品--Procurements page

**1 成本(Cost)**

没有安装购买模块, 也可以为产品指定一个成本. 这个数值将用于财务中的标准股票期权, 并且如果安装了购买模块(purchaseing module), 这个价格作为一个购买订单的基本价格.

**2 供应商说明(Description for Suppliers)**

这个字段允许为一个产品指定其作为供应商的描述文本. 在一些工业领域, 提供这样的产品信息是必不可少的.

###创建一个产品--盘点页面(Inventory)

**1 状态(Status)**

    开发中
    常规
    生命周期结束
    作废

**2 产品经理**

可以为每种产品指定一个产品经理, 这样做便于产生报表和更加智能的仪表盘. 例如, 一个工程师可以配置他的仪表盘仅显示他作为产品经理的产品.

###创建一个产品--销售页面(Sales)

这里可以设置质保时间以及报价单上的文本说明.

###创建一个产品--会计页面(Accounting)

这里设置税率(客户税和进项税).

##设置公司信息

选择"设置"=>"公司", 选择"Your Company"进行编辑. 比较简单, 不再记录.

##输入第一个销售订单(sales order)

点击"销售"=>"销售订单", 点击创建新建一个新的销售订单.

**1 选择客户**

选择刚才创建了的张三.

**2 日期**

**3 客户关联单号(Customer Reference)**

这个值用于与销售相关联, 例如可能希望保存一个该用户怎么获得的引用.

**输入销售订单的行项**

点击下方的"增加一个项目(Add a item)", 将会插入一个新的行, 在这里填写刚才输入的产品以及数量等信息.

点击保存后, 将显示客户地址, 并且金额的小计(Subtotal), 合计(Total)将计算并显示.

###理解销售订单工作流程

销售订单输入后, 它的状态处于"报价单草稿(Draft Quotation)"状态, 报价单的状态显示在报价单的右上方(OpenERP7). "报价单草稿"=>"报价单发送(Quotation Sent)"=>"已完成(Done)".

从这个流程可以看出来, 报价单需要发送才能确认完成. 报价单页面左上方显示当前可以进行的操作.

**以邮件发送(Send by Email)**

以设定的Email地址发送报价单copy.

**打印**

生成一个PDF格式的文档.

**确认订单(Confirm Sale)**

订单确认后, 报价单将转变为一个销售订单, 并进入到后续的销售流程中.

**取消报价(Cancel Quotation)**

取消订单终止报价工作流, 但不会被删除该报价单, 这些数据用于归档,

###开具销售发票(Invoicing the sale)

不同的业务类型在确认销售订单后会有不同的工作流程. 在本例中, 下一步是开具发票. 确认订单后, 点击"生成发票(Create Invoic)"按钮, 将显示一个"开票订单(Invoice Order)"窗口, 选择开票类型, 然后生成发票即可.

**生成发票(Creating the invoice)**

初始生成的发票处于"草稿(Draft)"状态, 点击"查看发票"=>"使其有效(Validate)"确认发票并进入到下一个流程.

