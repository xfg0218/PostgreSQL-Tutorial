**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 公用表表达式（`CTE`）从数据库中查询数据。

# `PostgreSQL CTE` 入门

`CTE` 代表公用表表达式。`PostgreSQL CTE` 提供了一种定义临时表的方法，该临时表可在 `SELECT` 、 `INSERT` 、 `UPDATE`、`DELETE` 和 `MERGE` 语句中引用。

以下是定义 `CTE` 的语法：

```sql
WITH cte_name(column_list) AS (
   -- CTE query
   SELECT ...
)
-- Main query
SELECT select_list
FROM cte_name;
```

在以下语法中：

- `WITH` 关键字定义了公用表表达式（ `CTE` ）。你可以将CTE视为查询中的一个临时表。
- `cte_name` 是你为 `CTE` 分配的名称。之后，你可以在主查询中像引用常规表一样引用 `cte_name` 。
- `column_list` 是公用表表达式（ `CTE` ）的一个可选的、用逗号分隔的列清单。如果不指定 `column_list` ，公用表表达式将使用从公用表表达式查询返回的列。
- `CTE` 查询是定义 `CTE` 结构的语句。它可以是任何返回结果集的语句，包括 `SELECT` 、 `INSERT` 、`UPDATE` 、`DELETE` 或 `MERGE` 。
- 主查询是通过引用 `cte_name` 来使用公用表表达式（`CTE` ）的语句。主查询可以是 `SELECT` 、 `INSERT` 、`UPDATE` 、 `DELETE` 或 `MERGE` 语句。

# 基本的 `PostgreSQL CTE` 示例

以下示例使用 `CTE` 计算所有仓库的最大库存价值：

```sql
WITH warehouse_inventories (warehouse_name, amount) AS (
    SELECT
      warehouse_name,
      SUM(quantity * price)
    FROM
      inventories
      INNER JOIN products USING (product_id)
      INNER JOIN warehouses USING (warehouse_id)
    GROUP BY
      warehouse_name
)
SELECT
  MAX(amount)
FROM
  warehouse_inventories;
```

输出：

```sql
    max
------------
 2419483.10
```

工作原理。

首先，定义一个名为 `warehouse_inventories` 的 `CTE` ，它包含两列：

- `warehouse_name`
- `amount`

在 `CTE` 查询中，使用 `INNER JOIN` 和  `GROUP BY` 子句从 `inventories` 、 `products` 和 `warehouses` 这三个表中检索每个仓库的总库存：

```sql
SELECT
  warehouse_name,
  SUM(quantity * price)
FROM
  inventories
  INNER JOIN products USING (product_id)
  INNER JOIN warehouses USING (warehouse_id)
GROUP BY
  warehouse_name;
```

输出：

```sql
     warehouse_name      |    sum
-------------------------+------------
 San Francisco Warehouse | 2419483.10
 Los Angeles Warehouse   | 2379982.20
 San Jose Warehouse      | 2044481.10
```

其次，从主查询的 `warehouse_inventories` 中检索最大值：

```sql
SELECT
  MAX(amount)
FROM
  warehouse_inventories;
```

# `PostgreSQL CTE` 与 `DELETE` 语句示例

首先，创建一个表，名为 `product_logs` ，用于存储已删除的产品：

```sql
CREATE TABLE product_logs (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  product_id INT NOT NULL,
  product_name VARCHAR(100) NOT NULL,
  price DECIMAL(11, 2),
  brand_id INT NOT NULL,
  category_id INT NOT NULL,
  deleted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

其次，使用 `CTE` 删除 `ID` 小于或等于3的产品，并将这些已删除的产品插入到 `product_logs` 表中：

```sql
WITH deleted_products AS (
    DELETE FROM products
    WHERE product_id <= 3 
    RETURNING *
)
INSERT INTO
  product_logs (product_id, product_name, price,  brand_id, category_id)
SELECT
  product_id, product_name, price, brand_id, category_id 
FROM
  deleted_products;
```

第三，从 `product_logs` 表中检索数据：

```sql
SELECT * FROM product_logs;
```

输出：

```sql
id | product_id  |    product_name    |  price  | brand_id | category_id |         deleted_at
----+------------+--------------------+---------+----------+-------------+----------------------------
  1 |          1 | Samsung Galaxy S24 |  999.99 |        1 |           1 | 2024-12-02 16:57:20.060067
  2 |          2 | Apple iPhone 15    | 1099.99 |        2 |           1 | 2024-12-02 16:57:20.060067
  3 |          3 | Huawei Mate 60     |  899.99 |        3 |           1 | 2024-12-02 16:57:20.060067
```

# 总结

- 使用 `WITH` 语句在查询中定义公用表表达式（`CTE`）或临时表名称。
- 使用 `PostgreSQL` 的公用表表达式（ `CTE` ）来简化复杂查询。