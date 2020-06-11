---
title: javascript正则-五
date: 2020-04-09 00:22:28
tags: 正则表达式
category: 前端
---
> 74  

# 正则的分组捕获
- 身份证号码
```js
let str = "130828199012040112"
let reg = /^(\d{6})(\d{4})(\d{2})(\d{2})\d{2}(\d)(?:\d|x)$/
console.log(reg.exec(str))
console.log(str.match(reg))
/**
 * [
  '130828199012040112',
  '130828',
  '1990',
  '12',
  '04',
  '1',
  '2',
  index: 0,
  input: '130828199012040112',
  groups: undefined
]
 */
// => 第一项：大正则匹配的结果
// => 其余项：第一个小分组单独匹配捕获的结果
// => 如果设置了分组（改变优先级），但是捕获的时不需要单独捕获，可以基于?:来处理
```

----
```js
// => 既要捕获到{数字}，也想单独的把数字也获取到，eg: 第一次找到{0} 还需要单独获取0

// => 不设置g只匹配一次，exec和match获取的结果一致（即有大正则匹配的信息，也有小分组匹配的信息)
let str = "{0}年{1}月{2}日"
let reg = /\{(\d+)\}/;
console.log(reg.exec(str))
console.log(str.match(reg)) // [ '{0}', '0', index: 0, input: '{0}年{1}月{2}日', groups: undefined ]

// => 多次匹配的情况下，match只能把大正则匹配的内容获取到，小分组匹配的信息无法获取
let str = "{0}年{1}月{2}日"
let reg = /\{(\d+)\}/g;
console.log(reg.exec(str)) // [ '{0}', '0', index: 0, input: '{0}年{1}月{2}日', groups: undefined ]
console.log(str.match(reg)) // [ '{0}', '{1}', '{2}' ]

// => 自己写
let argBig=[],
    argSmall = [],
    res=reg.exec(str)
while(res) {
  let [big,small] = res
  argBig.push(big)
  argSmall.push(small)
  res=reg.exec(str)
}
console.log(argBig, argSmall)

```

# 分组的每三个作用： "分组引用"
```js
let str = "book"     // => ‘good'、 ’look'、 'moon'、 'foot'...
let reg = /^[a-zA-Z]([a-zA-Z])\1[a-zA-Z]$/
// => 分组引用就是通过'\数字'让其代表和对应分组出现一模一样的内容
console.log(reg.test('soon')) // true
console.log(reg.test('book')) // true
console.log(reg.test('some')) // false
```
----
> 75
# 正则捕获的贪婪性

- 正则捕获的贪婪性：默认情况下，正则捕获的时候，是按照当前正则所匹配的最长结果来获取的
- 在量词元字符后面设置? => 取消捕获时候的贪婪性（按照正则匹配的最短结果来获取）
```js
let str = '老苏2019@2020回家'
let reg = /\d+/g
console.log(str.match(reg)) // => [ '2019', '2020' ]

let str = '老苏2019@2020回家'
let reg = /\d+?/g
console.log(str.match(reg))
/**
 * [
  '2', '0', '1',
  '9', '2', '0',
  '2', '0'
]
 */

```
- 问号在正则的五大含义
  - 问号左边是非量词元字符：本身代表量词元字符，出现零到一次
  - 问号左边是量词元字符：取消捕获时的贪婪性
  - (?:) 只匹配不捕获
  - (?=) 正向预查
  - (?!) 负向预查

- 分组在正则的三大含义
  - 改变优先级
  - 分组捕获
  - 分组引用 ()\1
