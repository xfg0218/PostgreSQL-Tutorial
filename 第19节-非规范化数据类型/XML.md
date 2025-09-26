**摘要**：在本教程中，您将学习如何使用 `PostgreSQL XML` 数据类型将 `XML` 数据存储在数据库中。

# `PostgreSQL XML` 数据类型概述

`PostgreSQL` 支持内置的 `XML` 数据类型，允许您存储格式正确的 `XML` 文档和 `XML` 片段。

要定义 `XML` 类型的列，请使用以下语法：

```sql
column_name XML
```

与使用 `TEXT` 数据类型存储 `XML` 相比，内置 `XML` 数据类型具有以下优点：

- 类型安全：`PostgreSQL` 验证 `XML`，确保 `XML` 数据有效。
- 内置函数：`PostgreSQL` 提供内置函数来帮助您有效地作 `XML` 数据。

# `PostgreSQL XML` 示例

首先，创建一个名为 `xproducts` 的新表来存储产品数据：

```sql
CREATE TABLE xproducts (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  data XML
);
```

在 `xproducts` 表中，`data` 列具有一种存储 `XML` 数据的 `XML` 类型。

其次，在 `xproducts` 表中插入一行新行：

```sql
INSERT INTO
  xproducts (data)
VALUES
  (
    XMLPARSE(
      DOCUMENT '<?xml version="1.0" encoding="UTF-8"?><product>
    <name>Samsung Galaxy S24</name>
    <price>999.99</price>
    <safety_stock>10</safety_stock>
</product>'
    )
  ),
  (
    XMLPARSE(
      DOCUMENT '<?xml version="1.0" encoding="UTF-8"?><product>
    <name>Apple iPhone 15</name>
    <price>1099.99</price>
    <safety_stock>20</safety_stock>
</product>'
    )
  ),
  (
    XMLPARSE(
      DOCUMENT '<?xml version="1.0" encoding="UTF-8"?><product>
    <name>Huawei Mate 60</name>
   <price>899.99</price>
    <safety_stock>30</safety_stock>
</product>'
    )
  );
```

在这份声明中：

- `DOCUMENT` 指示 `PostgreSQL` 以下字符串是一个完整的 `XML` 文档。
- `XMLPARSE` 函数将字符串转换为 `XML` 文档。
- `INSERT` 语句将新行插入 `xproducts` 表中。

第三，使用 `xpath` 函数从 `XML` 数据中检索产品名称：
```sql
SELECT
  xpath('/product/name/text()', data) AS name
FROM
  xproducts;
```

输出：

```sql
          name
------------------------
 {"Samsung Galaxy S24"}
 {"Apple iPhone 15"}
 {"Huawei Mate 60"}
```

在此示例中：

- XPath `/product/name/text()` 返回 `XML` 文档的 `name` 节点的文本值。
- 结果集包含行，其中每行都是表示产品名称的 `XML` 值数组。
- 由于每个产品只有一个名称，因此数组只有一个元素。

第四，检索产品名称和价格，并将它们转换为相应的数据类型：

```sql
SELECT
  (xpath('/product/name/text()', data)) [1]::TEXT name,
  (xpath('/product/price/text()', data)) [1]::TEXT::DEC price,
  (xpath('/product/safety_stock/text()', data)) [1]::TEXT::INT safety_stock
FROM
  xproducts;
```

输出：

```sql
        name        |  price  | safety_stock
--------------------+---------+--------------
 Samsung Galaxy S24 |  999.99 |           10
 Apple iPhone 15    | 1099.99 |           20
 Huawei Mate 60     |  899.99 |           30
```

让我们分解一下这个表达式：

```sql
(xpath('/product/name/text()', data)) [1]::TEXT
```

在这个表达式中：

- `(xpath('/product/name/text()', data))` 返回一个数组。
- `[1]` 返回第一个元素。
- `::TEXT` 将 `XML` 值转换为 `TEXT` 数据类型的值。

以下表达式首先将 `XML` 值强制转换为 `TEXT` 值，然后再将 `TEXT` 值强制转换为十进制：

```sql
(xpath('/product/price/text()', data)) [1]::TEXT::DEC
```

同样，以下表达式首先将 `XML` 值转换为 `TEXT` 值，然后再将文本转换为整数：

```sql
(xpath('/product/safety_stock/text()', data)) [1]::TEXT::INT
```

# 性能

当您从 `xproducts` 表中查询数据时，`PostgreSQL` 必须扫描整个表。

如果性能至关重要，并且 `xproducts` 表具有许多行，则可以为 `XML` 列创建索引以提高性能。

首先，创建一个函数索引，将产品名称提取为文本数组：

```sql
CREATE INDEX product_name_idx 
ON xproducts 
USING BTREE (cast(xpath('/product/name', data) AS TEXT[]));
```

其次，创建一个将行插入 `xproducts` 表的函数：

```sql
CREATE FUNCTION insert_products (row_count INT) 
RETURNS VOID 
LANGUAGE SQL 
AS 
$$
    INSERT INTO xproducts (data)
    SELECT
        XMLPARSE(DOCUMENT '<?xml version="1.0" encoding="UTF-8"?>
        <product>
            <name>' || 'Person' || generate_series || '</name>
            <price>' || generate_series || '</price>
            <safety_stock>' || generate_series || '</safety_stock>
        </product>')
    FROM generate_series(1, row_count);
$$;
```

第三，调用 `insert_products` 函数在 `xproducts` 表中插入 `500` 行：

```sql
SELECT insert_products(500);
```

最后，找到名称为 `Product 160` 的产品：

```sql
EXPLAIN ANALYZE
SELECT
  *
FROM
  xproducts
WHERE
  cast(xpath('/product/name', data) AS TEXT[]) = '{<name>Product 160</name>}';
```

输出：

```sql
                                                         QUERY PLAN

-----------------------------------------------------------------------------------------------------------------------------
 Index Scan using product_name_idx on xproducts  (cost=0.27..8.28 rows=1 width=36) (actual time=0.048..0.048 rows=0 loops=1)
   Index Cond: ((xpath('/product/name'::text, data, '{}'::text[]))::text[] = '{"<name>Product 160</name>"}'::text[])
 Planning Time: 0.268 ms
 Execution Time: 0.087 ms
```

输出指示查询使用索引来筛选 `xproducts` 表中的行。

# 总结

- 使用 `PostgreSQL XML` 数据类型将 XML 文档或片段存储在数据库中。
- 使用 `xpath()` 函数从 XML 文档中检索值。
