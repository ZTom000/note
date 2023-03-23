# Redis基础数据结构.md

[toc]

## 1. String 

### 1.1 . 数据结构

内部实现：字符数组

键值对（Key-Value）:  Redis 所有的数据类型都以一个唯一 Key 字符串作为名称，通过 Key 获取对应 Value 的数据。

### 1.2.  基本指令

```shell
# 键值对
set name codehole	# 存入值
get name	# 取出值
exists name	# 是否存在 key
del name	# 删除 key

# 批量处理
mset name1 boy name2 girl name3 unknown # 批量存入
mget name1 name2 name3 # 批量获取，返回一个列表

# 拓展命令
set name codehole
get name 
expire name 5 # 过期时间（单位： s）
setex name 5 codehole # 等价于 set + expire
setnx name holycoder # 当 name 不存在时创建键值对

# 计数
set age 30
incr age # 自增操作，当数值超出范围时 Redis 会报错（取值范围： signed long）
```



### 1.3.  注意事项

1. 通常 Java 对象使用 Redis 存储时，先将对象使用 JSON 序列化成字符串，然后存入 Redis 中。从 Redis 中取出对象时，将 JSON 字符串反序列化成 Java对象。

## 2. List

### 2.1 . 数据结构

基础数据结构：链表（ LinkedList ）,插入和删除操作的时间复杂度 O(1) ，索引使劲按复杂度 O(n)。

内部实现：

​		压缩列表（ ZipList ）：当 List 元素较少时， Redis 会使用一块连续的内存，将所有元素连续存储。

​		快速列表（ QuickList ）：当 List 元素较多时， Redis 会将 List 转换为（ QuickList ），实际情况将 ziplist 使用双向指针穿起来使用

### 2.2 . 基本指令

``` shell
# 右进左出（先进先出）实现队列
rpush books python java golang	# List 存入数据
llen books # 查询 List 长度
lpop books # 获取列表左边第一个元素

# 右进右出 （后进先出）实现堆栈
rpop books # 获取列表右边第一个元素 

# 满操作（慎用）
lindex books 1 # 会对 List 遍历，时间复杂度 O(n) 
lrange books 0 -1 # 获取 List 所有元素，时间复杂度 O(n)  
ltrim books 1 -1 # ltrim key start_index end_index 保留区间内的所有元素，删除区间外的元素（O(n)）
ltrim books 1 0 # 清空队列
```



### 2.3. 使用场景

1. 异步队列：将需要延迟处理的任务结构体序列化成字符串放入 Redis 的 List 中，另一个线程从列表中轮询数据进行处理。

### 2.4 . 注意事项

1. 当 List 中最后一个元素取出后，该数据结构会被自动删除，内存自动回收

## 3. Hash

### 3.1 . 数据结构

基础数据结构：数组 + 链表

内部实现：

​		数组 + 链表实现

​		渐进式 Rehash ：当进行 Rehash 时，会保留新旧两个 hash  表，查询时会同时查询两个 hash表，在后续的 hash 操作中循序渐进的将旧 hash 的内容一点点的迁移到新的 hash 中，当迁移完成后新的 hash 表将取而代之

### 3.2 . 基本指令

``` shell
# 存取指令
hset books java "think in java"	# hash 存存储指令，命令行字符串如果包含空格需要使用引号括起来
hest books golang "concurrency in go" 
hset books python "python cookbook"

hgetall books	# 获取所有值，key 和 value 间隔出现
hlen books	# 获取 hash 长度
hget books java # 获取指定元素
hset books golang "learning go programming" # 更新操作，返回值为0
hmset books java "effective java" python "learning python" golang "modern golang programming" # 批量 set
# 数值操作
hset user-ztom age 27
hincrby user-zton age 1 # 自加操作
```



### 3.3. 使用场景

1. Hash 可以用于存储用户信息，在 Hash 中用户信息可以按每个字段单独存储，获取时可以部分获取。缺点：Hash 结构的存储消耗高于单个字符串

### 3.4 . 注意事项

1. 当 Hash 中最后一个元素取出后，该数据结构会被自动删除，内存自动回收

## 4. Set

### 4.1 . 数据结构

### 4.2 . 基本指令

### 4.3. 使用场景

### 4.4 . 注意事项

## 5. Zset

### 5.1 . 数据结构

### 5.2 . 基本指令

### 5.3. 使用场景

### 5.4 . 注意事项

