**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `ANY` 运算符来将一个值与子查询返回的一组值进行比较。

# `PostgreSQL ANY` 运算符入门

在 `PostgreSQL` 中，`ANY` 运算符允许你将一个值与子查询返回的一组值进行比较。

以下是 `ANY` 运算符的语法：

```sql
value operator ANY(subquery)
```

在此语法中：

- 首先，指定一个 `value` ，可以是要比较的列或表达式。
- 其次，提供一个比较运算符（`=`、`<`、`>`、`<=`、`>=`和`!=`），用于将`value`与一组值进行比较。
- 第三，在括号 `()` 中定义一个子查询，后跟 `ANY` 运算符。该子查询需要返回一个值列表，用于与 `value` 进行比较。换句话说，它应该返回一个由单列组成的结果集。

如果比较结果对于集合中的至少一个值返回 `true`，则 `ANY` 运算符返回 `true`。如果所有比较结果均为 `false` ，则 `ANY` 运算符返回 `false`。

如果子查询没有返回任何行，`ANY` 运算符总是返回 `true`。

在 `PostgreSQL` 中，`SOME` 和 `ANY` 是同义词，因此可以互换使用。

# `PostgreSQL ANY` 运算符示例

假设我们有一个 `products` 表，其中包含 `id` 、`name`、`price` 和 `brand` ：

创建产品表并向其中插入数据的SQL脚本

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  price DEC(11, 2) NOT NULL CHECK (price > 0),
  brand VARCHAR(50) NOT NULL
);
INSERT INTO
  products (name, price, brand)
VALUES
  ('Galaxy S24', 799.99, 'Samsung'),
  ('iPhone 16', 1099.99, 'Apple'),
  ('iPhone 16 Pro Max', 1399.99, 'Apple'),
  ('iPhone 16 Plus', 1199.99, 'Apple'),
  ('Galaxy S24 Ultra', 1299.99, 'Samsung'),
  ('Galaxy S24 Plus', 1119.99, 'Samsung');
```

| id | name                | price    | brand   |
|:----:|:-----:|:----:|:----:|
| 1  | Galaxy S24          | 799.99   | Samsung |
| 2  | iPhone 16           | 1099.99  | Apple   |
| 3  | iPhone 16 Pro Max   | 1399.99  | Apple   |
| 4  | iPhone 16 Plus      | 1199.99  | Apple   |
| 5  | Galaxy S24 Ultra    | 1299.99  | Samsung |
| 6  | Galaxy S24 Plus     | 1119.99  | Samsung |

以下示例使用 `ANY` 运算符查找所有价格高于任何 `Apple` 产品价格的产品：

```sql
SELECT 
  name,
  brand,
  price
FROM
  products
WHERE
  price > ANY (
    SELECT
      price
    FROM
      products
    WHERE
      brand = 'Apple'
  );
```

输出：

```sql
       name        |  brand  |  price
-------------------+---------+---------
 iPhone 16 Pro Max | Apple   | 1399.99
 iPhone 16 Plus    | Apple   | 1199.99
 Galaxy S24 Ultra  | Samsung | 1299.99
 Galaxy S24 Plus   | Samsung | 1119.99
```

其工作原理。

首先，子查询返回所有 `Apple` 产品的价格：

```sql
  price
---------
 1099.99
 1399.99
 1199.99
```

其次，`WHERE` 子句使用大于号（ `>` ）将外部查询中每个产品的价格与子查询返回的价格集合进行比较。

如果价格高于任何 `Apple` 产品的价格，它将返回 `true` 。在这种情况下，WHERE子句会将该产品包含在结果集中。

# 重写 `ANY` 运算符

你可以使用 `MIN` 聚合函数重写上述查询，以查找价格高于任何 `Apple` 产品的产品：

```sql
SELECT
  name,
  brand,
  price
FROM
  products
WHERE
  price > (
    SELECT
      MIN(price)
    FROM
      products
    WHERE
      brand = 'Apple'
  );
```

首先，子查询返回了所有苹果产品的最低价格：

```sql
SELECT
  MIN(price)
FROM
  products
WHERE
  brand = 'Apple';
```

输出：

```sql
   min
---------
 1099.99
```

其次，外部查询返回的产品价格高于所有苹果产品的最低价格。

# 总结

使用 `ANY` 运算符将一个值与子查询返回的一组值进行比较。