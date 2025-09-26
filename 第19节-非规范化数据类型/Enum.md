**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 枚举数据类型为列定义固定值列表。

# `PostgreSQL` 枚举数据类型概述

`PostgreSQL` 允许您定义一个列，该列使用枚举存储固定值列表。

要创建枚举，请使用具有以下语法的 `CREATE TYPE` 语句：

```sql
CREATE TYPE enum_name 
AS 
ENUM(value1, value2, value3);
```

在此语法中：

- 首先，在 `CREATE TYPE` 子句中提供枚举的名称。
- 其次，在 `ENUM` 关键字后面的括号内列出枚举的值。枚举值区分大小写。值低于其后出现的值，高于之前。

要定义具有枚举类型的列，请使用以下语法：

```sql
column_name enum_name
```

`column_name` 只能存储 `enum_name` 中定义的值。如果您插入或更新不在列表中的值，`PostgreSQL` 将发出错误。

# `PostgreSQL` 枚举数据类型示例

首先，创建一个名为 `priority_type` 的新枚举类型，其中包含三个值：`low`、`medium`和`high`

```sql
CREATE TYPE priority_type AS ENUM('low', 'medium', 'high');
```

其次，创建一个名为 `transfer_orders` 的表，该表具有数据类型为 `priority_type` 的 `priority` 列：

```sql
CREATE TABLE transfer_orders (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  product_id INT NOT NULL,
  from_warehouse INT NOT NULL,
  to_warehouse INT NOT NULL,
  quantity INT CHECK (quantity > 0),
  priority priority_type NOT NULL DEFAULT 'medium'
);
```

第三，在 `transfer_orders` 表中插入一些行：

```sql
INSERT INTO
  transfer_orders (
    product_id,
    from_warehouse,
    to_warehouse,
    quantity,
    priority
  )
VALUES
  (1, 1, 2, 50, 'high'),
  (2, 2, 1, 20, 'low'),
  (2, 2, 1, 20, 'medium'),
  (3, 1, 2, 30, 'high')
RETURNING
  product_id,
  from_warehouse,
  to_warehouse,
  quantity,
  priority;
```

输出：

```sql
 product_id | from_warehouse | to_warehouse | quantity | priority
------------+----------------+--------------+----------+----------
          1 |              1 |            2 |       50 | high
          2 |              2 |            1 |       20 | low
          2 |              2 |            1 |       20 | medium
          3 |              1 |            2 |       30 | high
```

第四，检索转移单并按 `priority` 从高到低排序：

```sql
SELECT
  product_id,
  from_warehouse,
  to_warehouse,
  quantity,
  priority
FROM
  transfer_orders
ORDER BY
  priority DESC;
```

第五，查找优先级 `high` 的调动单：

```sql
SELECT
  product_id,
  from_warehouse,
  to_warehouse,
  quantity,
  priority
FROM
  transfer_orders
WHERE
  priority = 'high';
```

输出：

```sql
 product_id | from_warehouse | to_warehouse | quantity | priority
------------+----------------+--------------+----------+----------
          1 |              1 |            2 |       50 | high
          3 |              1 |            2 |       30 | high
```

# 向枚举添加新值

可以使用 `ALTER TYPE` 语句将新值添加到具有以下语法的现有枚举中：

```sql
ALTER TYPE enum_name
ADD VALUE [IF NOT EXISTS] new_value
[[{BEFORE | AFTER } existing_value];
```

在此语法中：

- 首先，在 `ALTER TYPE` 子句中提供要更改的枚举名称。
- 其次，在 `ADD VALUE` 子句中指定新值。仅当新值不存在时，才使用 `IF NOT EXISTS` 有条件地添加新值。
- 第三，定义新值相对于现有值的位置。默认位置是列表的末尾。

以下示例使用 `ALTER TYPE` 将紧急值添加到 `priority_type` 枚举中：

```sql
ALTER TYPE priority_type
ADD VALUE 'urgent';
```

# 获取枚举值

`ENUM_RANGE()` 函数接受枚举并将枚举值作为数组返回：

```sql
ENUM_RANGE(null::enum_name)
```

例如，以下命令返回 `priority_type` 枚举的所有值：

```sql
SELECT ENUM_RANGE(null::priority_type) priority_values;
```

输出：

```sql
     priority_values
--------------------------
 {low,medium,high,urgent}
```

# 获取枚举的第一个和最后一个值

`ENUM_FIRST` 和 `ENUM_LAST` 函数返回枚举的第一个和最后一个值：

```sql
SELECT
  ENUM_FIRST(NULL::priority_type) first_priority,
  ENUM_LAST(NULL::priority_type) last_priority;
```

输出：

```sql
 first_priority | last_priority
----------------+---------------
 low            | urgent
```

# 重命名枚举值

您可以使用 `ALTER TYPE ... RENAME VALUE` 语句将枚举的值更改为新值：

```sql
ALTER TYPE enum_name
RENAME VALUE existing_value TO new_value;
```

以下示例将 `priority_type` 列的 "紧急" 更改为 "非常高"：

```sql
ALTER TYPE priority_type
RENAME VALUE 'urgent' TO 'very high';
```

# 枚举与外键

如果有一个动态的值列表，则应创建一个单独的查找表，并在主表和查找表之间设置外键关系。

查找表允许在不修改主表的情况下添加或删除值。但是，由于需要连接两个表，`SQL` 的编写可能很复杂并且速度稍慢。

首先，创建一个名为 `priorities` 的表：

```sql
CREATE TABLE priorities (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  priority VARCHAR(255) NOT NULL
);
```

其次，在 `priorities` 表中插入行：

```sql
INSERT INTO
  priorities (priority)
VALUES
  ('low'),
  ('medium'),
  ('high')
RETURNING
  *;
```

输出：

```sql
 id | priority
----+----------
  1 | low
  2 | medium
  3 | high
```

第三，删除 `transfer_orders` 表：

```sql
DROP TABLE IF EXISTS transfer_orders;
```

第四，重新创建一个 `transfer_orders` 表，该表的 `priority` 列链接到 `priorities` 表：

```sql
CREATE TABLE transfer_orders (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  product_id INT NOT NULL,
  from_warehouse INT NOT NULL,
  to_warehouse INT NOT NULL,
  quantity INT CHECK (quantity > 0),
  priority_id INT NOT NULL,
  FOREIGN KEY (priority_id) REFERENCES priorities (id) ON DELETE CASCADE ON UPDATE CASCADE
);
```

第五，在 `transfer_orders` 表中插入行：

```sql
INSERT INTO
  transfer_orders (
    product_id,
    from_warehouse,
    to_warehouse,
    quantity,
    priority_id 
  )
VALUES
  (1, 1, 2, 50, 3),
  (2, 2, 1, 20, 1),
  (2, 2, 1, 20, 2),
  (3, 1, 2, 30, 3)
RETURNING
  product_id,
  from_warehouse,
  to_warehouse,
  quantity,
  priority_id ;
```

输出：

```sql
 product_id | from_warehouse | to_warehouse | quantity | priority_id
------------+----------------+--------------+----------+-------------
          1 |              1 |            2 |       50 |           3
          2 |              2 |            1 |       20 |           1
          2 |              2 |            1 |       20 |           2
          3 |              1 |            2 |       30 |           3
```

最后，从 `transfer_orders` 表中查询数据：

```sql
SELECT
  product_id,
  from_warehouse,
  to_warehouse,
  quantity,
  priority
FROM
  transfer_orders
  INNER JOIN priorities ON priorities.id = transfer_orders.priority_id
ORDER BY
  priority DESC;
```

输出：

```sql
 product_id | from_warehouse | to_warehouse | quantity | priority
------------+----------------+--------------+----------+----------
          2 |              2 |            1 |       20 | medium
          2 |              2 |            1 |       20 | low
          1 |              1 |            2 |       50 | high
          3 |              1 |            2 |       30 | high
```

# 总结

- 枚举是一种具有固定值列表的自定义数据类型。
- 使用 `CREATE TYPE` 语句创建新的枚举。
- 使用 `ALTER TYPE ... ADD VALUE` 语句向枚举添加新值。
- 使用 `ALTER TYPE ... RENAME VALUE` 重命名枚举值。
