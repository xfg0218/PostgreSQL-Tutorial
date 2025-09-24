**摘要**：在本教程中，您将学习如何使用 `PostgreSQL CREATE INDEX` 语句为表的一个或多个列创建索引。

# `PostgreSQL CREATE INDEX` 语句简介

`CREATE INDEX` 语句允许您在表的一个或多个列上创建索引。

以下是 `CREATE INDEX` 语句的基本语法：

```sql
CREATE INDEX [IF NOT EXISTS] [index_name]
ON  tablename (column1, column2);
```

在此语法中：

- 首先，在 `CREATE INDEX` 关键字后指定索引名称。`index_name` 是可选的。如果您不提供名称，`PostgreSQL` 会隐式生成一个名称并将其分配给索引。
- 其次，使用 `IF NOT EXISTS` 指示 `PostgreSQL` 在已存在同名索引时不要抛出错误。在这种情况下，`PostgreSQL` 将发出通知。
- 第三，提供表的名称以创建索引。
- 最后，在大括号内指定要索引的一个或多个列。

`PostgreSQL` 使用以下命名约定来生成索引名称：

```sql
tablename_column_idx
```

如果索引由两列或多列组成，则生成的索引名称将为：

```sql
tablename_column1_column2_idx
```

如果您没有为同一组列创建具有显式名称的索引，`PostgreSQL` 将为同一组列创建具有不同名称的冗余索引。

# `PostgreSQL CREATE INDEX` 语句示例

首先，在 `transactions` 表上为 type 列创建一个索引：

```sql
CREATE INDEX ON transactions(type);
```

此语句创建一个名为 `transations_type_idx` 的新索引。

其次，使用以下查询显示 `transactions` 表的索引：

```sql
SELECT
  indexname,
  indexdef
FROM
  pg_indexes
WHERE
  tablename = 'transactions';
```

输出：

```sql
       indexname       |                                         indexdef
-----------------------+-------------------------------------------------------------------------------------------
 transactions_pkey     | CREATE UNIQUE INDEX transactions_pkey ON public.transactions USING btree (transaction_id)
 transactions_type_idx | CREATE INDEX transactions_type_idx ON public.transactions USING btree (type)
```

# 创建具有两列的索引

以下语句在库存表上创建一个索引，其中包括两列 `safety_stock` 和 `gross_weight`：

```sql
CREATE INDEX ON products (safety_stock, gross_weight);
```

`PostgreSQL` 生成以下索引名称：

```sql
products_safety_stock_gross_weight_idx
```

# 总结

- 使用 `CREATE INDEX` 语句创建新索引。

