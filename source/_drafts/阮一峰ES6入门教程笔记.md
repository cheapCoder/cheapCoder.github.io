---
title: 阮一峰ES6入门教程笔记
tags:
---
```
ECMAScript是JavaScript的规范，javascript是ECMAScript的实现
ES6泛指5.1版本之后JavaScript的下一代标准
```

## 一 let const

- 不存在变量提升
- 暂时性死区(TDZ)
  
  > 本质：只要进入当前作用域，所使用的变量就已存在，但不可获取，只有等到变量声明出现才可以获取使用该变量 
- 不允许重复声明
- 块级作用域
  > 作用：
  > &emsp;1. 内层变量覆盖外层变量
  > &emsp;2. 防止for的初始化变量泄漏为全局变量
  > &emsp;3. 不在需要用IIFE创造作用域 
  >
  > for循环计数器很适合使用let命令

> 注意:
> 1. 由于es6与es5的块级作用域函数声明规则变化，为了兼容，在块级作用域声明函数应尽量使用函数表达式而不是function函数声明形式(详情见阮一峰es6入门教程p24)
> 2. ES6 的块级作用域允许声明函数的规则只在使用大括号的情况下成立，如果没有使用大括号，就会报错。

``` javascript
//不报错
'use strict';
if (true) {
  function f (){}
}
//报错
'use strict';
if (true)
  function f () {}
```

###### const保存的是指向变量的那块内存地址
