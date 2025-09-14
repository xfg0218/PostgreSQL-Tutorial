**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `DECIMAL` 数据类型在数据库中存储精确的数值数据。

# `PostgreSQL DECIMAL` 数据类型入门

`PostgreSQL` 的 `DECIMAL` 是一种数值数据类型，它允许你存储具有精度的精确数值。

`DECIMAL` 数据类型具有精确性和准确性，适用于存储金融金额以及其他对精度要求极高的数字。

以下是定义 `DECIMAL` 列的语法：

```sql
CREATE TABLE table_name(
     column_name DECIMAL(p,s),
     ...
);
```

`DEC` 和 `NUMERIC` 是 `DECIMAL` 的同义词，因此可以互换使用。例如：

```sql
CREATE TABLE table_name(
     column_name DEC(p,s),
     ...
);
```

在此语法中：

- `p` 代表精度。精度是指有效数字的数量，包括整数部分和小数部分。精度必须在 `1` 到 `1000` 之间。
- `s` 代表小数位数。小数位数是小数点右侧的数字个数。小数位数的有效范围是从 `-1000` 到 `1000` 。关于负小数位数，我们很快会详细介绍。

例如，`DEC(11,2)` 列可以存储一个 `11` 位的数字，包括小数点后的两位。

如果省略小数位数，它将默认为零：

```sql
DEC(p)
```

它等同于：

```sql
DEC(p,0)
```

然而，如果同时忽略精度和小数位数，它们将默认采用上述限制。

```sql
DEC
```

如果不使用精度和小数位数限制，`PostgreSQL` 最多可以存储 `131072` 位整数部分和 `16383` 位小数部分。

# `PostgreSQL` 十进制数据类型示例

首先，创建一个表，名为 `inventories` ：

```sql
CREATE TABLE inventories (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  product_name VARCHAR(255) NOT NULL,
  quantity INT NOT NULL CHECK (quantity >= 0),
  price DEC(11, 2) NOT NULL CHECK (price > 0),
  brand VARCHAR(255) NOT NULL
);
```

其次，向 `inventories` 表中插入一些行：

```sql
INSERT INTO
  inventories (product_name, quantity, price, brand)
VALUES
  ('iPhone 16 Pro', 50, 999.99, 'Apple'),
  ('iPhone 16 Pro Max', 40, 1099.99, 'Apple'),
  ('iPhone 16 Plus', 55, 899.99, 'Apple'),
  ('Galaxy S24 Ultra', 45, 1199.99, 'Samsung'),
  ('Galaxy S24 Plus', 50, 999.99, 'Samsung'),
  ('Galaxy Z Fold 6', 30, 1799.99, 'Samsung'),
  ('Galaxy Z Flip 6', 35, 999.99, 'Samsung'),
  ('Pixel 9', 50, 799.99, 'Google'),
  ('Pixel 9 Pro', 40, 999.99, 'Google'),
  ('Pixel 9 Pro Fold', 25, 1799.99, 'Google')
RETURNING *;
```

输出：

```sql
 id |   product_name    | quantity |  price  |  brand
----+-------------------+----------+---------+---------
  1 | iPhone 16 Pro     |       50 |  999.99 | Apple
  2 | iPhone 16 Pro Max |       40 | 1099.99 | Apple
  3 | iPhone 16 Plus    |       55 |  899.99 | Apple
  4 | Galaxy S24 Ultra  |       45 | 1199.99 | Samsung
  5 | Galaxy S24 Plus   |       50 |  999.99 | Samsung
  6 | Galaxy Z Fold 6   |       30 | 1799.99 | Samsung
  7 | Galaxy Z Flip 6   |       35 |  999.99 | Samsung
  8 | Pixel 9           |       50 |  799.99 | Google
  9 | Pixel 9 Pro       |       40 |  999.99 | Google
 10 | Pixel 9 Pro Fold  |       25 | 1799.99 | Google
```

第三，计算每个品牌的总库存价值：

```sql
SELECT
  brand,
  SUM(quantity * price)
FROM
  inventories
GROUP BY
  brand;
```

输出：

```sql
  brand  |    sum
---------+-----------
 Google  | 124998.85
 Apple   | 143498.55
 Samsung | 192998.40
```

# Negative scales

从 `PostgreSQL 15` 开始，您可以声明一个带有负小数位数的十进制列：

```sql
DEC(p,-s)
```

在这种语法中，`PostgreSQL` 会将数字四舍五入到小数点左侧。精度 `（p）` 表示未被四舍五入的最大位数。

例如：

首先，创建一个名为 `items` 的表，其中包含一个具有负小数位数的 `DEC` 列：

```sql
CREATE TABLE items (
  item_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  value DEC(5, -2) NOT NULL
);
```

其次，向 `items` 表中插入两行：

```sql
INSERT INTO
  items (value)
VALUES
  (12345),
  (67890) 
RETURNING *;
```

输出：

```sql
 item_id | value
---------+-------
       1 | 12300
       2 | 67900
```

由于 `value` 列的精度为 `-2` ，`PostgreSQL` 会将它们四舍五入到最接近的百位数，有效数字总数为5位。

# Rounding

`PostgreSQL` 对十进制值采用四舍五入时向远离零的方向进位的方式。

例如，如果你将2.5四舍五入到最接近的整数，`PostgreSQL` 会将其舍入为3。如果你将-2.5四舍五入到最接近的整数，`PostgreSQL` 会将其舍入为 -3。

以下示例展示了如何使用 `ROUND` 函数将平均库存值四舍五入为小数点后两位的数字：

```sql
SELECT
  brand,
  AVG(quantity * price),
  ROUND(AVG(quantity * price), 2)
FROM
  inventories
GROUP BY
  brand;
```

输出：

```sql
  brand  |        avg         |  round
---------+--------------------+----------
 Google  | 41666.283333333333 | 41666.28
 Apple   | 47832.850000000000 | 47832.85
 Samsung | 48249.600000000000 | 48249.60
```

# Special values

除了常规值外，`decimal` 类型还支持 `IEEE 754` 标准中规定的几个特殊值：

- `Infinity` ——表示一个无限大的值。
- `-Infinity` ——表示一个无限小的值。
- `NaN`（不是数字）——表示一个非数字值，由无效计算产生。

在 `SQL` 语句中使用这些值时，必须将它们放在单引号中，例如 `Infinity`、`-Infinity` 和 `NaN`。

# 总结

- 使用 `PostgreSQL` 的 `DECIMAL` 数据类型来存储需要精确性的数值。