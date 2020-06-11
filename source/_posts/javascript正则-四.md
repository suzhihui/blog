---
title: javascript正则 (四)
date: 2020-04-07 21:39:13
tags: 正则表达式
category: 前端
---

# 正则两种创建方式的区别
- 构造函数因为传递的是字符串，\ 需要写两个才代表斜杠

```js
let reg = /\d+/g
reg = new RegExp("\\d+", "g")
```

- 正则表达式中的部分内容是变量存储的值
  - 两个斜杠中间包起来的都是元字符(如果正则中要包含某个变量的值，则不能使用字面量方式创建)

```js
let type = "laosu"
reg = /^@"+type+"@$/
console.log(reg.test("@laosu@"));   // => false
console.log(reg.test('@"""typeeeeee"@')); //=> true
```
  - 这种情况只能使用构造函数方式（因为它传递的规则是字符串，只有这样才能进行字符串拼接)
```js
reg = new RegExp("^@"+type+"@$")
console.log(reg.test("@laosu@"))  // => true
```

# 正则捕获
> 实现正则捕获的办法
- 正则RegExp.prototype上的方法
  - exec
    - 捕获的结果是null或者一个数组
    - 第一项：本次捕获到的内空
    - 其余项：对应小分组本次单独捕获的内容
    - index: 当前捕获内容在字符串中的起始索引
    - input: 原始字符串
    - 第执行一次exec只能捕获到一个符合正则的，但是默认只捕获第一个（懒惰性)
      - lastIndex: 当前正则下一次匹配的起始索引位置 -> reg.lastIndex
      - 第一次执行完lastIndex没有变
      - 
  - test
- 字符串String.prototye上支持正则表达式处理的方法
  - replace
  - match
  - splite
  - .....

```js
let str = "laosu2019laosu2020laosu2021"
let reg = /\d+/
console.log(reg.exec(str)) // ["2019", index: 5, input: "laosu2019laosu2020laosu2021", groups: undefined]
reg = /\d+/g
console.log(reg.exec(str)) // => ["2019", ...]
console.log(reg.lastIndex) // 设置全局匹配修饰符g后，第一次匹配完 lastIndex 会自已修改
```

> 需求： 编写一个execAll， 执行一次可以把所有匹配的结果捕获到（前提正则一定要设置全局修饰符g）

```js
~function(str) {
  function execAll(str='') {
    // str=> str 要匹配的字符串
    // => this: RegExp的实例
    // 验证当前正则是否设置了g
    if(!this.global) return this.exec(str)
    let ary = [],
        res = this.exec(str)
    // 只知道条件用while循环
    while(res) {
      ary.push(res[0])
      // => 只要捕获的内容不为null
      res = this.exec(str)
    }
    return ary.length === 0 ? null : ary
  }
  RegExp.prototype.execAll = execAll
}();

let reg = /\d+/g;
console.log(reg.execAll(str))
```
> 上面的 execAll 等价于 String.prototype.match()