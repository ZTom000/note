# Vue 使用示例

[TOC]

## 1. Vue JavaScript / TypeScript 使用

### 1.1. 动态获取元素高度

思路：使用 `$nextTick()` 方法在 `this.$refs` 的对象中异步获取元素的高度

代码：

```js
function() {
    this.$nextTick(() => {
    this.$refs.refName2.style.height = window.getComputedStyle(this.$refs.refName1).height
})
}
```

### 2. Vue 组件使用

### 2.1. `:class` 动态设置标签 `class`

思路：使用 `param1?param2:param3` 进行判断执行。（ param1 为逻辑判断式，若 `param1` 为 `true` 时则赋值 `param2`，否则赋值 `param3`）

```html
<div :class="param1 ? param2 : param3">test</div>
```
