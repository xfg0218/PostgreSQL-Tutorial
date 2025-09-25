**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 数组类型将数组存储在数据库表中。

# `PostgreSQL ARRAY` 数据类型概述

在 `PostgreSQL` 中，您可以使用 `Array` 类型将相同类型的多个值存储在单个列中。

对于每种数据类型，包括用户定义的类型，`PostgreSQL` 都会隐式创建相应的 `Array` 数据类型。

例如，`INT` 类型具有 `INT[]` 数组类型，`VARCHAR` 具有 `VARCHAR[]` 数组类型，等等。

以下是定义具有数组数据类型的列的语法：

```sql
column_name datatype[]
```

在此语法中，在数据类型后使用方括号 [] 来定义特定数据类型的数组

例如，以下语句创建一个名为 `products` 的表来存储产品信息：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  features TEXT[]
);
```

在 `products` 表中，`features` 列的数据类型为一维数组，可以存储多个文本值。

数组可以是一维的，也可以是多维的。括号方块的数量决定了数组的维数。

例如，下面显示了如何定义具有两个维度的列：

```sql
column_name datatype [][]
```

# 将数据插入数组列 

要构造数组的值，您可以使用大括号 `{}` 或 `ARRAY` 构造函数。

以下语句使用 `ARRAY` 构造函数在 `products` 表中插入新行：

```sql
INSERT INTO
  products (name, price, features)
VALUES
  (
    'Smartphone X1',
    699.99,
    ARRAY['5G', 'OLED Display', '512GB Storage']
  )
RETURNING
  name,
  price,
  features;
```

输出：

```sql
     name      | price  |              features
---------------+--------+-------------------------------------
 Smartphone X1 | 699.99 | {5G,"OLED Display","512GB Storage"}
```

以下 `INSERT` 语句使用大括号将新产品插入 `products` 表：

```sql
INSERT INTO
  products (name, price, features)
VALUES
  (
    'Laptop Pro 15',
    1299.99,
    '{"Touchscreen", "32GB RAM", "1024GB SSD"}'
  )
RETURNING
  name,
  price,
  features;
```

输出：

```sql
     name      |  price  |               features
---------------+---------+---------------------------------------
 Laptop Pro 15 | 1299.99 | {Touchscreen,"32GB RAM","1024GB SSD"}
```

# 查询数组数据

`PostgreSQL` 数组使用基于 1 的索引，即第一个 `arary` 元素是 1，第二个数组元素是 2，依此类推。

以下查询从 `products` 表中检索产品名称和第一个功能：

```sql
SELECT
  name,
  features[1] AS main_feature
FROM
  products;
```

输出：

```sql
     name      | main_feature
---------------+--------------
 Smartphone X1 | 5G
 Laptop Pro 15 | Touchscreen
```

在此示例中，查询检索 `products` 表中每个产品的第一个特征。

以下查询检索完整的要素数组：

```sql
SELECT
  name,
  features
FROM
  products;
```

输出：

```sql
     name      |               features
---------------+---------------------------------------
 Smartphone X1 | {5G,"OLED Display","512GB Storage"}
 Laptop Pro 15 | {Touchscreen,"32GB RAM","1024GB SSD"}
```

# 修改数组元素

`PostgreSQL` 允许您更新数组中的特定元素或替换整个数组。

例如，以下 `UPDATE` 语句修改数组中的第二个元素：

```sql
UPDATE products
SET
  features[2] = 'Super Retina Display'
WHERE
  id = 1;
```

以下语句替换了整个数组：

```sql
UPDATE products
SET
  features = ARRAY[
    'OLED Display',
    '256GB Storage',
    'Water Resistant'
  ]
WHERE
  id = 2;
```

# 在数组中搜索

您可以使用 `ANY` 运算符检查数组中是否存在元素。例如，以下语句检索将 `5G` 作为其功能之一的产品：

```sql
SELECT
  name, features
FROM
  products
WHERE
  '5G' = ANY (features);
```

输出：

```sql
     name      |                  features
---------------+---------------------------------------------
 Smartphone X1 | {5G,"Super Retina Display","512GB Storage"}
```

# 将数组扩展为行

要将数组扩展为一组行，请使用 `UNNEST` 函数：

```sql
SELECT
  name,
  UNNEST(features) AS feature
FROM
  products;
```

输出：

```sql
     name      |       feature
---------------+----------------------
 Smartphone X1 | 5G
 Smartphone X1 | Super Retina Display
 Smartphone X1 | 512GB Storage
 Laptop Pro 15 | OLED Display
 Laptop Pro 15 | 256GB Storage
 Laptop Pro 15 | Water Resistant
```

使用 `UNNEST` 函数将数组元素视为单独的行。

# 计算数组元素

`ARRAY_LENGTH` 函数返回数组中的元素数：

```sql
ARRAY_LENGTH(array, dimension)
```

例如，以下查询使用 `ARRAY_LENGTH` 函数返回每个产品的功能数：

```sql
SELECT
  name,
  array_length(features, 1) AS feature_count
FROM
  products;
```

参数 1 表示一维数组。

输出：

```sql
     name      | feature_count
---------------+---------------
 Smartphone X1 |             3
 Laptop Pro 15 |             3
```

# 向数组添加新元素

`ARRAY_APPEND` 将元素附加到数组的末尾。

例如，以下语句使用 `ARRAY_APPEND` 函数向产品 `ID 1` 添加功能：

```sql
UPDATE products
SET
  features = ARRAY_APPEND(features, 'Fingerprint')
WHERE
  id = 1;
```

以下查询验证更新：

```sql
SELECT
  name,
  features
FROM
  products
WHERE
  id = 1;
```

输出：

```sql
     name      |                        features
---------------+---------------------------------------------------------
 Smartphone X1 | {5G,"Super Retina Display","512GB Storage",Fingerprint}
```

# 从数组中删除元素

要从数组中删除元素的所有匹配项，请使用 `ARRAY_REMOVE` 函数：

```sql
ARRAY_REMOVE(array, element)
```

例如，以下语句从产品 `ID` 2 中删除触摸屏功能：

```sql
UPDATE products
SET
  features = ARRAY_REMOVE(features, 'Touchscreen')
WHERE
  id = 2;
```

以下查询检查更新：

```sql
SELECT
  name,
  features
FROM
  products
WHERE
  id = 2;
```

输出：

```sql
     name      |                      features
---------------+----------------------------------------------------
 Laptop Pro 15 | {"OLED Display","256GB Storage","Water Resistant"}
```

# 索引数组列

如果性能至关重要，请考虑使用 `GIN` 索引来加快数组中的搜索速度。

例如，以下语句为 `features` 列中的数据创建索引：

```sql
CREATE INDEX idx_features ON products USING GIN (features);
```

通常，您对标记、属性等小列表使用数组。

如果需要频繁查询数据，可以考虑将数组数据存储在单独的表中，并与主表建立关系。

# 总结

- 数组是具有相同数据类型的元素的集合。
- 使用数组在一列中存储多个值。
