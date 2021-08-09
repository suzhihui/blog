---
title: composition-api
date: 2021-08-09 11:02:39
tags: compostion-api
category: 前端
---
## 诞生的理由
vue2
- 复杂代码越来越难维护
- ts 支持有限

## 生态圈
[脚手架](https://cli.vuejs.org/zh/)
[v3官网](https://v3.cn.vuejs.org/)
## 安装
```bash
npm install -g @vue/cli

vue create 项目名称
```

### eslint 不生效
根目录下新建.vscode文件夹 > settings.json

```json
{
    "eslint.validate": ["typescript"]
}
```

### vetur
[官网](https://vuejs.github.io/vetur/)

## 新特性
- `setup` [组件选项](https://v3.cn.vuejs.org/guide/composition-api-introduction.html#setup-%E7%BB%84%E4%BB%B6%E9%80%89%E9%A1%B9)
    - props
    - context

- `ref` 响应式变量 使任何响应式变量在任何地方起作用

![ref](/img/v3/ref.jpg)
```js
// src/components/UserRepositories.vue
import { fetchUserRepositories } from '@/api/repositories'
import { ref } from 'vue'

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: {
      type: String,
      required: true
    }
  },
  setup (props) {
    const repositories = ref([])
    const getUserRepositories = async () => {
      repositories.value = await fetchUserRepositories(props.user)
    }

    return {
      repositories,
      getUserRepositories
    }
  },
  data () {
    return {
      filters: { ... }, // 3
      searchQuery: '' // 2
    }
  },
  computed: {
    filteredRepositories () { ... }, // 3
    repositoriesMatchingSearchQuery () { ... }, // 2
  },
  watch: {
    user: 'getUserRepositories' // 1
  },
  methods: {
    updateFilters () { ... }, // 3
  },
  mounted () {
    this.getUserRepositories() // 1
  }
}
```

- `toRef` 可以用来为源响应式对象上的某个 property 新创建一个 ref。然后，ref 可以被传递，它会保持对其源 property 的响应式连接

```js
const state = reactive({
  foo: 1,
  bar: 2
})

const fooRef = toRef(state, 'foo')

fooRef.value++
console.log(state.foo) // 2

state.foo++
console.log(fooRef.value) // 3
```

- `toRefs` 将响应式对象转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的 ref
- `isRef` 检查值是否为一个 ref 对象

- `computed` 
    - 接受一个 getter 函数，并为从 getter 返回的值返回一个不变的响应式 ref 对象。
    - 它也可以使用具有 get 和 set 函数的对象来创建可写的 ref 对象

```js
const count = ref(1)
const plusOne = computed(() => count.value + 1)

console.log(plusOne.value) // 2

plusOne.value++ // 错误
```
    

```js
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: val => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value) // 0
```

- `reactive`