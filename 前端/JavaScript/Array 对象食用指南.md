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

## 2. 运算符

### 2.1. param1?param2:param3

说明：若`param1` 则 `param2` 否则 `param3`

#### 2.1.1. 示例

简单使用

```js
const age = 26;
const beverage = age >= 21 ? "Beer" : "Juice";
console.log(beverage); // "Beer"
```

处理 `null` 值

```js
const hello = (person) => {
    const name = person ？ person.name : "test"
    return "hello, ${name}"
}
console.log(hello({ name: "name"}));
console.log(hello(null);
```

条件链

```js
function example() {
  return condition1 ? value1
        : condition2 ? value2
        : condition3 ? value3
        : value4;
}
// 等价于


```
