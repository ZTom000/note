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

### 1.2. Array.from()

作用：将对象作为数组返回

```js
// 示例
Array.from(obj) // 将对象 obj 作为数组返回 
```

### 1.3. Array.prototype.filter()

说明：`filter()`实现对数组每个元素做过滤处理并返回到新数组中

```js
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];
// filter 实现过滤的方法，数组内每个元素均执行一次，根据结果返回 result 数组中
const result = words.filter(word => word.length > 6);

console.log(result);
```

### 1.4. Array.prototype.sort()

说明:

```javascript
// 基础类型数组
const v[] = [0, 10, 9, 5, 8, 13]
v.sort();

const s[] = ['abc', 'eas', 'qwe']
s.sort();
// 对象数组
const obj[] = [
    {'id': 10, 'name': 'a'}, 
    {'id': 1, 'name': 'b'}, 
    {'id': 2, 'name': 'c'}
]
obj.sort((a, b) -> {
    if(a.id > b.id){
        return -1;
    } else {
    return 1
    }
});
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
