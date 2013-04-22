---
layout: post
title: "jquery的最佳实践"
description: "看了阮一峰老师的文章,做下笔记,jquery最佳实践"
category: jquery
tags: [jquery, js效率]
---
{% include JB/setup %}

看了阮一峰老师的文章,做下笔记…过几天结合STK写点demo

## jquery的最佳实践

#### 使用最新的jquery  
- 速度快呀…不然为什么更新

#### 用对选择器
- 最快的: id和元素 因为用的是原生的getElementById..so...快一点
- 较慢的: class...IE中没有原生的方法..
- 最慢的: 伪类选择器和属性选择器.$(':hidden')$('[attribute=value]') 但是，一些浏览器的新版本，增加了querySelector()和querySelectorAll()方法，因此会使这类选择器的性能有大幅提高。  其实我在项目中用的最多的就是属性选择器了..但是貌似也没有别的方法..可以获得..
 
#### 理解子元素和父元素的关系

- $('.child', $parent) 转换成$.parent.find('child') 这个过程造成一定的损失
- $parent.find('.child') 用原生的方法(能用的话..)速度较快
- $parent.children('.child') 用遍历的方式,你懂得…速度堪忧
- $('#parent > .child') 用sizzle顺序是从右到左,先找child,再匹配合适的parent
- $('#parent .child') 这条语句与上一条是同样的情况。但是，上一条只选择直接的子元素，这一条可以于选择多级子元素，所以它的速度更慢，大概比最快的形式慢了77%。
- $('.child', $('#parent'))  转换成$('#parent').find('.child')

#### 不要过度使用jQuery
尽量用原生的吧

- 一个是选择器.
- 一个是巧用this来获得属性.

#### 做好缓存

- 选中某一个网页元素，是开销很大的步骤。所以，使用选择器的次数应该越少越好，并且尽可能缓存选中的结果，便于以后反复使用。

#### 少改动DOM结构
就和css的修改一样…先改好了,一起插进去..

- 对改动进行合并,一次插入
- 对一个DOM频繁改动的情况下,先取出来,改好后再插进去.
- 插入html代码的时候，浏览器原生的innterHTML()方法比jQuery对象的html()更快。

#### 尽量少生成jQuery对象
- 当你使用一次选择器（比如$('#id')），就会生成一个jQuery对象。jQuery对象是一个很庞大的对象，带有很多属性和方法，会占用不少资源。所以，尽量少生成jQuery对象。

#### 选择作用域链最短的方法

Javascript的变量采用链式作用域。读取变量的时候，先在当前作用域寻找该变量，如果找不到，就前往上一层的作用域寻找该变量。这样的设计，使得读取局部变量比读取全局变量快得多。