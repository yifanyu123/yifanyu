---
layout: post
title: 'markdown语法'
date: 2019-06-28
author: yifanyu
color: '#fee9b2'
cover: '../assets/3.jpg'
tags:　markdown coding
---
#markdown语法
##列表

##表格
姓名|技能|排行
--|:--:|--:
刘备|哭|大哥
关羽|打|二哥
张飞|骂|三弟

**这是加粗的文字**
*这是倾斜的文字*`
***这是斜体加粗的文字***
~~这是加删除线的文字~~


>这是引用的内容
>>这是引用的内容
>>>>>>>>>>这是引用的内容

---
----
***
*****

![图片alt](图片地址 ''图片title'')

图片alt就是显示在图片下面的文字，相当于对图片内容的解释。
图片title是图片的标题，当鼠标移到图片上时显示的内容。title可加可不加

```
  代码...
  代码...
  代码...
```


```flow
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
&```

*这里是斜体*
_这里是斜体_

**这里是加粗**
__这里是加粗__
