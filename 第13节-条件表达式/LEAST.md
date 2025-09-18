**摘要**：在本教程中，您将学习如何使用 `PostgreSQL LEAST` 函数从值列表中选择最低值。

# `PostgreSQL LEAST` 函数入门

`LEAST` 函数接受值列表并返回最小值。

下面是 `LEAST` 函数的语法：

```sql
LEAST (expression1, expression2, ....)
```

`LEAST` 函数忽略 `NULL` 如果所有表达式的计算结果都为 `NULL` 它将返回 `NULL` 。

> 在 `SQL` 标准中，如果任何表达式的计算结果为 `NULL` 则 `LEAST` 函数返回 `NULL` 。某些数据库系统以这种方式运行，例如 `MySQL` 。

# 基本 `PostgreSQL LEAST` 函数示例

以下示例使用 `LEAST` 函数返回列表中的最小数字：

```sql
SELECT LEAST(1,2,3) lowest;
```

输出：

```sql
 lowest
---------
       1
```

`LEAST` 函数返回 `3` ，因为它是列表中的最小数字。

以下示例使用 `LEAST` 函数按字母顺序返回最小字符串：

```sql
SELECT LEAST('A','B','C') smallest;
```

输出：

```sql
 smallest
---------
 A
```

`LEAST` 函数返回字母 `A` 是按字母顺序排列的 `A` `B` 和 `C` 中的最小值。

以下示例使用 `LEAST` 函数比较两个布尔值：

```sql
SELECT LEAST(true, false) smallest;
```

输出：

```sql
 smallest
---------
 f
```

以下示例使用 `LEAST` 函数在日期文字列表中查找最早的日期：

```sql
SELECT
  LEAST ('2025-01-01', '2025-02-01', '2025-03-01', NULL) earliest;
```

输出：

```sql
   earliest
------------
 2025-01-01
```

在此示例中，`LEAST` 函数返回 `January 1` , `2025` 日，因为它最早的日期是 `January 1` , `2025` 、`February 1 2025` 和 `March 1` , `2025` 

请注意，`LEAST` 函数忽略列表中的 `NULL`

# 总结

- 使用 `LEAST` 函数返回值列表中的最小值。
- `LEAST` 函数忽略 `NULL`
