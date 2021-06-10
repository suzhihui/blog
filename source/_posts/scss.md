---
title: scss
date: 2021-06-10 14:46:40
tags:
cover: https://icweiliimg6.pstatp.com/weili/smh/902952372222034012.webp
---

## 概念
> Sass 是一种 CSS 的预编译语言。它提供了 变量（variables）、嵌套（nested rules）、 混合（mixins）、 函数（functions）等功能，并且完全兼容 CSS 语法。Sass 能够帮助复杂的样式表更有条理， 并且易于在项目内部或跨项目共享设计。

### [官网](https://sass.bootcss.com/documentation)
### [资料](https://www.runoob.com/sass/sass-tutorial.html)

## 变量
- 字符串
- 数字
- 颜色值
- 布尔值
- 列表
- null值

定义变量使用$符号
```scss
$myFont: Helvetica, sans-serif;
$myColor: red;
$myFontSize: 18px;
$myWidth: 680px;

body {
  font-family: $myFont;
  font-size: $myFontSize;
  color: $myColor;
}

#container {
  width: $myWidth;
}
```
### 作用域
> Sass 变量的作用域只能在当前的层级上有效果，如下所示 h1 的样式为它内部定义的 green，p 标签则是为 red。

```scss
$myColor: red;

h1 {
  $myColor: green;   // 只在 h1 里头有用，局部作用域
  color: $myColor;
}

p {
  color: $myColor;
}
```
!global
当然 Sass 中我们可以使用 `!global` 关键词来设置变量是全局的：
```scss
$myColor: red;

h1 {
  $myColor: green !global;  // 全局作用域
  color: $myColor;
}

p {
  color: $myColor;
}
```
转换后如下css显示：
``` scss
h1 {
  color: green;
}

p {
  color: green;
}
```

### Sass 嵌套规则与属性

Sass 嵌套属性
```scss
font: {
  family: Helvetica, sans-serif;
  size: 18px;
  weight: bold;
}

text: {
  align: center;
  transform: lowercase;
  overflow: hidden;
}
```
```css
font-family: Helvetica, sans-serif;
font-size: 18px;
font-weight: bold;

text-align: center;
text-transform: lowercase;
text-overflow: hidden;
```

### Sass @import 与 Partials

#### Sass Partials
如果你不希望将一个 Sass 的代码文件编译到一个 CSS 文件，你可以在文件名的开头添加一个下划线。这将告诉 Sass 不要将其编译到 CSS 文件
> 以下实例创建一个 _colors.scss 的文件，但是不会编译成 _colors.css 文件：

_colors.scss:
```scss
$myPink: #EE82EE;
$myBlue: #4169E1;
$myGreen: #8FBC8F;
```
如果要导入该文件，则不需要使用下划线：
```scss
@import "colors";

body {
  font-family: Helvetica, sans-serif;
  font-size: 18px;
  color: $myBlue;
}
```

`注意`：请不要将带下划线与不带下划线的同名文件放置在同一个目录下，比如，_colors.scss 和 colors.scss 不能同时存在于同一个目录下，否则带下划线的文件将会被忽略。

#### Sass @mixin 与 @include
@mixin 指令允许我们定义一个可以在整个样式表中重复使用的样式。
@include 指令可以将混入（mixin）引入到文档中。

#### 定义一个混入
混入(mixin)通过 @mixin 指令来定义。 @mixin name { property: value; property: value; … }
以下实例创建一个名为 “important-text” 的混入：

```scss
@mixin important-text {
  color: red;
  font-size: 25px;
  font-weight: bold;
  border: 1px solid blue;
}
```
`注意`：Sass 的连接符号 - 与下划线符号 _ 是相同的，也就是 @mixin important-text { } 与 @mixin important_text { } 是一样的混入。

#### 使用混入
@include 指令可用于包含一混入：
```scss
selector {
  @include mixin-name;
}

.danger {
  @include important-text;
  background-color: green;
}
```
混入中也可以包含混入，如下所示：
```scss
@mixin special-text {
  @include important-text;
  @include link;
  @include special-border;
}
```
#### 向混入传递变量
混入可以接收参数。
我们可以向混入传递变量。
定义可以接收参数的混入：
```scss
/* 混入接收两个参数 */
@mixin bordered($color, $width) {
  border: $width solid $color;
}

.myArticle {
  @include bordered(blue, 1px);  // 调用混入，并传递两个参数
}

.myNotes {
  @include bordered(red, 2px); // 调用混入，并传递两个参数
}
```
混入的参数也可以定义默认值，语法格式如下：
```scss
@mixin bordered($color: blue, $width: 1px) {
  border: $width solid $color;
}
```
在包含混入时，你只需要传递需要的变量名及其值：
```
@mixin sexy-border($color, $width: 1in) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}
p { @include sexy-border(blue); }
h1 { @include sexy-border(blue, 2in); }

```
#### 可变参数
有时，不能确定一个混入（mixin）或者一个函数（function）使用多少个参数，这时我们就可以使用 … 来设置可变参数。

例如，用于创建盒子阴影（box-shadow）的一个混入（mixin）可以采取任何数量的 box-shadow 作为参数。
```scss
@mixin box-shadow($shadows...) {
      -moz-box-shadow: $shadows;
      -webkit-box-shadow: $shadows;
      box-shadow: $shadows;
}

.shadows {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}
```
## Sass @extend 与 继承
@extend 指令告诉 Sass 一个选择器的样式从另一选择器继承。

如果一个样式与另外一个样式几乎相同，只有少量的区别，则使用 @extend 就显得很有用。

以下 Sass 实例中，我们创建了一个基本的按钮样式 .button-basic，接着我们定义了两个按钮样式 .button-report 与 .button-submit，它们都继承了 .button-basic ，它们主要区别在于背景颜色与字体颜色，其他的样式都是一样的。
```scss
.button-basic  {
  border: none;
  padding: 15px 30px;
  text-align: center;
  font-size: 16px;
  cursor: pointer;
}

.button-report  {
  @extend .button-basic;
  background-color: red;
}

.button-submit  {
  @extend .button-basic;
  background-color: green;
  color: white;
}
```
```css
.button-basic, .button-report, .button-submit {
  border: none;
  padding: 15px 30px;
  text-align: center;
  font-size: 16px;
  cursor: pointer;
}

.button-report  {
  background-color: red;
}

.button-submit  {
  background-color: green;
  color: white;
}
```
使用 @extend 后，我们在 HTML 按钮标签中就不需要指定多个类 class=”button-basic button-report” ，只需要设置 class=”button-report” 类就好了。

@extend 很好的体现了代码的复用。

## Sass 函数
Sass 定义了各种类型的函数，这些函数我们可以通过 CSS 语句直接调用。
### Sass 字符串相关函数
Sass String(字符串) 函数用于处理字符串并获取相关信息。

Sass 字符串的起始索引值从 1 开始，记住不是 0。

下表列出了 Sass 的字符串函数：
- quote(string) 给字符串添加引号。
- unquote(string) 移除字符串的引号。
- str-index(string, substring) 返回 substring 子字符串第一次在 string 中出现的位置。如果没有匹配到子字符串，则返回 null。

```js
str-index(abcd, a)  => 1
str-index(abcd, ab) => 1
str-index(abcd, X)  => null
str-index(abcd, c)  => 3
```
- str-insert(string, insert, index) 在字符串 string 中 index 位置插入 insert。

```js
str-insert("Hello world!", " runoob", 6)
结果: "Hello runoob world!"
```

- str-slice(string, start, end) 从 string 中截取子字符串，通过 start-at 和 end-at 设置始末位置，未指定结束索引值则默认截取到字符串末尾。
```js
str-slice("abcd", 2, 3)   => "bc"
str-slice("abcd", 2)      => "bcd"
str-slice("abcd", -3, -2) => "bc"
str-slice("abcd", 2, -2)  => "bc"
```
- to-lower-case(string)
- to-upper-case(string)
- unique-id() 返回一个无引号的随机字符串作为 id。不过也只能保证在单次的 Sass 编译中确保这个 id 的唯一性。

```js
unique-id()
Result: uad053b1c
```
### Sass 数字函数
- `abs(number)` 返回一个数值的绝对值
- `ceil(number)` 向上取整
- `comparable(num1, num2)` 返回一个布尔值，判断 num1 与 num2 是否可以进行比较
- `floor(number)` 向下取整
- `max(number…)` 返回最大值
- `min(number…)` 返回最小值
-` percentage(number)` 将数字转化为百分比的表达形式
- `random()` 返回 0-1 区间内的小数，
- `round(number)` 返回最接近该数的一个整数，四舍五入

### Sass 列表(List)函数
Sass 列表(List)函数用于处理列表，可以访问列表中的值，向列表添加元素，合并列表等。

Sass 列表是不可变的，因此在处理列表时，返回的是一个新的列表，而不是在原有的列表上进行修改。

列表的起始索引值为 1，记住不是 0。

- `append(list, value, [separator])` 将单个值 value 添加到列表尾部。separator 是分隔符，默认会自动侦测，或者指定为逗号或空格
- `index(list, value)` 返回元素 value 在列表中的索引位置
- `is-bracketed(list)` 判断列表中是否有中括号
- `join(list1, list2, [separator, bracketed])` 合并两列表，将列表 list2 添加到列表 list1 的末尾。separator 是分隔符，默认会自动侦测，或者指定为逗号或空格。 bracketed 默认会自动侦测是否有中括- 号，可以设置为 true 或 false。
- `length(list)` 返回列表的长度
- `list-separator(list)` 返回一列表的分隔符类型。可以是空格或逗号。
- `nth(list, n)` 获取第 n 项的值
- `set-nth(list, n, value)` 设置列表第 n 项的值为 value
- `zip(lists)` 将多个列表按照以相同索引值为一组，重新组成一个新的多维度列表

### Sass Map(映射)函数
Sass Map(映射)对象是以一对或多对的 key/value 来表示。

Sass Map 是不可变的，因此在处理 Map 对象时，返回的是一个新的 Map 对象，而不是在原有的 Map 对象上进行修改。

- `map-get(map, key)` 返回 Map 中 key 所对应的 value(值)。如没有对应的 key，则返回 null 值。

```js
$font-sizes: ("small": 12px, "normal": 18px, "large": 24px)
map-get($font-sizes, "small")
结果: 12px
```

- `map-has-key(map, key)` 判断 map 是否有对应的 key，存在返回 true，否则返回 false

```js
$font-sizes: ("small": 12px, "normal": 18px, "large": 24px)
map-has-key($font-sizes, "big")
结果: false
```

- `map-keys(map)` 返回 map 中所有的 key 组成的队列

```js
$font-sizes: ("small": 12px, "normal": 18px, "large": 24px)
map-keys($font-sizes)
结果: "small", "normal, "large"
```

- `map-merge(map1, map2)` 合并两个 map 形成一个新的 map 类型，即将 map2 添加到 map1的尾部

```js
$font-sizes: ("small": 12px, "normal": 18px, "large": 24px)
$font-sizes2: ("x-large": 30px, "xx-large": 36px)
map-merge($font-sizes, $font-sizes2)
结果: "small": 12px, "normal": 18px, "large": 24px, "x-large": 30px, "xx-large": 36px
```

- `map-remove(map, keys…)` 移除 map 中的 keys，多个 key 使用逗号隔开

```js
$font-sizes: ("small": 12px, "normal": 18px, "large": 24px)
map-remove($font-sizes, "small")
结果: ("normal": 18px, "large": 24px)
map-remove($font-sizes, "small", "large")
结果: ("normal": 18px)
```

- `map-values(map)` 返回 map 中所有的 value 并生成一个队列
```js
$font-sizes: ("small": 12px, "normal": 18px, "large": 24px)
map-values($font-sizes)
结果: 12px, 18px, 24px
```

### Sass 选择器函数
Sass 选择器函数用于查看与处理选择器。
- `is-superselector(super, sub)` 比较两个选择器匹配的范围，即判断 super 选择器是否包含了 sub 选择器所匹配的范围，是的话返回 true，否则返回 false。

```js
is-superselector("div", "div.myInput")
结果: true
is-superselector("div.myInput", "div")
结果: false
is-superselector("div", "div")
结果: true
```

- `selector-append(selectors)` 将第二个 (也可以有多个) 添加到第一个选择器的后面。 selector.

```js
selector-append("div", ".myInput")
结果: div.myInput
selector-append(".warning", "__a")
结果: .warning__a
```

- `selector-nest(selectors)` 返回一个新的选择器，该选择器通过提供的列表选择器生成一个嵌套的列表

```js
selector-nest("ul", "li")
结果: ul li
selector-nest(".warning", "alert", "div")
结果: .warning div, alert div
```

- `selector-parse(selector)` 给定一个选择器，用replacement 替换 original 后返回一个新的选择器队列

```js
selector-replace("p.warning", "p", "div")
结果: div.warning
```

- `selector-unify(selector1, selector2)` 将两组选择器合成一个复合选择器。如两个选择器无法合成，则返回 null 值。

```js
selector-unify("myInput", ".disabled")
结果: myInput.disabled
selector-unify("p", "h1")
结果: null
```

- `simple-selectors(selectors)` 将合成选择器拆为单个选择器

```js
simple-selectors("div.myInput")
结果: div, .myInput
simple-selectors("div.myInput:before")
结果: div, .myInput, :before
```

### Sass Introspection 函数
Sass Introspection 函数比较少用于构建样式表，一般用于代码的调试上。
- `call(function, arguments…)` 函数的动态调用，即调用函数 function 参数为 arguments，并返回结果。
- `content-exists()` 查看当前的混入是否传递 @content 块。
- `feature-exists(feature`) 检查当前的 Sass 实现是否支持该特性。

```js 
feature-exists("at-error");
结果: true
```

- f`unction-exists(functionname)` 检测指定的函数是否存在

```js
function-exists("nonsense")
结果: false
```
- `get-function(functionname, css: false)` 返回指定函数。如果 css 为 true，则返回纯 CSS 函数
- `global-variable-exists(variablename)` 检测某个全局变量是否定义

```js
variable-exists(a)
结果: true
```
- `inspect(value)` 返回一个字符串的表示形式，value 是一个 sass 表达式。
- `mixin-exists(mixinname)` 检测指定混入 (mixinname) 是否存在

```js
mixin-exists("important-text")
结果: true
```

- `type-of(value)` 返回值类型。返回值可以是 number, string, color, list, map, bool, null, function, arglist

```js
type-of(15px)
结果: number
type-of(#ff0000)
结果: color
```

- `unit(number)` 返回传入数字的单位（或复合单位）

```js
unit(15px)
结果: px
```

- `unitless(number)` 返回一个布尔值，判断传入的数字是否带有单位

```js
unitless(15px)
结果: false
unitless(15)
结果: true
```
- `variable-exists(variablename)` 判断变量是否在当前的作用域下

```js
variable-exists(b)
结果: true
```

### Sass 颜色函数
- `rgb(red, green, blue)` 创建一个 Red-Green-Blue (RGB) 色。其中 R 是 “red” 表示红色，而 G 是 “green” 绿色，B 是 “blue” 蓝色。
- `rgba(red, green, blue, alpha)` 根据红、绿、蓝和透明度值创建一个颜色。
- `hsl(hue, saturation, lightness)` 通过色相（hue）、饱和度(saturation)和亮度（lightness）的值创建一个颜色
- `hsla(hue, saturation, lightness, alpha)` 通过色相（hue）、饱和度(saturation)、亮度（lightness）和透明（alpha）的值创建一个颜色。
- `grayscale(color)` 将一个颜色变成灰色，相当于 desaturate( color,100%)
- `complement(color)` 返回一个补充色，相当于adjust-hue($color,180deg)
- `invert(color, weight) `返回一个反相色，红、绿、蓝色值倒过来，而透明度不变。

### Sass 颜色获取
red(color) 从一个颜色中获取其中红色值（0-255）。
green(color) 从一个颜色中获取其中绿色值（0-255）。
blue(color) 从一个颜色中获取其中蓝色值（0-255）。
hue(color) 返回颜色在 HSL 色值中的角度值 (0deg - 255deg)。
saturation(color) 获取一个颜色的饱和度值(0% - 100%)。
lightness(color) 获取一个颜色的亮度值(0% - 100%)。
alpha(color) Returns the alpha channel of color as a number between 0 and 1
opacity(color) 获取颜色透明度值(0-1)。