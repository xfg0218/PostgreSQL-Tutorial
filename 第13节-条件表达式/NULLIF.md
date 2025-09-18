**摘要**：在本教程中，您将学习如何使用 `PostgreSQL NULLIF` 函数来比较两个表达式，如果它们相等，则返回 `NULL` 。

# `PostgreSQL NULLIF` 函数入门

在 `PostgreSQL` 中，`NULLIF` 函数比较两个表达式，如果它们相等，则返回 `NULL` 。否则，它将返回第一个表达式的结果。

以下是 `NULLIF` 函数的语法：

```sql
NULLIF(expression1, expression2)
```

如果 `expression1` 等于 `expression2` ，则该函数返回 `NULL` 否则，它将返回 `expression1` .

请注意，`NULLIF` 函数使用相等运算符 （ `=` ） 比较两个表达式的结果。这意味着两个表达式的结果必须具有可比性。换句话说，它们必须具有兼容的数据类型才能进行比较。

# 基本 `PostgreSQL NULLIF` 函数示例

以下示例返回 `NULL` ，因为两个值相等：

```sql
SELECT NULLIF(0, 0) result;
```

输出:

```sql
 result
--------
   NULL
```

以下示例返回 1，因为 1 不等于 0：

```sql
SELECT NULLIF(1, 0) result;
```

输出:

```sql
 result
--------
      1
```

以下示例始终返回 `NULL`，因为 `NULL` 不等于 `NULL`

```sql
SELECT NULLIF(NULL, NULL) result;
```

输出：

```sql
 result
--------
 NULL
```

将字符串 `1` 与数字 `1` 进行比较时，以下示例返回：

```sql
SELECT NULLIF('1' , 1) result;
```

输出：

```sql
 result
--------
   NULL
```

它返回的结果与使用相等运算符比较 `1` 和 `1` 相同：

```sql
SELECT '1' = 1 result;
```

输出: 

```sql
 result
--------
 t
```

`1` 等于 `1`，因此 `1`= `1` 返回 `true`。`NULLIF( 1, 1)` 返回 `NULL`.

# 使用 `PostgreSQL NULLIF` 函数将值替换为 `NULL`

`NULLIF` 函数可以帮助将值替换为 `NULL` 特别是对于数据清理。

在应用程序中，用户可以在可为 `null` 的列中输入空字符串 （``） 或 `N/A` 。

要解决此问题，您需要在应用程序层验证数据，并在数据库级别清理数据;您可以使用 `NULLIF` 函数将这些无效值替换为 `NULL`

假设您有以下 `contacts` 表，用于存储联系人 `ID` 、姓名、电话和电子邮件：

用于创建联系人表并将数据插入表的 SQL 脚本

```sql
CREATE TABLE contacts(
    contact_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    phone VARCHAR(25),
    email VARCHAR(255)
);
INSERT INTO contacts (name, phone, email) 
VALUES
('Alice Johnson', 'N/A', ''),
('Bob Smith', '123-456-789', 'bob.smith@pgtutorial.com'),
('Carol White', '123-456-788', 'carol.white@pgtutorial.com'),
('David Brown', '123-456-787', 'david.brown@pgtutorial.com' ),
('Eve Black', '123-456-786', '')
RETURNING *;
```

| contact_id | name | phone | email |
|:----:|:----:|:----:|:----:|
| 1 | Alice Johnson | N/A |  | `` |
| 2 | Bob Smith  | 123-456-789 | bob.smith@pgtutorial.com |
| 3 | Carol White| 123-456-788 | carol.white@pgtutorial.com |
| 4 | David Brown | 123-456-787 | david.brown@pgtutorial.com |
| 5 | Eve Black | 123-456-786| `` |

我们可以使用 `UPDATE` 语句和 `NULLIF `函数来更新电话和电子邮件列中的无效值，如下所示：

```sql
UPDATE contacts
SET
  phone = NULLIF(phone, 'N/A'),
  email = NULLIF(email, '') 
RETURNING *;
```

输出：

```sql
 contact_id |     name      |    phone    |           email
------------+---------------+-------------+----------------------------
          1 | Alice Johnson | NULL        | NULL
          2 | Bob Smith     | 123-456-789 | bob.smith@pgtutorial.com
          3 | Carol White   | 123-456-788 | carol.white@pgtutorial.com
          4 | David Brown   | 123-456-787 | david.brown@pgtutorial.com
          5 | Eve Black     | 123-456-786 | NULL
```

# 防止除以零错误

以下语句从 `products` 表中检索安全库存，将其与 `inventories` 表中的库存数量联接，并计算库存覆盖率：

```sql
WITH
  stocks AS (
    SELECT
      product_name,
      safety_stock,
      SUM(quantity) inventory_qty
    FROM
      products
      INNER JOIN inventories USING (product_id)
    GROUP BY
      product_name,
      safety_stock
  )
SELECT
  product_name,
  inventory_qty / safety_stock stock_coverage
FROM
  stocks
ORDER BY
  product_name;
```

输出：

```sql
division by zero
```

输出显示除以零误差。

原因是有些产品的安全库存为 `0` 。要防止除以零错误，如果安全库存为零，则可以使用 `NULLIF` 语句返回 `NULL`：

```sql
WITH
  stocks AS (
    SELECT
      product_name,
      NULLIF(safety_stock, 0) safety_stock,
      SUM(quantity) inventory_qty
    FROM
      products
      INNER JOIN inventories USING (product_id)
    GROUP BY
      product_name,
      safety_stock
  )
SELECT
  product_name,
  inventory_qty,
  safety_stock,
  inventory_qty / safety_stock stock_coverage
FROM
  stocks
ORDER BY
  product_name;
```

部分输出：

```sql
        product_name        | inventory_qty | safety_stock | stock_coverage
----------------------------+---------------+--------------+----------------
 Apple AirPods Pro 3        |           180 |           45 |              4
 Apple iMac 24"             |           320 |         NULL |           NULL
 Apple iPad Pro 12.9        |           170 |           15 |             11
 Apple iPhone 15            |           150 |           20 |              7
 Apple iPhone 15 Pro Max    |           140 |           50 |              2
 Apple Watch Series 9       |           200 |           20 |             10
 Bose SoundLink Max         |           270 |         NULL |           NULL
...
```

在此示例中，如果安全库存为 `0` ，则 `NULLIF` 语句返回 `NULL` 否则，它将返回安全库存。将清单除以 `NULL` 将导致 `NULL` 而不是错误。

# 总结

- 如果两个参数相等，则使用 `PostgreSQL NULLIF` 函数返回 `NULL` ，否则返回第一个参数。
- 使用 `NULLIF` 函数将值替换为 `NULL`，或通过将 `0` 替换为 `NULL` 来防止除以零。




