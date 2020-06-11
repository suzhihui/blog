---
title: vue自定义指令
date: 2020-04-02 01:03:43
tags: vue
category: 前端
layout: post
cover:  'https://cdn4.buysellads.net/uu/1/3386/1525189943-38523.png'
---
# 官方资料
[Vue自定义指令](https://cn.vuejs.org/v2/guide/custom-directive.html)

# 注册方式
- 全局

```js
  // 注册一个全局自定义指令 `v-focus`
  Vue.directive('focus', {
    // 当被绑定的元素插入到 DOM 中时……
    inserted: function (el) {
      // 聚焦元素
      el.focus()
    }
  })
```

- 局部

```js
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```
----
# 钩子函数

一个指令定义对象可以提供如下几个钩子函数 (均为可选)：
- `bind`: 只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
- `inserted`: 被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- `update`: 所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。
- `componentUpdated`：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
- `unbind`：只调用一次，指令与元素解绑时调用。
  
----
# 钩子函数参数
- `el`: 指令所绑定的元素，可以用来直接操作 DOM。真实DOM元素
- `binding`：一个对象，包含以下属性：
  - `name`：指令名，不包括 v- 前缀。
  - `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 2。
  - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。
  - `arg`：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
  - `modifiers`：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。
- `vnode`：Vue 编译生成的虚拟节点。移步 VNode API 来了解更多详情。
- `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。
  
```html
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
```
```js
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
```

> 除了 el 之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过元素的 `dataset` 来进行。

----
# 动态指令参数
指令的参数可以是动态的。例如，在 ```v-mydirective:[argument]="value"``` 中，```argument``` 参数可以根据组件实例数据进行更新！这使得自定义指令可以在应用中被灵活使用。

例如你想要创建一个自定义指令，用来通过固定布局将元素固定在页面上。我们可以像这样创建一个通过指令值来更新竖直位置像素值的自定义指令：

# 函数简写
```js
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

# 对象字面量
如果指令需要多个值，可以传入一个 JavaScript 对象字面量。记住，指令函数能够接受所有合法的 JavaScript 表达式。
```html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```
```js
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```

# ts编写vue clickOutside自定义指令
```ts
import { DirectiveOptions } from 'vue';

const clickOutside:DirectiveOptions = {
  bind (el, binding, vnode) {
      function documentHandler (e) {
          if (el.contains(e.target)) {
              return false;
          }
          if (binding.expression) {
              binding.value(e);
          }
      }
      (el as any).__vueClickOutside__ = documentHandler;
      document.addEventListener('click', documentHandler);
  },
  unbind (el, binding) {
    document.removeEventListener('click', (el as any).__vueClickOutside__);
    delete (el as any).__vueClickOutside__;
  }
}

export default clickOutside
```

```html
<a-select style="width: 80px" v-click-outside="no">
</a-select>
```
```ts
import {Vue, Component, Prop, Emit} from 'vue-property-decorator'
import clickOutside from '@/lib/script/clickOutside'
@Component({
  directives: {
    clickOutside
  }
})
export default class BirthdaySelect extends Vue {
  no(e) {
    // code here
  }
}
```