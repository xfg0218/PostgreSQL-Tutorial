**摘要**：在本教程中，您将学习如何使用 `PostgreSQL DROP INDEX` 语句删除索引。

# `PostgreSQL DROP INDEX` 语句简介

`DROP INDEX` 语句允许您从数据库中删除现有索引。

以下是 `DROP INDEX` 语句的基本语法：

```sql
DROP INDEX [CONCURRENTLY] [IF EXISTS] index_name
[CASCADE | RESTRICT]
```

在此语法中：

- 首先，在 `DROP INDEX` 关键字后指定要删除的索引名称。
- 其次，使用 `CONCURRENTLY` 选项删除索引，而不锁定表上的并发选择、插入、更新和删除。
- 第三，仅当索引有条件地存在时，才使用 `IF EXISTS` 删除索引。如果尝试删除不存在的索引而不使用 `IF EXISTS` 选项，则会遇到错误。
- 最后，使用 `CASCADE` 选项自动删除依赖于索引的对象，进而自动删除依赖于这些对象的所有对象。如果索引具有任何依赖对象，则 `RESTRICT` 选项将拒绝索引删除。`RESTRICT` 选项是缺省值。

# `PostgreSQL DROP INDEX` 语句示例

首先，在 `products` 表上创建一个索引：

```sql
CREATE INDEX products_safety_stock_idx
ON products(safety_stock);
```

其次，删除名称为 `products_safety_stock_idx` 索引：

```sql
DROP INDEX products_safety_stock_idx;
```

# 总结

- 使用 `DROP INDEX` 语句从数据库中删除索引。