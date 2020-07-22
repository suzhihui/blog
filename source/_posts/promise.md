---
title: promise
date: 2020-06-11 00:36:20
tags: 前端架构
category: 前端
---
# Promise 
> 解决并发问题 （同步多个异步方法的执行结果）
> Promise是一个类
- 每次new一个promise 都需要传递一个执行器， 执行器立即执行
- 执行器函数中有两个参数 resolve, reject
- 链式调用的问题 （先获取name, 通过name再获取age）解决多个回调嵌套的问题
- 默认promise 有三个状态 pendding -> resolve 表示成功了fulfilled reject表示拒绝 rejected
- 如果一旦成功不 不能变失败； 一旦失败 不能再变成功
- 每个promise 都有一个then方法
> 链式调用
- 如果返回一个普通值 会走下一个then的成功
- 抛出错误 then的失败方法
- 如果返回 promise 就让promise执行采用他的状态
- 返回一个新promise来实现链式调用
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

# 手写promise
```js
const PENDING   = 'PENDING'
const FULFILLED = 'FULFILLED'
const REJECTED  = 'REJECTED'
class Promise {
	constructor(executor) {
		this.value;
		this.reason;
        this.status = 'PENDING' // 记录3个状态， 默认pending

        this.onResolvedCallBacks = []
        this.onRejectedCallBacks = []
		let resolve = value => {
            // 只有是panding状态才能改变状态
            if(this.status === PENDING) {
                this.value = value
                this.status = 'FULFILLED'
                this.onResolvedCallBacks.forEach(fn => fn()) // 发布
            }
		}
		let reject = reason => {
            if(this.status === PENDING) {
                this.reason = reason
                this.status = 'REJECTED'
                this.onRejectedCallBacks.forEach(fn => fn()) // 发布
            }
        }
        // 这里可能会发生异常
        try {
            // 1。执行器立即执行
            executor(resolve, reject)
        } catch (e) {
            reject(e)    
        }
    }
    
    // 实例方法 判断当前的状态
    then(onFulfilled, onRejected) {
        if(this.status === FULFILLED) {
            onFulfilled(this.value)
        }
        if(this.status === REJECTED) {
            onRejected(this.reason)
        }
        // 如果还是pending状态就订阅
        if(this.status === PENDING) {
            this.onResolvedCallBacks.push(() => {
                onFulfilled(this.value)
            })
            this.onRejectedCallBacks.push(() => {
                onRejected(this.reason)
            })
        }
    }
}

module.exports = Promise
```