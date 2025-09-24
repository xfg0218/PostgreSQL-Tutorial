**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 分部索引仅对满足指定条件的数据进行索引。

# `PostgreSQL` 分部索引简介

当您创建包含一列或一组列的索引时，`PostgreSQL` 会从这些列中提取所有数据以进行索引。

`PostgreSQL` 允许您在表中仅包含满足索引中指定条件的数据子集。该索引称为 `partial index`。

要创建部分索引，请将以下 `CREATE INDEX` 语句与 `WHERE` 子句一起使用：

```sql
CREATE INDEX [index_name]
ON table_name (column1, column2)
WHERE condition;
```

在此语法中：

- 首先，在 `CREATE INDEX` 关键字后指定索引名称。索引名称是可选的。
- 其次，提供表名后跟要包含在索引中的一列或多列。
- 第三，在 `WHERE` 子句中定义一个条件，以指定索引中应包含哪些行。仅包含满足条件的行。

当您从表中查询数据时，`PostgreSQL` 将仅对索引中包含的行使用部分索引。

# `PostgreSQL` 分部索引示例

首先，创建一个 `products` 表：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DEC(10, 2) NOT NULL,
  discontinued BOOL
);
```

接下来，创建一个函数以将行插入到 `products` 表中：

```sql
CREATE OR REPLACE FUNCTION insert_products (n INT) 
RETURNS VOID 
AS 
$$
DECLARE
   i INT;
BEGIN
   FOR i IN 1..n LOOP
      INSERT INTO products (name, price, discontinued)
      VALUES (
       'Product_' || i, -- Generating a simple name
       ROUND((RANDOM() * 100 + 1)::numeric, 2), -- Corrected casting for rounding
       CASE WHEN RANDOM() < 0.2 THEN TRUE ELSE FALSE END -- 20% chance of being discontinued
      );
   END LOOP;
END;
$$ 
LANGUAGE plpgsql;
```

然后，调用 `insert_products` 函数在 `products` 表中插入 `1000` 行：

```sql
SELECT insert_products(1000);
```

之后，创建一个仅包含活跃产品的索引：

```sql
CREATE INDEX ON products (price)
WHERE
  discontinued = FALSE;
```

此查询创建一个部分索引 `products_price_idx` ，其中仅包含已停产 `false` 的产品。

最后，检索活动产品：

```sql
EXPLAIN SELECT
  name,
  price
FROM
  products
WHERE
  discontinued = FALSE
  AND price < 100;
```

输出：

```sql
                                    QUERY PLAN
-----------------------------------------------------------------------------------
 Bitmap Heap Scan on products  (cost=9.57..18.66 rows=167 width=532)
   Recheck Cond: ((price < '100'::numeric) AND (NOT discontinued))
   ->  Bitmap Index Scan on products_price_idx  (cost=0.00..9.53 rows=167 width=0)
         Index Cond: (price < '100'::numeric)
```

输出指示查询使用 `products_price_idx` 索引。

如果您查询已停产的产品，`PostgreSQL` 将不会使用该索引。例如：

```sql
EXPLAIN SELECT
  name,
  price
FROM
  products
WHERE
  discontinued = TRUE
  AND price < 100;
```

输出：

```sql
                         QUERY PLAN
------------------------------------------------------------
 Seq Scan on products  (cost=0.00..11.75 rows=23 width=532)
   Filter: (discontinued AND (price < '100'::numeric))
```

# `PostgreSQL` 分部索引的好处

部分索引具有以下优点：

- **较小的索引大小** – 部分索引仅采用目标行进行索引。因此，它们占用的磁盘空间更少。
- **更快的索引扫描和更好的查询性能** – 当您执行与索引条件匹配的查询时，`PostgreSQL` 必须扫描较小的索引，这有助于提高查询性能。
- **减少写入开销并提高查询性能**： 当您插入、更新和删除时，`PostgreSQL` 必须更新行的子集，从而减少索引更新。

# 何时使用分部索引

在实践中，您可以在以下场景中使用分部索引：

- 查询一致包含条件的大型表与部分索引匹配。
- 筛选条件显著减少了索引的行数，例如活跃用户、实时产品等。

# 总结

- 使用部分索引通过仅索引表行的子集来优化查询。
