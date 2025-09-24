**摘要**：在本教程中，您将学习如何利用 `PostgreSQL` 仅索引扫描来提高查询性能。

# `PostgreSQL` 仅索引扫描概述

在 `PostgreSQL` 中，索引是优化查询性能的重要工具。

当查询使用索引列过滤数据时，`PostgreSQL` 会在索引中找到匹配的行并访问表以检索实际的行数据。

如果索引中包含所需的数据，则 `PostgreSQL` 可以完全从索引中检索数据，而无需访问表。这种机制称为 `index-only scan`。

在以下情况下，可以进行仅索引扫描：

- 索引包含查询所需的所有列。
- 可见性映射将所有行标记为全部可见，即这些行最近没有更新或删除。

`PostgreSQL` 使用可见性映射（位图结构）来跟踪表中特定页面上的所有行是否对所有事务可见。

# `PostgreSQL` 仅索引扫描示例

首先，创建一个 `products` 表：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DEC(10, 2) NOT NULL
);
```

其次，创建一个函数，在 `products` 表中插入 `n` 行：

```sql
CREATE OR REPLACE FUNCTION insert_products (n INT) 
RETURNS VOID 
AS 
$$
DECLARE
   i INT;
BEGIN
   FOR i IN 1..n LOOP
      INSERT INTO products (name, price)
      VALUES (
       'Product_' || i, 
       ROUND((RANDOM() * 100 + 1)::numeric, 2)
      );
   END LOOP;
END;
$$ 
LANGUAGE plpgsql;
```

第三，通过调用 `insert_products` 函数在 `products` 表中插入 `1000` 行：

```sql
SELECT insert_products (1000);
```

第四，`products` 表的 `name` 列创建索引：

```sql
CREATE INDEX ON products(name);
```

第五，找到名称 `Product_100` 的产品：

```sql
EXPLAIN ANALYZE 
SELECT name
FROM products 
WHERE name = 'Product_100';
```

输出：

```sql
                                                            QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------------
 Index Only Scan using products_name_idx on products  (cost=0.28..8.29 rows=1 width=11) (actual time=0.983..0.984 rows=1 loops=1)
   Index Cond: (name = 'Product_100'::text)
   Heap Fetches: 1
 Planning Time: 0.078 ms
 Execution Time: 0.999 ms
```

输出表明 `PostgreSQL` 使用仅索引扫描，因为查询仅检索索引中包含的 `name` 。但是，它仍然访问堆（或表）以获取一行：

```sql
Heap Fetches: 1
```

发生这种情况是因为可见性映射没有将页面标记为全可见，因此 `PostgreSQL` 需要验证行的可见性。

第六，要验证表的页面是否标记为全部可见，您可以运行以下查询：

```sql
SELECT
  relname,
  relpages,
  relallvisible
FROM
  pg_class
WHERE
  relname = 'products';
```

输出：

```sql
 relname  | relpages | relallvisible
----------+----------+---------------
 products |        7 |             0
```

如果 `relallvisible` 为低，则 `PostgreSQL` 必须检查堆（或表）。在本例中，它为零。

第七，为了提高行的可见性，您可以运行 `VACUUM ANALYZE` 语句：

```sql
VACUUM ANALYZE products;
```

此语句将较旧的行标记为全部可见，从而减少堆获取并提高仅索引扫描性能。

第八，运行查询检查 `relallvisible` 的新值：

```sql
SELECT
  relname,
  relpages,
  relallvisible
FROM
  pg_class
WHERE
  relname = 'products';
```

输出：

```sql
 relname  | relpages | relallvisible
----------+----------+---------------
 products |        7 |             7
```

最后，再次运行查找名称为 `Product_100` 的产品的查询，您会看到 `PostgreSQL` 使用仅索引扫描，但不必从堆中获取数据。

```sql
EXPLAIN
ANALYZE
SELECT
  name
FROM
  products
WHERE
  name = 'Product_100';
```

输出：

```sql
                                                            QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------------
 Index Only Scan using products_name_idx on products  (cost=0.28..4.29 rows=1 width=11) (actual time=0.153..0.154 rows=1 loops=1)
   Index Cond: (name = 'Product_100'::text)
   Heap Fetches: 0
 Planning Time: 0.293 ms
 Execution Time: 0.214 ms
```

# 总结

- 利用仅索引扫描，通过从索引中读取所有必需的数据来提高性能。
- 使用 `VACUUM ANALYZE` 语句可提高仅索引扫描的效率。
