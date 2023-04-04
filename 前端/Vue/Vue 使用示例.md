# Vue 使用示例

[TOC]

## 1. 动态获取元素高度

思路：使用 `$nextTick()` 方法在 `this.$refs` 的对象中异步获取元素的高度

代码：

```js
function() {
    this.$nextTick(() => {
    this.$refs.refName2.style.height = window.getComputedStyle(this.$refs.refName1).height
})
}
```
