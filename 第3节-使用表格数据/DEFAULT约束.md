**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `DEFAULT` 约束为表列设置默认值。

# `PostgreSQL DEFAULT` 约束简介

在 `PostgreSQL` 中，表的列默认值为 `NULL` 。`PostgreSQL` 允许您使用灵活的 `DEFAULT` 约束为列指定默认值，如下所示：

```sql
CREATE TABLE table_name(
   column1 data_type DEFAULT default_value,
   column2 data_type,
);
```

在这种语法中，你需要在列的数据类型之后指定 `DEFAULT` 关键字，后跟该列的默认值：

```sql
column1 data_type DEFAULT default_value
```

当你在不提供 `column1` 的值的情况下插入一行时，`PostgreSQL` 会使用`default_value` 进行插入：

```sql
INSERT INTO
  table_name (column2)
VALUES
  (value2);
```

你也可以使用 `DEFAULT` 关键字来便捷地表示在 `column1` 中定义的默认值：

```sql
INSERT INTO
  table_name (column1, column2)
VALUES
  (DEFAULT, value2);
```

# `PostgreSQL DEFAULT` 约束基本示例

首先，[创建表](../第1节-PostgreSQL入门/创建新表.md) `items` ，其 `tax`  列的默认值为 `5%（或 0.05）`：

```sql
CREATE TABLE items (
  item_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  quantity INT NOT NULL,
  price DEC(11, 2) NOT NULL,
  tax DEC(11, 2) DEFAULT 0.05
);
```

其次，向 `items` 表中插入新行，且不为 `tax` 列提供值：

```sql
INSERT INTO
  items (name, quantity, price)
VALUES
  ('iPhone 15 Pro', 1, 1299.99) RETURNING *;
```

输出：

```sql
 item_id |     name      | quantity |  price  | tax
---------+---------------+----------+---------+------
       1 | iPhone 15 Pro |        1 | 1299.99 | 0.05
```

`PostgreSQL` 使用默认值 `0.05` 插入到 `tax` 列中。

第三，向 `items` 表中插入一行新数据，并使用 `DEFAULT` 关键字进行插入：

```sql
INSERT INTO
  items (name, quantity, price, tax)
VALUES
  ('iPhone 16 Pro', 1, 1399.99, DEFAULT) 
RETURNING *;
```

输出：

```sql
 item_id |     name      | quantity |  price  | tax
---------+---------------+----------+---------+------
       2 | iPhone 16 Pro |        1 | 1399.99 | 0.05
```


由于我们在 `INSERT` 语句中使用了 `DEFAULT` 值，`PostgreSQL` 会使用 `tax` 列中定义的默认值进行插入。

最后，将一个新行插入到 `items` 表中，其值为 `tax` 列的值：

```sql
INSERT INTO
  items (name, quantity, price, tax)
VALUES
  ('iPhone 17 Pro', 1, 1499.99, 0.08) 
RETURNING *;
```

输出：

```sql
 item_id |     name      | quantity |  price  | tax
---------+---------------+----------+---------+------
       3 | iPhone 17 Pro |        1 | 1499.99 | 0.08
```

在这个示例中，`PostgreSQL` 使用提供的值 `(0.08)`而非默认值。

# 为时间戳列设置默认值

在 `PostgreSQL` 中，`TIMESTAMP` 数据类型用于存储日期和时间值。如果希望 ` TIMESTAMP` 列具有默认值，可以将 `DEFAULT` 约束与 `CURRENT_TIMESTAMP` 函数一起使用。例如：

首先，创建一个名为 `orders` 的新表来存储客户订单：

```sql
CREATE TABLE orders (
  order_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  customer VARCHAR(100) NOT NULL,
  ship_to VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

`created_at` 列使用 `CURRENT_TIMESTAMP ` 作为默认值。

其次，向 `orders` 表中插入一个新订单：

```sql
INSERT INTO
  orders (customer, ship_to)
VALUES
  (
    'John Doe',
    '9000 N 1st Street, San Jose, CA 95134'
  ) 
RETURNING *;
```

输出：

```sql
 order_id | customer |                ship_to                |         created_at
----------+----------+---------------------------------------+----------------------------
        1 | John Doe | 9000 N 1st Street, San Jose, CA 95134 | 2024-11-22 12:22:22.724668
```

在这个示例中，我们没有为 `created_at` 列提供值。因此，`PostgreSQL` 会使用当前时间戳进行插入。

# 总结

- 使用 `PostgreSQL` 的 `DEFAULT` 约束为列设置默认值。
- 使用 `DEFAULT` 关键字表示具有默认约束的列的默认值。


