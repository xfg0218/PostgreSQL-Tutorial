**摘要**: 在本教程中，你将学习使用 `PostgreSQL` 的 `IS NULL` 运算符来判断一个值是否为 `NULL`

# `PostgreSQL IS NULL` 运算符简介

在 `PostgreSQL` 中，`NULL` 是一个表示未知数据的标记。要检查一个值是否为 `NULL` ，不能使用等于运算符将其与 `NULL` 进行比较。

由于 `NULL` 表示未知，将一个值与未知值进行比较会产生未知结果 `(NULL)` 。例如：

```sql
SELECT
  10 = NULL AS result;
```

输出：

```sql
 result
--------
 NULL
```

在这个示例中，我们使用等于运算符将数字 `10` 与 `NULL` 进行比较。比较结果返回 `NULL`。

要将一个值与 `NULL` 进行比较，需使用 `IS NULL` 运算符：

```sql
value IS NULL
```

`IS NULL` 运算符将值与 `NULL` 进行比较，如果该值为 `NULL` ，则返回 `true` ，否则返回`false` 。例如：

```sql
SELECT
  NULL IS NULL null_vs_null,
  10 IS NULL null_vs_10;
```

输出：

要否定 `IS NULL` 运算符的结果，可以使用 `IS NOT NULL` 运算符。

```sql
value IS NOT NULL
```

`IS NOT NULL` 运算符将值与 `NULL` 进行比较，如果该值不为空，则返回 `true` ，否则返回`false`。

要确保列不包含 `NULL` ，可以使用 [`NOT NULL`](../第3节-使用表格数据/非空约束.md) 约束。

如果你想使用函数而非 `IS NULL` 运算符来处理 `NULL` ，可以使用 `NULLIF` 、`ISNULL` 和`COALESCE` 函数。

# `PostgreSQL IS NULL` 运算符示例

我们将使用 `profiles` 表来演示 `IS NULL` 运算符：

![images](../images/is-null.png)

以下 `SELECT` 语句使用 `IS NULL` 运算符来查询没有工作电话的用户：

```sql
SELECT
  first_name,
  last_name,
  work_phone
FROM
  profiles
WHERE
  work_phone IS NULL;
```

输出：

```sql
 first_name | last_name | work_phone
------------+-----------+------------
 Alice      | Jones     | NULL
 Bob        | Brown     | NULL
 Charlie    | Davis     | NULL
```

该查询返回 `work_phone` 的值为 `NULL` 的员工。

# `PostgreSQL IS NOT NULL` 示例

以下查询使用 `IS NOT NULL` 运算符来查询有工作电话的员工：

```sql
SELECT
  first_name,
  last_name,
  work_phone
FROM
  profiles
WHERE
  work_phone IS NOT NULL;
```

输出：

```sql
 first_name | last_name |  work_phone
------------+-----------+--------------
 John       | Doe       | 408-456-7890
 Jane       | Smith     | 408-456-7891
```

# `psql` 与 `NULL` 

`psql` 是一个使用命令行界面与 `PostgreSQL` 服务器交互的客户端程序。

`psql` 程序在默认情况下会将 `NULL` 显示为空白。若要使用 `NULL` 文字字符串来表示 `NULL` ，可以使用以下命令：

```sql
\pset null NULL
```

# 总结

- `IS NULL` 运算符将一个值与 `NULL` 进行比较，如果该值为 `NULL` ，则返回 `true` ，否则返回`false`。
- `IS NOT NULL` 运算符将某个值与 `NULL` 进行比较，如果该值不是 `NULL` ，则返回 `true` ，否则返回 `false` 。

