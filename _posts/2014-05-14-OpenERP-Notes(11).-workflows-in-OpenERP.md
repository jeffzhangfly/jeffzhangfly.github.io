---
layout: post
category : OpenERP
tagline:
tags : [Python, OpenERP]
---
{% include JB/setup %}

##工作流简介

* 工作流程系统在OpenERP里是非常有用的机制，可以用于及时描述文件(模型)的演进过程。
* 工作流程是完全可以定制的，这些流程可以调整适用于几乎所有公司的作业流程和交易逻辑。 这个工作流程系统使OpenERP非常有弹性，而且可以不用编程增加新功能,就可以支持不断变化的需要。

[官方工作流文档](https://doc.openerp.com/6.1/zh_CN/developer/07_workflows/)

##工作流的目标

* 及时描述文件的演进
* 符合某些条件时自动触发动作
* 管理公司里人员的角色和验证步骤
* 管理不同物件/模块间的互动
* 为文件流程可视化提供图形化的工具

##内容

* 查看OE业务友好的工作流视图;
* 使用OE图形化的工作流编辑器检查销售订单工作流;
* 学习OE中如何用活动(activities)和变迁(transitions)管理文档状态;
* 学习如何对一个现实的例子修改工作流: 订单确认前增加一个批准过程.

##查看销售订单工作流

* 激活开发模式, 销售->销售订单顶部, 销售订单标签右侧有个小问号图标, 点它查看销售订单业务流程.
* 每个矩形是一个节点, 包含工作流过程中的一个步骤; 箭头指出信息在工作流中流动的方向.
* 背景有环形符号的节点表示该节点包含子工作流.

------

*点击左下角的蓝色信息图标可以跳转到OpenERP网站的相关帮助内容*
*点击右下角的绿色箭头图标可以跳转到该节点对应的OpenERP模型*

##工作流在OE中能干什么?

* 修改文档当前的状态或阶段;
* 确定执行一个过程所需的文档状态条件;
* 基于用户角色和访问权限确定处理方式;
* 创建子工作流来封装复杂性.

##工作流设计器

Debug模式下, 销售->销售订单顶部销售订单标签左侧下拉框选择编辑工作流, 将显示工作流列表, 在右侧选择图表视图即可出现编辑工作流可视化编辑器.

------

灰色节点表示它是工作流的开始或结束节点.