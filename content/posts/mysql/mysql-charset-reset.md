---
title: "MySQL 批量修改字符集"
subtitle: ""
date: 2021-12-22T08:44:41+08:00
lastmod: 2021-12-22T08:44:41+08:00
draft: false
authors: ["ryoii"]
description: ""

tags: ["MySQL"]
categories: ["MySQL"]
---

---

`MySQL` 有一个大家都知道的坑点，就是 `utf8` 字符集并不是标准规范里的 `utf8`。对此，`MySQL` 给出了 `utf8mb4` 字符集对应标准的 `utf8`。

但是如果旧数据库已经使用上错误的 `utf8` 字符集，现在要进行切换就比较麻烦了。而 `MySQL` 是没有提供这样 DDL 的，所以有了批量修改字符集的需求。

## 大致思路

读取表结构，给指定数据库生成 DDL 语句再批量执行。

修复过程大致上分为三步
1. 修复字段
2. 修复表
3. 修复数据库

### 修复表字段

`MySQL` 表结构都保存在 `information_schema` 这个库中。其中 `COLUMNS` 这张表保存了所有的表字段。

```sql
SELECT 
  TABLE_SCHEMA '数据库',
  TABLE_NAME '表名',
  COLUMN_NAME '列名',
  CHARACTER_SET_NAME '字符集',
  COLLATION_NAME '排序规则',
  CONCAT('ALTER TABLE ', TABLE_SCHEMA,'.',TABLE_NAME, ' MODIFY COLUMN ',COLUMN_NAME, ' ', COLUMN_TYPE, ' COMMENT "', COLUMN_COMMENT,  '" CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;') '修复语句'
FROM information_schema.COLUMNS
WHERE 
  TABLE_SCHEMA = '<database>'
  AND TABLE_NAME = '<table>'
  AND COLLATION_NAME RLIKE 'utf8';
```

{{< admonition type="warning" open=true >}}
这里的表备注一定不要去掉，不然表备注是会被清空的。
{{< /admonition >}}

{{< admonition open=true >}}
`<database>` 和 `<table>` 填要修改的表和库名，限定修改范围。`COLLATION_NAME` 是原字符集，这里限定了只修改错误的 `utf8` 编码，如果修改全部字段编码，去掉这个过滤条件就好了。
{{< /admonition >}}

最后把生成出来的修复语句拷贝出来，粘贴到你熟悉的工具批量执行，或者放到文本里作为 `.sql` 直接执行。

### 修复表

表的信息在 `information_schema.TABLES` 这张表

```sql
SELECT 
  TABLE_SCHEMA '数据库',
  TABLE_NAME '表名',
  TABLE_COLLATION '原排序规则',
  CONCAT('ALTER TABLE ',TABLE_SCHEMA,'.', TABLE_NAME, 'COLLATE=utf8mb4_general_ci;') '修复语句'
FROM information_schema.TABLES
WHERE TABLE_COLLATION RLIKE 'utf8';
```

同样把修复语句拷贝出来批量执行

### 修复数据库

数据库的信息在 `information_schema.SCHEMATA` 这张表

```sql
SELECT 
  SCHEMA_NAME '数据库',
  DEFAULT_CHARACTER_SET_NAME '原字符集',
  DEFAULT_COLLATION_NAME '原排序规则',
  CONCAT('ALTER DATABASE ', SCHEMA_NAME, ' CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;') '修复语句'
FROM information_schema.SCHEMATA
WHERE DEFAULT_CHARACTER_SET_NAME RLIKE 'utf8';
```
