---
layout: post
category : Development
tagline:
tags : [JavaScript]
---
{% include JB/setup %}

###1. 内容: HTML; 外观: CSS; 行为: JavaScript.

###2. 变量的使用:

* 声明变量;
* 初始化变量.

###3. 比较

* 1 == '1' : true
* 1 === '1' : false    三个等号的不进行任何转换.
* != 和 !=== 类似.

###4. NaN不等于任何东西, 包括它自己.

###5. undefined

试图访问不存在的变量时, 就会得到一个undefined, JavaScript将声明时没有初始化的变量设为undefined.

###6. 数组

广义表.

var a= [1,2,3]

a[2] : 3

a[3] = 'three' : [1, 2, 3, "three"]

a[6] = 'new' : [1, 2, 3, "three",undefined, undefined, "new"]

delete a[2] : [1, 2, undefined, "three",undefined, undefined, "new"]

