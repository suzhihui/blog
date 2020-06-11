---
title: javascript正则（二）
date: 2020-04-03 22:31:21
tags: 正则表达式
category: 前端
cover: https://i.loli.net/2019/07/21/5d33d5dc1531213134.png
---
# 元字符详细

```js
let reg = /^\d/;
console.log(reg.test('laosu'));       // false
console.log(reg.test('2020laosu'));   // true
console.log(reg.test('laosu2020'));   // false

let reg = /\d$/;
console.log(reg.test('laosu'));       // false
console.log(reg.test('2020laosu'));   // false
console.log(reg.test('laosu2020'));   // true

// => ^/$两个都不加：字符串中包含符合规则的内容即可
let reg = /\d+/
// => 字符串只能是和规则一致的内容
let reg = /^\d$/

// eg: 验证手机号(11位， 第一个数字1即可)
let reg = /^1\d{10}$/
```

## 转义字符 \
```js
// . 不是小数点；是除\n外的任意字符
let reg = /^2.3$/
console.log(reg.test('2.3'))  // true
console.log(reg.test('2@3'))  // true
console.log(reg.test('23'))    // false

// \. 只能是小数点
let reg = /^2\.3$/
console.log(reg.test('2.3'))  // true
console.log(reg.test('2@3'))  // false
console.log(reg.test('23'))    // false

let str = "\d";
reg = /^\d$/;    // -> \d 代表0~9的数字
console.log(reg.test(str)) // false
reg = /^\\d$/
console.log(reg.test(str)) // true
```
- x|y

```js
let reg = /^18|29$/
console.log(reg.test('18')) // true
console.log(reg.test('29')) // true
console.log(reg.test('129')) // true
console.log(reg.test('189')) // true
console.log(reg.test('1829')) // true
console.log(reg.test('829')) // true
console.log(reg.test('182'))   // true
```
> 直接x|y会存在很乱的优先级问题, 一般我们写的时候都伴随着小括号进行分组,因为小括号改变处理的优先级
```js
let reg = /^(18|29)$/
console.log(reg.test('18')) // true
console.log(reg.test('29')) // true
console.log(reg.test('129')) // false
console.log(reg.test('189')) // false
console.log(reg.test('1829')) // false
console.log(reg.test('829')) // false
console.log(reg.test('182'))   // false
```
- []
  - 中括号中出现的字符一般都代表本身的含义

```js
let reg = /^[@+]$/
console.log(reg.test('@')) // true
console.log(reg.test('+')) // true
console.log(reg.test('@@')) // false
console.log(reg.test('@+')) // false

let reg = /^[\d]$/
console.log(reg.test("d")) // false
console.log(reg.test("\\")) // false
console.log(reg.test("9"))  // true
```
  - 中括号中不存在多位数
  
``` js
let reg = /^[18]$/
console.log(reg.test('1')); // true
console.log(reg.test('8')); // true
console.log(reg.test('18')); // false

let reg = /^[10-29]$/
console.log(reg.test('1')); // true
console.log(reg.test('0')); // true
console.log(reg.test('2')); // true
console.log(reg.test('9')); // true
console.log(reg.test('10')); // false
console.log(reg.test('-')); // false
```