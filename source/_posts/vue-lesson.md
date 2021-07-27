---
title: vue-lesson
date: 2021-07-28 01:51:11
tags: 前端
---

## 讲座大纲
> 1、@vue/compositon-api 理论
> 2、改写慧来客几个vue2 options-api 组件

## 什么是组合式API？
vue3新的编写Vue组件的方式

1: vue2: options-api
```html
<script>
data(){
    return {
        dark: false
    }
},
computed: {
    light() {
        return !this.dark
    }
},
methods: {
    toggleDark() {
        this.dark = !this.dark
    }
}
</script>
```

2: vue3: compositon-api
```html
<script>
    import {ref, computed} from 'vue'
    export default {
        setup() {
            const dark = ref(false)
            const light = computed(() => !dark.value)

            return {
                dark,
                light,
                toggleDark() {
                    dark.value = !dark.value
                }
            }
        }
    }
</script>
```

## 对比
- 对象式api存在的问题-options-api
    - 不利于复用
    - 潜在命名冲突
    - 上下文丢失
    - 有限的类型支持
    - 按API类型组织

- 组合式API的能力-compositon-api
    - 极易复用（原生JS函数)
    - 可灵活组合（生命周期钩子可多次使用)
    - 提供理好的上下文支持
    - 更好的ts类型支持
    - 按功能/逻辑组织
    - 可独立于Vue组件使用

## [编辑器](https://sfc.vuejs.org)

## 新特性
- Ref
- Reactive
- unref