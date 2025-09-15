**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `TIMESTAMP` 数据类型在数据库中存储日期和时间值。

# `PostgreSQL TIMESTAMP` 数据类型入门

在 `PostgreSQL` 中，`TIMESTAMP` 数据类型允许你在数据库中存储日期和时间。`TIMESTAMP` 数据类型不包含任何时区数据。

这意味着当你更改 `PostgreSQL` 服务器的时区时，数据库中存储的 `TIMESTAMP` 值不会自动更改。

> 如果您想存储带有时区的日期和时间，可以改用 `TIMESTAMPTZ` 数据类型。

`PostgreSQL` 使用8字节来存储 `TIMESTAMP` 值。`TIMESTAMP` 的有效范围是从 `4713 BC` 到 `294276 AD` 。

以下是定义 `TIMESTAMP` 列的语法：

```sql
CREATE TABLE table_name(
    column_name TIMESTAMP
);
```

如果您想为`TIMESTAMP` 列设置默认值，可以应用 `DEFAULT` 约束，并将 `LOCALTIMESTAMP` 函数返回的默认值作为其值：

```sql
CREATE TABLE table_name(
    column_name TIMESTAMP DEFAULT LOCALTIMESTAMP
);
```

请注意，`LOCALTIMESTAMP` 根据 `PostgreSQL` 服务器的本地时间返回本地日期和时间。

`PostgreSQL` 在时间戳的输入和输出上均采用 `ISO 8601` 标准：

```sql
yyyy-mm-dd hh:mm:ss
```

`ISO 8601` 使用大写字母 `T` 来分隔日期和时间：

```sql
yyyy-mm-dd<strong>T</strong>hh:mm:ss
```

`PostgreSQL` 接受这种输入格式。但为了可读性，它使用空格而非大写字母 `T` 。

```sql
yyyy-mm-dd hh:mm:ss
```

此外，`PostgreSQL` 还支持本教程中未涵盖的其他合理的时间戳格式。

> 实际上，只有当你从数据库中保存和检索时间戳且不进行任何计算时，才应该使用 `TIMESTAMP` 数据类型来存储时间戳。否则，你应该改用 `TIMESTAMPTZ`。

# `PostgreSQL TIMESTAMP` 数据类型示例

`TIMESTAMP` 数据类型适用于存储本地时间，且无需考虑时区。

例如，您可以使用 `TIMESTAMP` 数据类型来存储本地时间，如当地剧院演出时间。其所处的时区无关紧要。

首先，创建一个表，名为 `events` ，用于存储当地活动，如戏剧演出。

```sql
CREATE TABLE events (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  location VARCHAR(255) NOT NULL,
  start_at TIMESTAMP NOT NULL,
  end_at TIMESTAMP NOT NULL
);
```

其次，向 `events` 表中插入一些行：

```sql
INSERT INTO
  events (title, location, start_at, end_at)
VALUES
  (
    '"O" by Cirque du Soleil',
    'O Theatre, Bellagio',
    '2024-12-08 19:00:00',
    '2024-12-08 21:30:00'
  ),
  (
    'David Copperfield',
    'David Copperfield Theater, MGM Grand',
    '2024-12-07 16:00:00',
    '2024-12-07 19:00:00'
  ),
  (
    'Awakening',
    'Awakening Theater, Wynn Las Vegas',
    '2024-12-06 19:00:00',
    '2024-12-06 21:00:00'
  ) 
RETURNING *;
```

输出：

```sql
 id |          title          |               location               |      start_at       |       end_at
----+-------------------------+--------------------------------------+---------------------+---------------------
  1 | "O" by Cirque du Soleil | O Theatre, Bellagio                  | 2024-12-08 19:00:00 | 2024-12-08 21:30:00
  2 | David Copperfield       | David Copperfield Theater, MGM Grand | 2024-12-07 16:00:00 | 2024-12-07 19:00:00
  3 | Awakening               | Awakening Theater, Wynn Las Vegas    | 2024-12-06 19:00:00 | 2024-12-06 21:00:00
```

第三，通过用结束时间减去开始时间来计算每个事件的持续时间：

```sql
SELECT
  title,
  end_at - start_at duration
FROM
  events;
```

输出:

```sql
          title          | duration
-------------------------+----------
 "O" by Cirque du Soleil | 02:30:00
 David Copperfield       | 03:00:00
 Awakening               | 02:00:00
```

# 总结

使用 `PostgreSQL` 的 `TIMESTAMP` 数据类型来存储不带时区的日期和时间值。

