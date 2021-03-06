---
layout:     post
title:      【正则表达式系列】零宽断言
category: blog
tags: 正则表达式
favour: 正则表达式
description: 正则表达式的零宽断言在JavaScript中的使用，包括正向前瞻和负向前瞻，以及一些实战演练
---

## 前言

本文属于 正则表达式系列文章之一，更多请前往  [正则表达式系列](http://www.jianshu.com/p/d04be43416fb)

正则表达式中，有一个绕不过去的坎，那就是`零宽断言`

`零宽断言`是一种零宽度的匹配，它匹配的内容不会保存到匹配结果中，也不会占用`index`宽度，最终匹配的结果只是一个位置

简单的说，它用于查找在某些内容之前或之后的东西(但返回结果并不包括这些内容)

`JavaScript`中只支持`零宽先行断言`

## 简介

`零宽断言`分为`4`类

- 正向零宽先行断言（`?=exp`）
  
  - 目标字符出现的位置的右边必须匹配到`exp`这个表达式 

- 负向零宽先行断言（`?!exp`）

  - 目标字符出现的位置的右边不能匹配到`exp`这个表达式 

- 正向零宽后发断言（`?<=exp`）

  - 目标字符出现的位置的左边必须匹配到`exp`这个表达式 

- 负向零宽后发断言（`?<!exp`）

  - 目标字符出现的位置的左边不能匹配到`exp`这个表达式 

注，关于`先行`和`后发`，还有其它称呼，譬如`前瞻`和`后瞻`等，本文统一使用`先行`与`后发`

## `JavaScript`中的断言

`JavaScript`语言内只支持`零宽先行断言`（即只支持`?=exp`和`?!exp`）

所以本文中只会介绍`零宽先行断言`

另外，可以通过`RegexBuddy 4`等工具分析正则的匹配过程

## 大纲

- 正向零宽先行断言

- 负向零宽先行断言

- 实战演练

## 正向零宽先行断言

__示例1__

```js
var str="abcdefg";
var reg=/ab(?=cd)/;
str.match(reg); // ["ab", index: 0, input: "abcdefg"]
```

- `index  = 0`，`a`匹配`a`成功，尝试`b`匹配`b`成功

- 继续尝试匹配，`(?=cd)`接管控制权

- 接下来`(?=cd)`依次尝试匹配`c`和`d`成功

- 此时所有的正则表达式匹配完毕，匹配成功，返回成功结果



__示例2__

```js
var str="abcdefg";
var reg=/(?=cd)efg/;
str.match(reg); // null
```

想要达到的效果是匹配在`cd`后方的`efg`，但是这是`零宽后发断言`才有的效果（`?<=exp`），而`JS`中并不支持，此时使用先行断言，实际效果为

- `?=cd`获取控制权，一直到`index = 2`时才匹配成功，接下来`e`获取控制权

- 可惜由于`?=cd`是零宽式的，因此匹配成功后，下一轮匹配依然从`index = 2`开始尝试，此时`c`匹配`e`失败，于是`index`挪到`3`

- 接下来又是`d, e, f, g`匹配`?=cd`失败，于是最终匹配失败，返回`null`

__示例3__

```js
var str="abcdefg";
var reg=/(?=cd)cdefg/;
str.match(reg); // ["cdefg", index: 2, input: "abcdefg"]
```

基于示例2的变形

- 在`index = 2`时，`?=cd`匹配成功了，交给`cdefg`

- `index = 2`，此时刚好`c`匹配，继续吃进`d`,`e`,`f`,`g`也都匹配，于是匹配成功，于是返回成功结果

__示例4__

```js
var str="abcdefg";
var reg=/ab(?=cd)cdefg/;
str.match(reg); // ["abcdefg", index: 0, input: "abcdefg"]
```

上述示例的综合

- `index = 0`时，左侧的`ab`匹配成功

- 继续往后尝试(`index = 2`处)，`?=cd`也匹配成功

- 接下来继续从`index = 2`开始尝试，`c, d, e ,f , g`依次匹配成功，于是匹配结束，返回成功结果(`index = 0`，因为没有失败，后续的尝试都成功了)


注，零宽断言返回的是位置而不是字符，零宽断言匹配成功后，其余表达式会基于这个返回的位置继续判断

另外，请不要把`先行断言`当成`后发断言`来用

## 负向零宽先行断言

__示例1__

```js
var str="abcdefg";
var reg=/ab(?!cd)/;
str.match(reg); // null
```

- 在`ab`匹配成功后，接下来`cd`匹配`?!cd`失败

- 接下来的`b, c, d, e, f, g`依次都匹配`a`失败，于是最终匹配失败，返回`null`

__示例2__

```js
var str="abcdefg";
var reg=/ab(?!ab)cd/;
str.match(reg); // ["abcd", index: 0, input: "abcdefg"]
```
- 在`ab`匹配成功后，接下来`cd`匹配`?!ab`成功

- 由于`?!ab`是零宽的，因此接下来仍然从`index = 2`处尝试（也就是`c`继续匹配`c`），因此匹配成功，接下来`d`也匹配`d`成功，所有表达式匹配完毕，最终返回成功结果（`index = 0`，因为没有失败）

## 实战演练

接下来一些实战练习，加深印象

### 找到文本中的所有`ing`单词的前缀

__需求说明__

例如: `I am reading in the dining room`的匹配结果应该是`read`与`din`

__代码__

```js
var str="I am reading in the dining room";
var reg=/\w+(?=ing)/g;
str.match(reg); //  ["read", "din"]
```

__说明__

- `\w+`匹配至少一个以上的单词

- `?=ing`代表右侧必须有`ing`，但是匹配的结果又不包含`ing`

- `g`是全局匹配

- 因此符合了需求，成功匹配

### 测试一个文件是否是`.css`后缀，但又不能是`.min.css`

__需求说明__

这道题曾多次出现在各大平台，基本都是依靠零宽断言来检测，例如:

```js
test('a.min.css'); // false
test('b.css'); // true
test('c.mining.css'); // true
```

__代码__

```js
var reg=/^(?!.*\.min\.css$).+\.css$/;

reg.test('a.min.css'); // false
reg.test('.min.css'); // false
reg.test('.css'); // false
reg.test('min.css'); // true
reg.test('b.css'); // true
reg.test('c.mining.css'); // true
```

__说明__

由于只考虑单个文件名的匹配，所以较简单

- 先用`?!.*\.min\.css`负向先行断言试探文件名。这一步匹配完后，直接就排除了`xxx.min.css`了（由于是`*`，所以`.min.css`也会匹配失败）

- 然后继续用`\w+.*\.css`匹配`xxx.css`这种情况

- 于是就符合了要求，只匹配`.css`但不匹配`.min.css`

- 另外，关于回溯步骤，由于较多，这里就不赘述了，感兴趣的可以用`RegexBuddy`等工具自行检测

### 找到所有的`.min.css`文件的文件名

__需求说明__

例如: `a.min.css;.min.css;.css;min.css;b.css;c.min.js;d.css;e.a.min.css`（文件以`;`隔开）的匹配结果应该是`a`与`e.a`

__代码__

```js
var str="a.min.css;.min.css;.css;min.css;b.css;c.min.js;d.css;e.a.min.css";
var reg=/\w+[^;]*(?=\.min\.css)/g;
str.match(reg); //  ["a", "e.a"]
```

__说明__

这类型表达式回溯次数很多，实际中可以有更好的解决方案，比如先分割，再匹配

- 左侧的表达式`\w+[ ^;]*`确保了必须是一个正常的单词开头，并且不能包括`;`，所以直接排除了名字以`.`开头或名字中包含`;`的情况

- 右侧的零宽断言`?=\.min\.css`确保名字右侧必须有`.min.css`

- 于是最终只剩下了`a`与`e.a`符合情况

### 找到所有`.css`文件的文件名，需要排除`.min.css`

__需求说明__

这道题基于上两题的综合与变形，增加了点难度（不再是单个文件名匹配，而是字符串中的文件名提取）

例如: `a.min.css;.min.css;.css;min.css;b.css;c.min.js;d.css;e.a.min.css；f.min.a.css`（文件以`;`分割）的匹配结果应该是`min`、`b`、`d`和`f.min.a`

__代码__

```js
var str = "a.min.css;.min.css;.css;min.css;b.css;c.min.js;d.css;e.a.min.css;f.min.a.css"; 
var reg1 = /[^;]+(?=\.css)/g;

var match1 = str.match(reg1); 

var reg2 = /\.min$/;
var match2 = [];

match1 && match1.map(function(item, index) {
    !reg2.test(item) && match2.push(item);
});

console.log(match1); // ["a.min", ".min", "min", "b", "d", "e.a.min", "f.min.a"]
console.log(match2); // ["min", "b", "d", "f.min.a"]
```

__说明__

好吧，我承认无法只靠一个表达式实现这个功能（不知道在座的各位有谁可以的...）

- 第一步`[ ^;]+(?=\.css)`先匹配所有的`.css`后缀的名字

- 第二步`\.min$`剔除以`.min`结尾的名字

- 于是就只剩下了正常的结果

- 或者先分割，然后再匹配（是`.css`但非`.min.css`）也行

_PS：本来准备一步解决作为压轴的，但是尝试了很久都未果，最终还是拆分来实现的，之所以仍然放在最后，也算是给自己一个警醒_

## 写在最后的话

深入研究后，才发现精通正则表达式真的很难，很多时候，你认为的`已经精通了`只是一种假象。

因此，还是放下身段，努力学习吧！

## 附录

### 参考资料

* [正则表达式元字符](http://www.runoob.com/regexp/regexp-metachar.html)

* [正则表达式零宽断言详解](http://www.jb51.net/article/95906.htm)

* [正则表达式之零宽断言详解](http://blog.csdn.net/hsd2012/article/details/51272902)