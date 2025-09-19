**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `INTERVAL` 数据类型来处理间隔值。

# `PostgreSQL INTERVAL` 数据类型概述

在 `PostgreSQL` 中，间隔是时间持续时长，例如2天、3小时、1年6个月。

您可以使用 `INTERVAL` 数据类型来存储时间间隔。该时间间隔类型可以存储年、月、时、分、秒和毫秒的间隔。

以下是定义文字值的语法：

```sql
INTERVAL 'value unit'
```

在以下语法中：

- `value` 是一个表示持续时间的数字。
- `unit` 是时间单位，可以是年、月、小时、分钟、秒、毫秒、微秒、十年、百年和千年。

一个时间间隔可以包含多个单位，例如 `1 days 2 hours` 。

# `PostgreSQL INTERVAL` 数据类型示例

以下语句展示了如何创建一个2天的间隔：

```sql
SELECT
  INTERVAL '2 days';
```

输出：

```sql
 interval
----------
 2 days
```

以下语句展示了如何在单个间隔中组合多个单位：

```sql
SELECT
  INTERVAL '2 days 5 hours 30 minutes';
```

输出：

```sql
    interval
-----------------
 2 days 05:30:00
```

如果你使用十进制值，`PostgreSQL` 会将其转换为相应的单位：

```sql
SELECT
  INTERVAL '1.5 hours 30 minutes';
```

输出：

```sql
 interval
----------
 02:00:00
```

在这个示例中，`PostgreSQL` 将1.5小时转换为1小时30分钟，再加上30分钟，结果为2小时。

以下示例使用十年单位表示时间间隔：

```sql
SELECT
  INTERVAL '1 decade';
```

输出：

```sql
 interval
----------
 10 years
```

# 创建带有 `Interval` 列的表

首先，创建一个表，该表包含一个 `INTERVAL` 列：

```sql
CREATE TABLE subscription_plans (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  duration INTERVAL NOT NULL
);
```

其次，向 `subscription_plans` 表中插入一些行：

```sql
INSERT INTO
  subscription_plans (name, duration)
VALUES
  ('6 months', INTERVAL '6 months'),
  ('1 year', INTERVAL '1 year'),
  ('3 years', INTERVAL '3 years')
RETURNING
  *;
```

输出：

```sql
 id |   name   | duration
----+----------+----------
  1 | 6 months | 6 mons
  2 | 1 year   | 1 year
  3 | 3 years  | 3 years
```

第三，检索期限为1年及以上的计划：

```sql
SELECT * FROM subscription_plans
WHERE duration >= INTERVAL '1 year';
```

输出：

```sql
 id |  name   | duration
----+---------+----------
  2 | 1 year  | 1 year
  3 | 3 years | 3 years
```

# 使用 `Intervals` 进行计算

你可以对时间间隔进行加、减、乘、除运算。例如，以下操作会在当前时间戳上增加7天：

```sql
SELECT
  NOW() + INTERVAL '7 days' next_week;
```

以下示例从当前时间戳中减去3天：

```sql
SELECT
  NOW() - INTERVAL '3 days' three_days_ago;
```

要将时间戳相乘，需使用 `*` 运算符：

```sql
SELECT INTERVAL '1 hour' * 24 result;
```

输出：

```sql
  result
----------
 24:00:00
```

并且您可以使用除法运算符（ `/` ）来对间隔进行除法运算：

```sql
SELECT
  INTERVAL '1 day' / 24 result;
```

输出：

```sql
  result
----------
 01:00:00
```

# 从 `Intervals` 中提取组件

要提取间隔字段，需使用 `EXTRACT` 函数。例如，以下语句从间隔中提取天数：

```sql
SELECT
  EXTRACT(
    DAY
    FROM
      INTERVAL '2 days 5 hours'
  );
```

输出：

```sql
 extract
---------
       2
```

# 调整 `Intervals`

`PostgreSQL` 提供了三个用于调整间隔的函数：

- `JUSTIFY_HOURS` ：将多余的小时调整为天。
- `JUSTIFY_DAYS`：将多余的天数调整为月数，同时精确处理闰年。
- `JUSTIFY_INTERVAL` ：将月调整为年，将日调整为月。

例如，以下语句使用 `JUSTIFY_HOURS` 来调整一个时间间隔：

```sql
SELECT JUSTIFY_HOURS(INTERVAL '26 hours');
```

输出：

```sql
 justify_hours
----------------
 1 day 02:00:00
```

以下语句使用 `JUSTIFY_DAYS` 将多余的天数调整为月数：

```sql
SELECT
  JUSTIFY_DAYS(INTERVAL '75 days');
```

输出：

```sql
  justify_days
----------------
 2 mons 15 days
```

以下示例使用 `JUSTIFY_INTERVAL` 将多余的月份调整为年，将多余的天数调整为月：

```sql
SELECT
  JUSTIFY_INTERVAL(INTERVAL '13 months 30 days');
```

输出：

```sql
 justify_interval
------------------
 1 year 2 mons
```

# 总结

- 使用 `INTERVAL 'value unit'` 来创建一个间隔。
- 使用转换运算符（ `::` ）将字符串转换为间隔。
- 使用 `EXTRACT` 函数从间隔中提取一个组成部分。
- 使用 `JUSTIFY_HOURS` 、 `JUSTIFY_DAYS` 和 `JUSTIFY_INTERVAL` 函数来调整间隔。
- 使用运算符（ `+` 、 `-` 、 `*` 、 `/` ）执行区间算术运算。

