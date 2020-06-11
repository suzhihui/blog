---
title: vue自定义指令扩展
date: 2020-04-02 20:57:53
tags: vue
category: 前端
cover:  'https://cn.vuejs.org/images/logo.png'
---
### 实现介绍
主要就是利用vue指令的功能，获取所绑定元素的dom对象dom_A以及传递过来的回调方法fun_A，然后监听浏览器的mousedown和mouseup事件(mousedown作为辅助信息，真正触发传递的回调方法的是mouseup事件)，当前事件中鼠标位置对应的dom对象dom_B不属于dom_A，则代表鼠标点击了dom_A外部，触发clickoutside回调方法。

### 扩展介绍
理论上clickoutside只能也只需要绑定一个元素作为inside，但是一些特殊的原因(可能是代码不够好)，要求clickoutside可以选定多个元素作为inside，当鼠标点击了这些元素所构成的inside的外部时，再触发事件。
结合下图，A与B两个元素作为一个inside，当鼠标点击在click1位置时，触发clickoutside，当鼠标点击click2或者click3位置时都不触发clickoutside。
![avatar](https://image-static.segmentfault.com/410/817/4108174745-5ac75d12a0d4d_articlex)

### 扩展实现
要实现上述功能，就必须获取到A和B的dom对象，然后在原先鼠标事件的监听的基础上，判断鼠标位置是否都不包含在A和B中，如果是的话再触发clickoutside。
实现方式为，在A和B的父级parent元素上绑定`v-clickoutside:yourClassName="handleClickOutside"`，在A和B元素上添加同一个class样式，样式名称与指令冒号后面内容一致`class="yourClassName"`。主要在处理指令绑定时，通过binding.arg即可获取到A和B共有的class，存放在dom变量中。在鼠标放开触发事件处理时，通过class获取到他们的dom对象。

### 使用示例
```html
<div v-clickoutside:exactAreaClassName="handleClickOutside">
    Parent
    <div class="exactAreaClassName">A</div>
    <div class="exactAreaClassName">B</div>
</div>
```

### 实现代码
```js
// 引入Vue用以判断当前运行环境
import Vue from 'vue'
// element封装的一些常用dom操作，这里on可以先当做是addEventListener的封装
import { on } from 'element-ui/src/utils/dom'
// 所有绑定了clickoutside指令的元素的dom对象数组
const nodeList = []
// 用来做存放于dom对象中clickoutside相关参数对象的key
const ctx = '@@clickoutsideContext'

let startClick
let seed = 0
// 鼠标按下时，记录此时事件信息
!Vue.prototype.$isServer && on(document, 'mousedown', e => (startClick = e))
// 鼠标松开时候，遍历绑定clickoutside的节点，进行判断是否在节点外部以触发回调
!Vue.prototype.$isServer && on(document, 'mouseup', e => {
  nodeList.forEach(node => node[ctx].documentHandler(e, startClick))
})

// 是否在特殊限定范围内
function ifInExact (exactElms, target1, taget2) {
  for (let i = 0; i < exactElms.length; i++) {
    let elm = exactElms[i]
    if (elm.contains(target1) || elm.contains(taget2) || elm === target1) return true
  }
  return false
}

// 是否有特殊限定范围
function ifHasExact (el, exactArea) {
  if (!exactArea) return false
  return el.getElementsByClassName(exactArea)
}

function createDocumentHandler (el, binding, vnode) {
  return function (mouseup = {}, mousedown = {}) {
    if (!vnode ||
      !vnode.context ||
      !mouseup.target ||
      !mousedown.target ||
      (vnode.context.popperElm &&
        (vnode.context.popperElm.contains(mouseup.target) ||
          vnode.context.popperElm.contains(mousedown.target)))) return
    let exactElms = ifHasExact(el, el[ctx].exactArea)
    // 如果是有特殊限定范围的，则进行判断当前点击是否在 限定范围内
    if (exactElms) {
      if (ifInExact(exactElms, mouseup.target, mousedown.target)) {
        return
      }
    // 无特殊限定范围，则判断点击是否在默认的指令所在范围内
    } else if (el.contains(mouseup.target) || el.contains(mousedown.target) || el === mouseup.target) {
      return
    }
    if (binding.expression &&
      el[ctx].methodName &&
      vnode.context[el[ctx].methodName]) {
      vnode.context[el[ctx].methodName]()
    } else {
      el[ctx].bindingFn && el[ctx].bindingFn()
    }
  }
}

export default {
  bind (el, binding, vnode) {
    nodeList.push(el)
    const id = seed++
    el[ctx] = {
      id,
      documentHandler: createDocumentHandler(el, binding, vnode),
      methodName: binding.expression,
      bindingFn: binding.value,
      // 特殊限定范围的class，限定范围为该class的所有元素的并集
      exactArea: binding.arg
    }
  },

  update (el, binding, vnode) {
    el[ctx].documentHandler = createDocumentHandler(el, binding, vnode)
    el[ctx].methodName = binding.expression
    el[ctx].bindingFn = binding.value
    // 附加 真正起作用部分
    el[ctx].exactArea = binding.arg
  },

  unbind (el) {
    let len = nodeList.length

    for (let i = 0; i < len; i++) {
      if (nodeList[i][ctx].id === el[ctx].id) {
        nodeList.splice(i, 1)
        break
      }
    }
    delete el[ctx]
  }
}

```