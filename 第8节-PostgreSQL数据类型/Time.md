**摘要**：在本教程中，你将了解 `PostgreSQL` 的 `TIME` 数据类型以及如何使用它在数据库中存储时间数据。

# `PostgreSQL TIME` 数据类型入门

在 `PostgreSQL` 中，您可以使用 `TIME` 数据类型在表中存储时间数据（不包含日期）。

以下是定义 `TIME` 列的语法：

```sql
CREATE TABLE table_name (
    column_name TIME,
    ...
);
```

时间列可以存储从 `00:00:00` 到 `24:00:00` 的时间值。`PostgreSQL` 使用8字节来存储时间值。

要为 `TIME` 列设置默认值，需使用 `DEFAULT` 约束： 

```sql
CREATE TABLE table_name(
    column_name TIME DEFAULT default_time,
    ...
);
```

如果您想将当前时间设置为默认值，可以按如下方式使用 `CURRENT_TIME` 函数：

```sql
CREATE TABLE table_name(
    column_name TIME DEFAULT CURRENT_TIME,
    ...
);
```

# Time Input

`PostgreSQL` 接受多种时间格式，为你提供了灵活性。不过，建议使用 `ISO 8601` 格式，其时间输入格式为`HH:MM:SS` ，例如 `09:30:45` 。

# Time Output

`PostgreSQL` 允许你设置各种时间输出格式，就像时间输入格式一样。默认格式是 `ISO 8601` 格式。

# 带精度的时间

`PostgreSQL` 允许您存储带有小数秒精度的时间，最多可达6位。

以下语句定义了一个具有小数秒精度的 `TIME` 列：

```sql
CREATE TABLE table_name (
    column_name TIME(precision)
);
```

精度的有效值为1到6。

要使用具有小数秒精度的时间值，请使用以下格式：

```sql
HH:MI:SS.pppppp
```

在该语法中，`p` 决定精度。例如， `09:30:45.123456`。

# 获取当前时间

要获取当前时间，需使用 `curent_time` 函数：

```sql
SELECT current_time;
```

输出：

```sql
    current_time
--------------------
 11:42:54.539691-07
```

`current_time` 返回 `PostgreSQL` 运行所在时区的时间以及秒的小数部分精度。

要去除小数秒精度，请将精度设为零并传递给 `current_time` 函数：

```sql
SELECT current_time(0);
```

输出：

```sql
 current_time
--------------
 11:43:07-07
```

要获取当前的本地时间，可使用 `localtime` 函数：

```sql
SELECT localtime;
```

输出:

```sql
    localtime
-----------------
 11:43:18.576448
```

不含小数秒时：

```sql
SELECT localtime(0);
```

输出：

```sql
 localtime
-----------
 11:43:32
```

# `PostgreSQL TIME` 数据类型示例

以下示例创建了一个名为 `schedules` 的表，其中包含一个 `TIME` 列：

```sql
CREATE TABLE schedules (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  employee VARCHAR(100) NOT NULL,
  start_at TIME NOT NULL,
  end_at TIME NOT NULL
);
```

时间表有两个 `TIME` 列，分别是开始 `（start）` 和结束 `（end）` ，用于表示日程的开始时间和结束时间。

# 插入时间值

以下示例将带有输入时间值的新行插入到 `schedules` 表中：

```sql
INSERT INTO
  schedules (employee, start_at, end_at)
VALUES
  ('John Doe', '08:00:00', '12:00:00'),
  ('John Doe', '13:00:00', '17:00:00') 
RETURNING *;
```

输出：

```sql
 id | employee | start_at |  end_at
----+----------+----------+----------
  1 | John Doe | 08:00:00 | 12:00:00
  2 | John Doe | 13:00:00 | 17:00:00
```

