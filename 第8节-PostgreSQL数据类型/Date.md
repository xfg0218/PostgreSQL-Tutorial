**摘要**：在本教程中，你将了解 `PostgreSQL` 的 `DATE` 数据类型，以及如何使用它在表中存储日期数据。

# `PostgreSQL DATE` 数据类型入门

在 `PostgreSQL` 中，您使用 `DATE` 数据类型在表中存储不带时间的日期。以下是定义 `DATE` 列的语法：

```sql
CREATE TABLE table_name (
    column_name DATE,
    ...
);
```

日期列可以存储公元前 `4713` 年到公元 `5874897` 年之间的日期。`PostgreSQL` 使用 `4` 字节来存储日期值。

要为 `DATE` 列设置默认值，需使用 `DEFAULT` 约束：

```sql
CREATE TABLE table_name (
    column_name DATE DEFAULT default_date
);
```

如果您想将当前日期设置为默认值，可以按如下方式使用 `CURRENT_DATE` 函数：

```sql
CREATE TABLE table_name (
    column_name DATE DEFAULT CURRENT_DATE
);
```

# Date Input

`PostgreSQL` 接受多种日期格式，但建议使用 `ISO 8601` 格式，即日期输入采用 `yyyy-mm-dd` 。例如，`2024-12-31` 表示 `December 31` , `2024` 。

# Date Output

`PostgreSQL` 允许你设置各种日期输出格式，就像日期输入格式一样。默认格式是 `ISO 8601` 格式。

# `PostgreSQL DATE` 数据类型示例

以下示例创建了一个名为 `stock_adjustments` 的表，其中包含一个 `DATE` 列：

```sql
CREATE TABLE stock_adjustments (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  product_name VARCHAR(255) NOT NULL,
  quantity INT NOT NULL,
  adjustment_date DATE NOT NULL DEFAULT CURRENT_DATE
);
```

`stock_adjustments` 表有一个 `adjustment_date` 列，用于表示调整日期。其值默认为当前日期。

如果在插入数据时不提供日期，`PostgreSQL` 会将当前日期插入到调整日期列中。

# 插入默认日期值 

以下语句将在 `stock_adjustments` 表中插入一个新行：

```sql
INSERT INTO
  stock_adjustments (product_name, quantity)
VALUES
  ('iPhone 16 Pro', 5) 
RETURNING *;
```

输出：

```sql
 id | product_name  | quantity | adjustment_date
----+---------------+----------+-----------------
  1 | iPhone 16 Pro |        5 | 2024-12-08
```

在这个 `INSERT` 语句中，我们没有为调整日期列提供值；它默认是我们运行该语句当天的日期。

日期列的输出采用 `ISO 8601` 格式，即 `yyyy-mm-dd` 。调整日期为 `2024-12-08` ，表示 `December 08` , `2024` 。

# 插入日期值

以下示例向 `stock_adjustments` 表中插入一行包含输入日期值的数据：

```sql
INSERT INTO
  stock_adjustments (product_name, quantity, adjustment_date)
VALUES
  ('Galaxy S23 Ultra', 2, '2024-12-09') 
RETURNING *;
```

输出：

```sql
 id |   product_name   | quantity | adjustment_date
----+------------------+----------+-----------------
  2 | Galaxy S23 Ultra |        2 | 2024-12-09
```

在这个示例中，我们明确使用了 `ISO 8601` 格式的日期输入：`2024-12-09` ，它表示 `December 09` , `2024` 。

# 从日期中提取日、月、年

`extract()` 函数从日期值中提取一个字段，该字段可以是日、月和年。

例如，以下语句使用 `extract()` 函数从调整日期列中提取日、月和年：

```sql
SELECT
  product_name,
  quantity,
  adjustment_date,
  extract(day from  adjustment_date) d,
  extract(month from adjustment_date) m,
  extract(year from adjustment_date) y
FROM
  stock_adjustments
ORDER BY
  adjustment_date;
```

输出：

```sql
   product_name   | quantity | adjustment_date | d | m  |  y
------------------+----------+-----------------+---+----+------
 iPhone 16 Pro    |        5 | 2024-12-08      | 8 | 12 | 2024
 Galaxy S23 Ultra |        2 | 2024-12-09      | 9 | 12 | 2024
```

# 格式化日期值

要将日期值格式化为特定格式，需使用 `to_char()` 函数。

例如，以下语句会检索库存调整数据，并将日期格式化为 `Mon dd, yyyy` 格式：

```sql
SELECT
  product_name,
  quantity,
  adjustment_date,
  to_char (adjustment_date, 'Month dd, yyyy') formatted_date
FROM
  stock_adjustments
ORDER BY
  adjustment_date;
```

输出：

```sql
   product_name   | quantity | adjustment_date |   formatted_date
------------------+----------+-----------------+--------------------
 iPhone 16 Pro    |        5 | 2024-12-08      | December  08, 2024
 Galaxy S23 Ultra |        2 | 2024-12-09      | December  09, 2024
```

# 为日期增加或减少天数

要向日期添加一天，需使用 `+` 运算符：

```sql
date + days
```

同样，要从日期中减去天数，可使用 `-` 运算符：

```sql
date - days
```

例如，以下语句将调整日期增加3天：

```sql
SELECT
  product_name,
  quantity,
  adjustment_date,
  adjustment_date + 3 approval_date
FROM
  stock_adjustments
ORDER BY
  approval_date;
```

输出：

```sql
   product_name   | quantity | adjustment_date | approval_date
------------------+----------+-----------------+---------------
 iPhone 16 Pro    |        5 | 2024-12-08      | 2024-12-11
 Galaxy S23 Ultra |        2 | 2024-12-09      | 2024-12-12
```

# 总结

- 使用 `PostgreSQL` 的 `DATE` 类型在表中存储日期。
- 使用 `extract()` 函数从日期中提取日、月和年。 
- 使用 `to_char()` 函数来格式化日期。
- 使用 `+` 运算符向日期添加若干天。 
- 使用 `-` 运算符从日期中减去若干天。
