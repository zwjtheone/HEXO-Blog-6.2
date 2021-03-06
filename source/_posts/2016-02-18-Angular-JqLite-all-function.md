title: Angular JqLite 方法汇总
date: 2016-2-18 20:08:08
updated: 2016-11-30 20:08:08
tags:
- Angular
---


##### addClass()-为每个匹配的元素添加指定的样式类名

##### after()-在匹配元素集合中的每个元素后面插入参数所指定的内容，作为其兄弟节点

##### append()-在每个匹配元素里面的末尾处插入参数内容

##### attr() - 获取匹配的元素集合中的第一个元素的属性的值

##### bind() - 为一个元素绑定一个事件处理程序
##### children() - 获得匹配元素集合中每个元素的子元素，选择器选择性筛选
##### clone()-创建一个匹配的元素集合的深度拷贝副本
##### contents()-获得匹配元素集合中每个元素的子元素，包括文字和注释节点
##### css() - 获取匹配元素集合中的第一个元素的样式属性的值
##### data()-在匹配元素上存储任意相关数据

<!--more-->

##### detach()-从DOM中去掉所有匹配的元素
##### empty()-从DOM中移除集合中匹配元素的所有子节点
##### eq()-减少匹配元素的集合为指定的索引的哪一个元素
##### find() - 通过一个选择器，jQuery对象，或元素过滤，得到当前匹配的元素集合中每个元素的后代
##### hasClass()-确定任何一个匹配元素是否有被分配给定的（样式）类
##### html()-获取集合中第一个匹配元素的HTML内容
##### next() - 取得匹配的元素集合中每一个元素紧邻的后面同辈元素的元素集合。如果提供一个选择器，那么只有紧跟着的兄弟元素满足选择器时，才会返回此元素
##### on() - 在选定的元素上绑定一个或多个事件处理函数
##### off() - 移除一个事件处理函数
##### one() - 为元素的事件添加处理函数。处理函数在每个元素上每种事件类型最多执行一次
##### parent() - 取得匹配元素集合中，每个元素的父元素，可以提供一个可选的选择器
##### prepend()-将参数内容插入到每个匹配元素的前面（元素内部）
##### prop()-获取匹配的元素集中第一个元素的属性（property）值
##### ready()-当DOM准备就绪时，指定一个函数来执行
##### remove()-将匹配元素集合从DOM中删除。（同时移除元素上的事件及 jQuery 数据。）
##### removeAttr()-为匹配的元素集合中的每个元素中移除一个属性（attribute）
##### removeClass()-移除集合中每个匹配元素上一个，多个或全部样式
##### removeData()-在元素上移除绑定的数据
##### replaceWith()-用提供的内容替换集合中所有匹配的元素并且返回被删除元素的集合
##### text()-得到匹配元素集合中每个元素的合并文本，包括他们的后代
##### toggleClass()-在匹配的元素集合中的每个元素上添加或删除一个或多个样式类,取决于这个样式类是否存在或值切换属性。即：如果存在（不存在）就删除（添加）一个类
##### triggerHandler() -为一个事件执行附加到元素的所有处理程序
##### unbind() - 从元素上删除一个以前附加事件处理程序
##### val()-获取匹配的元素集合中第一个元素的当前值
##### wrap()-在每个匹配的元素外层包上一个html元素
