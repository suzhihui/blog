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


```js
setup() {
        let person = reactive({
            name: '老苏',
            age: 36,
            job: {
                type: '前端工程师',
                salary: '30K',
                number: 666,
                a: {
                    b: {
                        c: {
                            d: 100
                        }
                    }
                }
            },
            hobby: ['抽烟', '喝酒', '烫头']
        })
        // let number = reactive(666)
        function changeInfo() {
            person.name = '小苏'
            person.age = 18
            person.type = 'UI设计师'
            person.salary = '60K'
            person.number = 999
            person.job.a.b.c.d = 200
            person.hobby[0] = '学习' // vue2.x 不能用索引改
        }
        return {
            person,
            changeInfo,
        }
    }
```

## Vue3.x中的响应式原理

## vue2.x的响应式
- 实现原理
    - 对象类型： 通过Object.defineProperty() 对属性的读取、修改进行拦截（数据劫持)
    - 数组类型： 通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹)
    ```js
        Object.defineProperty(data, 'count', {
            get() {},
            set() {}
        })
    ```
- 存在问题：
    - 新增属性、删除属性、界面不会更新
    - 直接通过下标修改数组，界面不会自动更新
## vue3.0响应式
- 实现原理
    - 通过Proxy(代理)： 拦截对象中任意属性的变化，包括：属性的读写、属性的添加、属性的删除等。
    - 通过Reflect(反射): 对被代理对象的属性进行操作
    - MDN文档
        - [Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
        - [Reflect](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

```js
let person = {
    name: '张三’，
    age: 18
}

const p = new Proxy(person, {
    get(target,propName) {
        return Reflect.get(target, propName)
    },
    set(target, propName, value) {
        Reflect.set(target, propName, value)
    },
    deleteProperty(target, propName) {
        return Reflect.deleteProperty(target, propName)
    }
})
```
## reactive 对比 ref
- 从定义数据角度对比:
    - ref用来定义：`基本类型数据`。
    - reactive用来定义：`对象（或数组)`类型数据
    - 备注：ref也可以用来定义`对象（或数组)`类型数据,它内部会自动通过reactive转为`代理对象`
- 从原理角度对比：
    - ref能过`Object.defineProperty()`来实现响应（数据劫持）
    - reactive通过使用`Proxy`来实现响应(数据劫持)， 并通过`Reflect`操作`源对象`内部数据
- 从使用角度对比：
    - ref定义的数据：操作数据需要.value,读取时模板中不需要.value;
    - reactive定义数据：操作数据与读取数据：均不需要.value;

## setup的两个注意点
    - setup执行的时机
        - 在beforeCreate之前执行一次，this是undefined
        - context: 上下文对象
            - attrs: 值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性，相当于this.$attrs,
            - slots: 收到的插槽内容，相当于this.$slots.
            - emit: 分发自定义事件的函数，相当前this.$emit.

## computed函数
    - 与vue2.x中的computed配置功能一致
    - 写法
```js
setup() {
    ...
    // 计算属性-- 简写
    let fullName = computed(() => {
        return person.firstName + '-' + person.lastName
    })
    // 完整版
    let fullName = computed({
        get() {
            return person.firstName + '-' + person.lastName
        }
        set(value) {
            const nameArr = value.splite('-')
            person.firstName = nameArr[0]
            person.lastName = nameArr[1]
        }
    })
}
```

## watch函数
```js
let sum = ref(0)
let msg = ref('你好啊')
let person = reactive({
    name: '张三',
    age: 18,
    job: {
        j1: {
            salary: 20
        }
    }
})

// 情况一：
watch(sum, (nValue, oValue) => {
    console.log('sum变了', nValue,oValue)
}, {immediate: true})
// 情况二：
watch([sum, msg], (nValue, oValue) => {
    console.log('sum或msg变了', nValue,oValue)
}, {immediate: true})
//  情况三：
//  * 注意1：此处无法正确的获取oldValue
//  * 注意2：deep 无当关闭
watch(person, (nValue, oValue) => {
    console.log('person变了', nValue,oValue)
}, {immediate: true， deep: false})

// 情况四：监视reactive定义的一个响应式数据中的某个属性
watch(() => person.name, (nValue, oValue) => {
     console.log('person的name变了', nValue,oValue)
})
// 情况五：监视reactive定义的一个响应式数据中的某些属性
watch([() => person.name, () => person.age], (nValue, oValue) => {
     console.log('person的name或age变了', nValue,oValue)
})
// 特殊情况 些处监视的reactive所定义的对象中的某个属性，所以deep配制有效
watch(()=>person.job, (nValue, oValue) => {
    console.log('person的job变了', nValue,oValue)
},{deeper: true})

```

## watchEffect函数
- watch的套路是：既要指明监视的属性，也要指明监视的回调
- watchEffect的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个个属性，
- watchEffect有点像computed:
    - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值
    - 而watchEffect理注重的是过程（回调函数的函数体），所以不用写返回值
```js
// watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调
watchEffect(() => {
    const x1 = sum.value
    const x2 = person.value
    console.log('watchEffect配置的回调执行了')
})
```