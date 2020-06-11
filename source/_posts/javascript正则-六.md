---
title: javascript正则-六
date: 2020-04-11 10:02:05
tags: 正则表达式
category: 前端
---
# 其它正则捕获的方法
- test也能捕获（本意是匹配）
  - RegExp.$1-RegExp.$9: 获取当前本次正则匹配后，第一个到第九个分组信息
```js
let str = '{0}年{1}月{2}日'
let reg = /\{(\d+)\}/g
// change lastIndex for RegExp
console.log(reg.test(str));// true
console.log(RegExp.$1);// 0
console.log(reg.test(str));// true
console.log(RegExp.$1);// 1
console.log(reg.test(str));// true
console.log(RegExp.$1);    // 2
console.log(reg.test(str));// false
console.log(RegExp.$1);    // 2  存的是上次捕获的结果
```
- replace 字符串中实现替换的方法（一般都是伴随正则一起使用的）

```js
let str = 'laosu2019@laosu@2020'

str = str.replace('laosu', '老苏')
console.log(str); // 老苏2019@laosu@2020
str = str.replace('laosu', '老苏')
console.log(str); // 老苏2019@老苏@2020

str = str.replace(/laosu/g, '老苏')
console.log(str);  // 老苏2019@老苏@2020
```
----
# 案例：把时间字符串进行处理
```js
let time = '2019-08-13'
// => '2019年08月13日
let reg = /^(\d{4})-(\d{1,2})-(\d{1,2})$/
time = time.replace(reg, "$1年$2月$3日")
console.log(time); // 2019年08月13日

time = time.replace(reg, (...arg) => {
  //=> 这里的$1-$3是我们自己设置的变量 
  console.log(big,$1,$2,$3);
  let [,$1,$2,$3] = arg
  return $1+'年'+$2+'月'+$3+'日'
})
```
## 单词首字母大写
```js
let str = 'good good study, day day up!'
let reg = /\b([a-zA-Z])[a-zA-Z]*\b/g
str = str.replace(reg, (...arg) => {
  let [content, $1] = arg
  $1=$1.toUpperCase()
  content = content.substring(1)
  return $1 + content
})
console.log(str);
```

## 简单密码
> 6-16位字符 区分大小写
> 不可以仅为连续数字或相同字符
> 数字、字母、符号至少包含两种
```js

let str = "book"     // => ‘good'、 ’look'、 'moon'、 'foot'...
// let reg = /^[a-zA-Z]([a-zA-Z])\1[a-zA-Z]$/
// let reg = /([a-zA-Z])\1{}/
// let reg = /((\w)\1){6,16}$/ig
// let reg = /^(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]).{6,12}$/
let reg = /^(?=.*(\d|[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]))(?=.*([a-zA-Z]|[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?])).{6,12}$/
// let reg = /^(?![\d]+$)(?![a-zA-Z]+$)(?![^\da-zA-Z]+$).{6,20}$/
// let reg = /^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,}$/
console.log(reg.test('m$$$$$$')) 
```