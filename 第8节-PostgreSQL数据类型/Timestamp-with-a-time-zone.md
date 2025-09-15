**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `TIMESTAMP WITH TIME ZONE` 数据类型来处理带时区的时间戳值。

# `PostgreSQL` 的带时区时间戳`（TIMESTAMP WITH TIME ZONE）`数据类型入门

在 `PostgreSQL` 中，您可以使用 `TIMESTAMP WITH TIME ZONE` 数据类型来存储具有时区信息的时间戳值。

以下是定义类型为 `TIMESTAMP WITH TIME ZONE` 的列的语法：

```sql
CREATE TABLE table_name(
    column_name TIMESTAMP WITH TIME ZONE,
    ...
);
```

`TIMESTAMPTZ` 是 `TIMESTAMP WITH TIME ZONE` 的缩写，因此您可以用它来减少一些输入量：

```sql
CREATE TABLE table_name(
    column_name TIMESTAMPTZ,
    ...
);
```

当你向 `TIMESTAMPTZ` 列中插入时间戳时，`PostgreSQL` 会将其转换为 `UTC` 时间进行存储。这种转换确保了存储的时间戳具有一致性，且不受时区差异的影响。

当你从 `TIMESTAMPTZ` 列中检索时间戳时，`PostgreSQL` 会将存储的 `UTC` 时间戳转换回你会话的时区。这种转换意味着你得到的时间会根据数据库会话设置的时区进行调整。

需要注意的是，`PostgreSQL` 不会在 `TIMESTAMPTZ` 列中存储时区，而是在存储和检索时进行时区转换。

您可以使用 `SET TIME ZONE` 语句来设置数据库会话的时区。例如：

```sql
SET TIME ZONE 'America/New_York';
```

`TIMESTAMPTZ` 能够自动处理夏令时变更和其他与时区相关的调整。

实际上，在处理跨时区时间的应用程序中，你会经常使用 `TIMESTAMP WITH TIME ZONE` 数据类型，例如日程安排应用、国际交易和事件日志记录。

# 获取带时区的当前时间戳

`CURRENT_TIMESTAMP` 函数返回带时区的当前时间。

```sql
SELECT CURRENT_TIMESTAMP;
```

输出：

```sql
       current_timestamp
-------------------------------
 2024-12-08 02:42:59.773965-08
```

它显示的是 `US/Pacific` 时区的当前时间。

要将数据库会话的时区设置为另一个时区，请使用 `SET TIME ZONE` 语句：

```sql
SET TIME ZONE 'America/New_York';
```

如果你再次获取当前时间戳，你会看到时间已调整为 `America/New_York` 时区：

```sql
SELECT CURRENT_TIMESTAMP;
```

输出：

```sql
       current_timestamp
-------------------------------
 2024-12-08 05:43:11.587639-05
```

要获取受支持的时区名称列表，请使用以下语句：

```sql
SELECT
  name,
  abbrev,
  utc_offset,
  is_dst
FROM
  pg_timezone_names;
```

# 为 `TIMESTAMPTZ` 列设置默认值

要为 `TIMESTAMPTZ` 列设置默认值，您可以使用 `DEFAULT` 约束：

```sql
CREATE TABLE table_name(
    column_name TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);
```

在这个示例中，如果您插入或更新 `TIMESTAMPTZ` 列时未提供值，`PostgreSQL` 会使用 `CURRENT_TIMESTAMP` 函数返回的当前时间戳来进行插入或更新操作。


# `PostgreSQL TIMESTAMPTZ` 数据类型示例

首先，创建一个名为 `inventory_scans` 的表，用于跟踪库存中已扫描的产品：

```sql
CREATE TABLE inventory_scans (
  scan_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  warehouse_id INT NOT NULL,
  product_id INT NOT NULL,
  serial_no VARCHAR(25) NOT NULL,
  quantity INT NOT NULL,
  scanned_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

其次，向 `inventory_scans` 表中插入一些行：

```sql
INSERT INTO
  inventory_scans (warehouse_id, product_id, serial_no, quantity)
VALUES
  (1, 1, 'F2LZK3H8N72Q', 1),
  (2, 1, 'R58N40Y1A2B3', 1) 
RETURNING *;
```

输出:

```sql
 scan_id | warehouse_id | product_id |  serial_no   | quantity |          scanned_at
---------+--------------+------------+--------------+----------+-------------------------------
       1 |            1 |          1 | F2LZK3H8N72Q |        1 | 2024-12-08 05:44:11.998853-05
       2 |            2 |          1 | R58N40Y1A2B3 |        1 | 2024-12-08 05:44:11.998853-05
```

# 总结

- 使用 `PostgreSQL` 的 `TIMESTAMP WITH TIME ZONE` 数据类型（或 `TIMESTAMPTZ` ）来处理带时区的时间戳值。
- 使用 `CURRENT_TIMESTAMP` 获取带时区的当前时间戳。


