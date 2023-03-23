# Elasticsearch 基本概念

[toc]

## 1. 文档（Document）

### 1.1 定义

* Elasticsearch 是面向文档的，文档是所有可搜索数据的最小单位。
* 文档会被序列化成 JSON 格式并保存在 Elasticsearch 中；JSON 文档格式灵活，不需要预定义格式；字段的类型可以指定或通过 Elasticsearch 自动推算，支持数组与嵌套。
* 每个文档都有一个 Unique ID ，用户可以自己指定 iD 或通过 Elasticsearch  自动生成。

### 1.2  元数据 

定义： 用来标注文档的相关信息

<table>
    <tr>
        <td>元数据名</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>_index</td>
        <td>文档所属索引名</td>
    </tr>
    <tr>
        <td>_type</td>
        <td>文档所属类型名</td>
    </tr>
    <tr>
        <td>_id</td>
        <td>文档唯一 id</td>
    </tr>
    <tr>
        <td>_source</td>
        <td>文档的源时 JSON 数据</td>
    </tr>
    <tr>
        <td>_all</td>
        <td>整合所有字段内容到该字段，已弃用</td>
    </tr>
    <tr>
        <td>_version</td>
        <td>文档的版本信息</td>
    </tr>
    <tr>
        <td>_score</td>
        <td>相关性打分</td>
    </tr>
</table>

## 2. 索引

### 2.1. 定义

* 索引是文档的容器，是一类文档的集合。
* Index 体现逻辑空间的概念：每个索引都有自己的 Mapping 定义 ，用于定义所包含文档的字段名和字段类型。
* Shard 体现物理空间的概念： 索引中的数据分散在 Shard 上。
* 索引的 Mapping 定义文档字段的类型。
* 索引的 Setting 定义不同的数据分布。

### 2.2. 语义

名词：一个 Elasticsearch  集群中，可以创建许多不同索引。

动词：保存一个文档到 Elasticsearch 的过程也叫索引（indexing）。

## 3. 节点

### 3.1. 定义

* 节点是 Elasticsearch  的一个实例，一台机器可以运行多个 Elasticsearch 实例；生产环境中建议一台机器上只运行一个实例。
* 每个节点都有一个名称，通过配置文件配置，或者启动时使用设置运行参数` -E node.name = nodename` 指定 。
* 每个节点启动后，会分配一个 UID 并保存在 Data 目录下。
### 3.2. 节点分类

#### 3.2.1. master-eligible nodes

定义：每个节点启动后默认是一个 master-eligible  节点；master-eligible 节点可以参加选举流程 master 主节点。

#### 3.2.2. master node 

* 当第一个节点启动时，它会将自己选举成 master 节点。
* 每个节点都保存了集群的状态，只有 master 节点才能修改集群的状态信息。
  * 集群状态 （Cluster State）：维护了一个集群中，必要的信息。
    * 所有节点的信息。
    * 所有的索引和其相关的 Mapping 与 Seting 信息。
    * 分片的路由信息。

#### 3.2.3. data node

* 可以保存数据的节点，叫做 data node ，负责保存分片数据。

#### 3.2.4. coordinating node

* 负责接受 client 请求，将请求分发到合适的节点，最终把结果汇集到一起。每个节点默认起到了 coordinating node 的作用。

#### 3.2.5. hot & warm node 



#### 3.2.6. machine learning node



#### 3.2.7. tribe node



## 4. 集群

## 5. 分片

## 6. 副本