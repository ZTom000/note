# Array 对象食用指南

[TOC]

## 1. 方法

### 1.1. Array.foreach()

```js
// 示例
const array1 = [1, 2, 3, 4, 5, 6]
// 参数说明： element 数组元素， index 数组下标
array1.forEach((element, index) => {
    console.log(element + index)
})
```
