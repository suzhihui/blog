---
title: promise
date: 2020-06-11 00:36:20
tags: 前端架构
category: 前端
---
# 并发
```js
const fs = require('fs')
let school = {}
const after = (times, fn) => --times === 0 && fn()
let newAfter = after(2, () => {
  console.log(school)
})
fs.readFile('name.txt', 'utf8', (err, data) => {
  school['name'] = data
  newAfter()
})
fs.readFile('age.txt', 'utf8', (err, data) => {
  school['age'] = data
  newAfter()
})

```
# 发布订阅模式
```js
const fs = require('fs')
let school = {}
let e = {
  arr:[],
  on(fn) {
    this.arr.push(fn)
  },
  emit() {
    this.arr.forEach(fn => fn())
  }
}
e.on(() => { // 订阅
  console.log('ok')
})
e.on(() => { // 订阅
  if(Object.keys(school).length === 2) {
    console.log(school)
  }
})

fs.readFile('name.txt', 'utf8', (err, data) => {
  school['name'] = data
  e.emit() // 发布
})
fs.readFile('age.txt', 'utf8', (err, data) => {
  school['age'] = data
  e.emit() // 发布
})
```

### ing time is 16:15