---
title: ActiveAndroid 源码学习
date: 2016-08-27 11:03:40
tags: [源码, ActiveAndroid]
categories: 源码阅读
blog: true
---



## 分类

- greenDAO 使用工具生成代码，效率高，减少编写重复的代码
- ActiveAndroid、LitePal 使用反射创建代码，效率比较低，接口灵活

<!-- more -->

## 主要类

### Configuration

配置文件包括

1. context: 上下文，使用 ApplicationContext, 会被强引用
2. DatabaseName: Manifast.xml 中配置("AA_DB_NAME")或者默认 "Application.db"
3. DatabaseVersion: 默认为1
4. SqlParser: 语法分析程序，默认 "legacy"
5. mModelClasses: 存储对象类型，Manifast.xml 中配置("AA_MODELS")，默认全局查找所有类，效率低
6. mTypeSerializers: 类型转化工具类，默认支持 Calendar, File, SqlDate, Date。 可以自定义存储的转化，实现TypeSerializer接口，在 Manifast.xml 中配置("AA_SERIALIZERS")

### Cache

1. 使用 LRU cache 存储 Java 实体对象，键为 `{TableName}@{id}`，默认大小 1024
2. 通过类对象(? extends Model)，获取表信息
3. 通过类对象，获取存储转化工具类
3. 通过类对象获取表名
4. 通过类名和ID 获取、添加、删除存储的实体对象
5. 获取数据库，打开、关闭数据库

### DataBaseHelper

1. 继承自 SQLiteOpenHelper
2. 如果数据库不存在，并且 Asserts 目录下有 dataBaseName.db 的文件，会复制到手机上
3. 创建数据表的功能：把所有的子 Model 转成创建表sql，并创建数据表
4. 获取 asserts/migrations 下的所有文件，按照升序排序，执行 (old, current] 版本的sql文件，如: 1.sql, 2.sql

### Model

1. 不能覆盖 getId() 方法，这是作为 key 使用的
2. delete(): 用sql从数据库中删除，从cache移除，发送 ContentProvider 通知数据已经变化
3. save(): 反射获取所有字段的名或者`@Column`定义的值和对应的值，如果mId 为空则 insert 到数据库，发送 ContentProvider 通知数据已经变化
4. loadFromCursor(): 将数据库中获得的 cursor 使用反射解析成 Java 对象，并且存入到 Cache 中，如果有关联的对象，一并获取出来，
5. getMany(): 

静态方法：

1. `Model.delete(Class<? extends Model> type, long id)` 删除数据
2. `Model.load(Class<? extends Model> type, long id)` 查询数据


### ModelInfo

1. mTableInfos 存放所有的 Model 信息，如果没有在 Manifast.xml 中配置，会遍历所有的类对象查找 Model 的子类
2. mTypeSerializers 支持的存储类型，如果没有在 Manifast.xml 中配置，才会遍历搜索


### TableInfo 

1. 跟 Model 一一对应
2. 对传入的 Model类，会解析其中的 `@Table()`,`@Column`，包括子类的属性。得到<属性,表字段> 的键值对


### ContentProvider

ContentProvider: 是一个内容提供者，类似于网站，为外界提供数据。数据的存储可以是 SP，数据库，文件等，可以跨运用

Uri: `Uri.parse("content://<authority>/<table>")`

在 onCreate 中初始化 ActiveAndroid，包括初始化 Cache

URI_MATCHER & TYPE_CODES: 通过 uri 在 URI_MATCHER 获取 key, 再用 key 获取 TYPE_CODES 中的 Model 类对象

getType(): 与 Intent 的 Category 有关，返回类似 `vnd.com.binwin20.xxx.item/vnd.com.binwin20.TableName`

insert,query,delete,update 都是调用 Cache 处理数据。处理完之后，发送 notifyChange

### query 指令

使用状态机，使用 Delete, From, Join, Select, Set, Update 类，拼接成一个 Sql 字符串。  
然后调用 SqlUtils -> Cache 执行 Sql 语句。

{% img "/images/active-android-state-flow.png" %}

