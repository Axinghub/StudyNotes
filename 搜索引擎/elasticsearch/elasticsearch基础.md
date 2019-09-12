---
typora-copy-images-to: images
typora-root-url: ./
---

## 文档：

elasticsearch是面向文档的，文档是所有可搜索数据的最小单位；相当于数据库表里的一条记录。

文档会被序列化成json格式保存在elasticsearch中

每个文档都有一个id

#### 元数据：

\_index:文档所属的索引名

_type:文档所属的类型名

_id :文档唯一id

\_source :文档的原始json数据

_all :整合所有字段内容到该字段，已被废除

_version:文档所属的索引名

_score：相关性打分

## 索引：

索引是文档的容器，是一类文档的集合，

index体现了逻辑空间的概念：每个索引都有自己的Mapping定义，用于定义包含的文档字段和字段类型。

Shard体现了物理空间的概念：索引中的数据分散在Shard上

mapping定义了字段的类型

Setting定义了不同的数据分布

7.0以后一个索引只能创建一个type

| RDBMS  | Elasticsearch |
| ------ | ------------- |
| Table  | index         |
| row    | document      |
| cloumn | field         |
| schema | mapping       |
| sql    | dsl           |

