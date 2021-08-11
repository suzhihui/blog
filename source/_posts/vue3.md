---
title: vue3
date: 2021-06-15 14:42:38
tags: vue
category: 前端
---

## vue3 简介
## 特点
- 性能提升
- 源码行升级
    - Proxy 代替 defineProperty实现响应式
    - 重写虚拟DOM实现和tree-shaking
- 拥抱Ts
- Composition API (组合API)
    - setup
    - ref与reactive
    - watch与watchEffect
    - provide与inject
    - ...
- 新的内置组件
    - Fragment
    - Teleport
    - Suspense
- 其他改变
    - 新的生命周期钩子
    - data选项应始终被声明为一个函数
    - 移除keyCode支持作为v-on的修饰符
    - 。。。

## 安装
- vue-cli

```bash
## @vue/cli 4.5.0以上
vue --version
vue -V
## 安装或者升级你的@vue/cli
npm install -g @vue/cli
## 创建
vue create vue_test
## 启动
cd vue_test
npm run serve
```

- vite

> [官网](https://cn.vitejs.dev/) 新一代前端构建工具
    - 开发环境中, 无需打包，可快速冷启动
    - 快速热重载（HMR)
     - 真正的按需编译

```sh
## 创建
npm init vite-app <propject-name>
## 启动
cd <project-name>
npm install 
npm run dev
```

## composition API 组合式api
[官网](https://v3.cn.vuejs.org/api/composition-api.html)
- setup
    - vue3 新的配制项， 函数 
    - setup 是所有compositionApi 表演舞台
    - 组件中所用到的：数据、方法等，均要在setup中配置
    - setup函数的两种返回值：
        - 对象
        - 渲染函数
    - 注意点：
        - 不要跟vue2.x 混用
        - setup 不能是一个async函数

```js
// 渲染函数
import {h} from 'vue'

export default {
    name: 'App',
    setup() {
        return () => h('h1', '老苏')
    }
}
```

- ref函数
    - 定义一个响应式的数据
    - 语法
        - 创建一个包含响应式数据的`引用对象（reference对象，简称ref对象）`
        - JS中操作数据： xxx.value
        - 模板中读取数据：不需要.value
    - 备注
        - 接收的数据可以是： 基本类型、也可以是对象类型
        - 基本类型的数据：响应式依然是靠Object.defineProperty()的get与set完成
        - 对象类型的数据：reactive函数 （内部用的Proxy做响应处理)
    
- reactive函数
    - 定义一个`对象类型`的响应式数据
    - 语法
        - `const 代理对象= reactive(被代理对象)` 接收一个对象（或数组）,返回一个`代理器对象（proxy对象)`
    - reactive 定义的响应式数据是“深层次的"
    - 内部基于ES6的Proxy实现，通过代理对象操作源对象内部数据都是响应式的