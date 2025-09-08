**摘要**: 在本教程中，你将学习如何在 `SELECT` 语句中使用 `PostgreSQL` 的`ORDER BY` 子句，按升序或降序对行进行排序。

# `PostgreSQL ORDER BY` 子句简介

`SELECT` 语句默认返回的结果集中，行的顺序是未指定的。要对 `SELECT` 语句返回的行进行排序，可以使用 `ORDER BY` 子句。

以下是 `ORDER BY` 子句的语法：

```sql
SELECT
  column1,
  column2,
  ...
FROM
  table_name
ORDER BY
  sort_expression [ASC | DESC]; 
```

在以下语法中：

- 首先，在 `FROM` 子句中指定你想要从中检索数据的表名。
- 其次，在 `SELECT` 子句中提供要包含在结果集中的列。
- 第三，在 `ORDER BY` 子句中指定排序表达式。

`sort_expression` 可以是用于排序的列或表达式。

使用 `ASC` 按升序对行进行排序，使用 `DESC` 按降序对行进行排序。

`ASC` 或 `DESC` 选项为可选。如果未明确指定，`ORDER BY` 子句默认使用 `ASC`

`PostgreSQL` 按以下顺序计算 `SELECT` 语句中的子句：

1. `FROM` 
2. `SELECT`
3. `ORDER BY`

如果在 `SELECT` 子句中使用了列别名，你可以在 `ORDER BY` 子句中使用它们。原因是 `PostgreSQL` 会先计算 `SELECT` 子句，再计算 `ORDER BY` 子句。` SELECT` 子句中可用的列别名可以被 `ORDER BY` 子句访问。

# 创建示例表

我们将 [创建一个新表](../第1节-PostgreSQL入门/创建新表.md) ，`inventories` ，来练习 `ORDER BY` 子句：

创建库存表并向其中插入一些行的SQL脚本：

```sql
CREATE TABLE inventories (
    name VARCHAR(255) NOT NULL,
    price DEC(11,2) NOT NULL,
    quantity INT NOT NULL,
    color VARCHAR(50),
    updated_date DATE NOT NULL    
);

INSERT INTO inventories (name, price, quantity, color, updated_date) 

VALUES
('iPhone 15 Black', 999.99, 10, 'Black', '2024-12-01'),
('Galaxy S23', 899.99, 12, NULL, '2024-12-02'),
('Pixel 8', 799.99, 7, NULL, '2024-12-02'),
('iPhone 15 Pro', 1099.99, 6, 'Silver', '2024-12-03'),
('Galaxy S23 Ultra', 1199.99, 4, 'Black', '2024-12-03'),
('Pixel 8 Pro', 999.99, 9, 'Red', '2024-12-03'),
('iPhone 15 Pro Max', 1299.99, 3, 'Gold', '2024-12-04');
```

# 按一列对行进行排序

以下语句使用 `ORDER BY` 子句按价格从低到高对库存中的产品进行排序：

```sql
SELECT
  name,
  price
FROM
  inventories
ORDER BY
  price;
```

输出：

```sql
       name        |  price
-------------------+---------
 Pixel 8           |  799.99
 Galaxy S23        |  899.99
 Pixel 8 Pro       |  999.99
 iPhone 15 Black   |  999.99
 iPhone 15 Pro     | 1099.99
 Galaxy S23 Ultra  | 1199.99
 iPhone 15 Pro Max | 1299.99
```

要按价格降序对产品进行排序，请使用 `DESC` 选项：

```sql
SELECT
  name,
  price
FROM
  inventories
ORDER BY
  price DESC;
```

输出：

```sql
       name        |  price
-------------------+---------
 iPhone 15 Pro Max | 1299.99
 Galaxy S23 Ultra  | 1199.99
 iPhone 15 Pro     | 1099.99
 iPhone 15 Black   |  999.99
 Pixel 8 Pro       |  999.99
 Galaxy S23        |  899.99
 Pixel 8           |  799.99
```

# 按多列对行进行排序

有些产品的价格相同；你可以先按价格对产品进行排序，然后再按名称对排好序的产品列表进行排序。

以下查询使用 `ORDER BY` 子句，按 `price` 和 `name` 列中的值对产品进行排序：

```sql
SELECT
  name,
  price
FROM
  inventories
ORDER BY
  price DESC,
  name DESC;
```

输出：

```sql
       name        |  price
-------------------+---------
 iPhone 15 Pro Max | 1299.99
 Galaxy S23 Ultra  | 1199.99
 iPhone 15 Pro     | 1099.99
 Pixel 8 Pro       |  999.99
 iPhone 15 Black   |  999.99
 Galaxy S23        |  899.99
 Pixel 8           |  799.99
```

# 通过表达式对行进行排序

以下语句计算每个产品的库存数量，并按库存数量从高到低对产品进行排序：

```sql
SELECT
  name,
  price * quantity
FROM
  inventories
ORDER BY
  price * quantity DESC;
```

输出：

```sql
       name        | ?column?
-------------------+----------
 Galaxy S23        | 10799.88
 iPhone 15 Black   |  9999.90
 Pixel 8 Pro       |  8999.91
 iPhone 15 Pro     |  6599.94
 Pixel 8           |  5599.93
 Galaxy S23 Ultra  |  4799.96
 iPhone 15 Pro Max |  3899.97
```

由于 `PostgreSQL` 会先计算 `SELECT` 语句，再计算 `ORDER BY` 语句，因此您可以在 `SELECT` 和 `ORDER BY` 子句中使用列别名：

```sql
SELECT
  name,
  price * quantity AS amount
FROM
  inventories
ORDER BY
  amount DESC;
```

输出：

```sql
       name        |  amount
-------------------+----------
 Galaxy S23        | 10799.88
 iPhone 15 Black   |  9999.90
 Pixel 8 Pro       |  8999.91
 iPhone 15 Pro     |  6599.94
 Pixel 8           |  5599.93
 Galaxy S23 Ultra  |  4799.96
 iPhone 15 Pro Max |  3899.97
```

在这个示例中，我们将 `amount` 指定为表达式 `price * quantity` 的列别名，然后在 `ORDER BY` 子句中使用它进行排序。

# 按日期对行进行排序

以下语句使用 `ORDER BY` 子句，按更新日期从最早到最晚对 `inventory` 表中的行进行排序：

```sql
SELECT
  name,
  updated_date
FROM
  inventories
ORDER BY
  updated_date;
```

输出：

```sql
       name        | updated_date
-------------------+--------------
 iPhone 15 Black   | 2024-12-01
 Galaxy S23        | 2024-12-02
 Pixel 8           | 2024-12-02
 iPhone 15 Pro     | 2024-12-03
 Galaxy S23 Ultra  | 2024-12-03
 Pixel 8 Pro       | 2024-12-03
 iPhone 15 Pro Max | 2024-12-04
```

你可以使用 `DESC` 选项按更新日期从最新到最早对库存中的产品进行排序，方法如下：

```sql
SELECT
  name,
  updated_date
FROM
  inventories
ORDER BY
  updated_date DESC;
```

输出：

```sql
       name        | updated_date
-------------------+--------------
 iPhone 15 Pro Max | 2024-12-04
 iPhone 15 Pro     | 2024-12-03
 Galaxy S23 Ultra  | 2024-12-03
 Pixel 8 Pro       | 2024-12-03
 Galaxy S23        | 2024-12-02
 Pixel 8           | 2024-12-02
 iPhone 15 Black   | 2024-12-01
```

# `PostgreSQL ORDER BY`：处理排序中的 `NULL` 值

在 `PostgreSQL` 中，`NULL`  表示未知或缺失的数据。由于 `NULL` 是未知的，因此不能将其与任何其他值进行比较。

然而，`PostgreSQL` 需要知道哪些值在其他值之前或之后才能执行排序。在涉及` NULL` 时，`PostgreSQL` 在 `ORDER BY` 子句中提供了两种选项：

```sql
ORDER BY sort_expression NULLS FIRST;
ORDER BY sort_expression NULLS LAST;
```

在以下语法中：

- `NULLS FIRST` 会将 `NULLs` 放在其他非 `non-NULL` 值之前。
- `NULLS LAST` 将 `NULLs` 放在其他非空值之后。

> 请注意，在排序表达式与 `NULLS FIRST` 或 `NULLS LAST` 之间，您可以使用`ASC` 或 `DESC` 选项。

以下语句使用 `ORDER BY` 子句按颜色字母顺序对产品进行排序，并将 `NULLs` 放在首位：

```sql
SELECT
  name,
  color
FROM
  inventories
ORDER BY
  color NULLS FIRST;
```

输出：

```sql
       name        | color
-------------------+--------
 Galaxy S23        | NULL
 Pixel 8           | NULL
 iPhone 15 Black   | Black
 Galaxy S23 Ultra  | Black
 iPhone 15 Pro Max | Gold
 Pixel 8 Pro       | Red
 iPhone 15 Pro     | Silver
```

要将 `NULLs` 放在其他非 `NULL` 值之后，可以使用 `NULLS LAST` 选项：

```sql
SELECT
  name,
  color
FROM
  inventories
ORDER BY
  color NULLS LAST;
```

输出：

```sql
       name        | color
-------------------+--------
 Galaxy S23 Ultra  | Black
 iPhone 15 Black   | Black
 iPhone 15 Pro Max | Gold
 Pixel 8 Pro       | Red
 iPhone 15 Pro     | Silver
 Pixel 8           | NULL
 Galaxy S23        | NULL
```

# 总结

- 在 `SELECT` 语句中使用 `ORDER BY` 子句，可按一个或多个列对行进行排序。
- `PostgreSQL` 在 `SELECT` 子句之后对 `ORDER BY` 子句进行求值。
- `ASC` 选项将行按从低到高的顺序排列，而 `DESC` 选项则按从高到低的顺序排列。
- `NULLS FIRST` 将 `NULLs` 放在前面，`NULLS LAST` 将 `NULLs` 放在其他非 `NULL` 值之后。




