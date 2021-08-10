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
        -