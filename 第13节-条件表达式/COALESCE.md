**摘要**：在本教程中，您将学习如何使用 `PostgreSQL CASE` 表达式在查询中执行条件逻辑。

# `PostgreSQL COALESCE` 函数入门

在 `PostgreSQL` 中，`NULL` 表示未知值。`NULL` 表示记录时数据未知或缺失。

`PostgreSQL COALESCE()` 函数接受参数列表并返回第一个非 `NULL` 参数。下面显示了 `COALESCE()` 函数的语法：

```sql
COALESCE(value1, value2, ...)
```

`COALESCE` 函数接受可变数量的参数（ `value1` 、`value2` 等）。它从左到右计算参数并返回第一个非 `NULL` 的参数。

如果所有参数均为 `NULL` 则 `COALESCE` 函数返回 `NULL` 。

# `PostgreSQL COALESCE（）` 函数示例

我们将使用 `inventory` 数据库中的 `profiles` 表进行演示。

以下示例从用户配置文件中检索名字和工作电话号码：

```sql
SELECT
  first_name,
  work_phone
FROM
  profiles
ORDER BY
  first_name;
```

输出：

```sql
 first_name |  work_phone
------------+--------------
 Alice      | NULL
 Bob        | NULL
 Charlie    | NULL
 Jane       | 408-456-7891
 John       | 408-456-7890
```

输出指示 `Alice`、`Bob` 和 `Charlie` 的工作电话号码为 `NULL` :

为了使列表更便于业务，您可以使用 `COALESCE` 函数将 `NULL` 替换为 `N/A`（不可用）。

```sql
SELECT
  first_name,
  COALESCE(work_phone, 'N/A') work_phone
FROM
  profiles
ORDER BY
  first_name;
```

输出：

```sql
 first_name |  work_phone
------------+--------------
 Alice      | N/A
 Bob        | N/A
 Charlie    | N/A
 Jane       | 408-456-7891
 John       | 408-456-7890
```

在此示例中，如果工作电话不是 `NULL`，则 `COALESCE` 返回工作电话，如果工作电话为 `NULL` ，则返回 `N/A` 。

假设您打印出所有用户的联系人列表，包括名字和电话号码。如果工作电话号码不可用，您可以使用家庭电话号码。

为此，您可以使用 `COALESCE` 函数，如下所示：

```sql
SELECT
  first_name,
  COALESCE(work_phone, home_phone) phone
FROM
  profiles
ORDER BY
  first_name;
```

输出:

```sql
 first_name |    phone
------------+--------------
 Alice      | 408-111-4444
 Bob        | 408-111-5555
 Charlie    | NULL
 Jane       | 408-456-7891
 John       | 408-456-7890
```

在此语句中，`COALESCE` 将返回工作电话号码（如果它不是 `null` ）或家庭电话号码。

输出指示 `Charlie` 没有工作和家庭电话号码。

您可以在 `COALESCE` 函数中将该 `NULL` 替换为 `N/A` ，如下所示：

```sql
SELECT
  first_name,
  COALESCE(work_phone, home_phone, 'N/A') phone
FROM
  profiles
ORDER BY
  first_name;
```

输出：

```sql
 first_name |    phone
------------+--------------
 Alice      | 408-111-4444
 Bob        | 408-111-5555
 Charlie    | N/A
 Jane       | 408-456-7891
 John       | 408-456-7890
```

# 总结

- 使用 `PostgreSQL COALESCE` 函数检索第一个非空参数。

