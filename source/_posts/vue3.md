---
title: vue3
date: 2021-06-15 14:42:38
tags: vue
category: 前端
---

## vue3 简介
[v3官网](https://v3.cn.vuejs.org/)
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

## toRef
- 作用：创建一个ref对象，其value值指向另一个对象中的某个属性。
- 语法：`const name = toRefs(person, 'name')`
- 应用：要将响应式对象中的某个属性单独提供给外部使用时
- 扩展： `toRefs`与`toRef`功能一致，但可以批量创建多个`ref`对象，语法：`roRefs(person)`

##  其它Composition API
- shallowReactive 与 shallowRef
    - shallowReactive: 只处理对象最外层属性的响应式 （浅响应式）
    - shallowRef: 只处理基本数据类型的响应式，不进行对象的响应式处理。
    - 什么时候用：
        - 如果有一个对象数据，结构比较深，但变化时只是外层属性变化 ===> shallowReactive.
        - 如果有一个对象数据， 后续功能不会修改该对象中的属性， 而是生成新的对象来替换 ===> shallowRef.
- readonly 与 shallowReadonly
    - readonly: 让一个响应式数据变为只读的（深只读)
    - shallowReadonly: 让一个响应式数据变为只读的（浅只读）
    - 应用场景：不希望数据被修改时
- toRow 与 markRow
    - toRow:
        - 作用： 将一个由reactive生成的`响应式对象`转为` `
        - 使用场景： 用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。
    - markRaw:
        - 作用： 标记一人对象，使其永远不会再成为响应式对象
        - 应用场景：
            1.有些值不应被设置为响应式的，例如复杂的第三方类库等
            2.当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。

- [customRef](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/customRef)
    - 创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。

```html
<input v-model="text" />
```
```js
function useDebouncedRef(value, delay = 200) {
  let timeout
  return customRef((track, trigger) => {
    return {
      get() {
        track()
        return value
      },
      set(newValue) {
        clearTimeout(timeout)
        timeout = setTimeout(() => {
          value = newValue
          trigger()
        }, delay)
      }
    }
  })
}

export default {
  setup() {
    return {
      text: useDebouncedRef('hello')
    }
  }
}
```
类型声明：
```ts
function customRef<T>(factory: CustomRefFactory<T>): Ref<T>

type CustomRefFactory<T> = (
  track: () => void,
  trigger: () => void
) => {
  get: () => T
  set: (value: T) => void
}
```
- provide与inject
    - 作用： 实现祖孙组件间通信
    - 套路： 父组件有一个provide选项来提供数据，后代组件有一个inject 选项来开始使用这些数据
    - 具体写法：
        ```js
        setup() {
            ...
            let car = reactive({name: '奔驰', price: '40W'})
            provide('car', car)
            ...
        }
        ```
    - 后代组件中：
    ```js
    setup(props, context) {
        ...
        const car = inject('car')
        return {car}
        ...
    }
    ```
- 响应式数据的判断
    - isRef: 检查一个值是否为一个ref对象
    - isReactive: 检查一个对象是否是由reactive创建的响应式代理
    - isReadonly: 检查一个对象是否是由readonly创建的只读代理
    - isProxy: 检查一个对象是否是由reactive或者readonlay方法创建的代理

## vue3.0新的组件
- Fragment
    - 在vue2.x中：组件必须有一个根标签
    - 在vue3.0中：组件可以没有要标签，内部会将多个标签包含在一个Fragment虚拟元素中
    - 好处: 减少标签层级，减小内存占用
- Teleport
    - 什么是Teleport? ---- Teleport 是一种能够将我们的`组件html结构`移动指定位置的技术
    ```html
    <teleport to="移动位置">
        <div v-if="isShow" class="mask">
            <div class="dialog">
                <h3>我是一个弹窗</h3>
                <button @click="isShow = false">关闭弹窗</button>
            </div>
        </div>
    </teleport>
    ```