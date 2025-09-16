**摘要**：在本教程中，您将了解 `PostgreSQL` 视图以及如何使用它来简化复杂的查询。

# `PostgreSQL` 视图入门

视图是存储在 `PostgreSQL` 数据库服务器中的命名查询。视图允许您像常规表一样从中查询数据，但不以物理方式存储数据。因此，视图称为虚拟表。

在 `PostgreSQL` 中，您可以创建物理存储数据的视图。这些视图称为物化视图。

# 创建视图

要创建视图，请从返回结果集并为其分配名称的查询开始。

在 `PostgreSQL` 中，您可以使用 `CREATE VIEW` 语句创建新视图：

```sql
CREATE VIEW view_name 
AS 
query;
```

在此语法中：

- 首先，在 `CREATE VIEW` 关键字后指定视图名称。
- 其次，提供定义视图的查询。

定义视图的查询称为定义查询。定义查询引用的表称为基表。

以下语句基于从 `products` 和 `categories` 表中选择数据的查询创建名为 `product_view` 的视图：

```sql
CREATE VIEW product_view 
AS
SELECT
  product_id,
  product_name,
  price,
  category_name
FROM
  products
  JOIN categories USING (category_id);
```

创建 `product_view` 视图后，您可以从中查询数据。

# 使用视图查询数据 

以下示例使用 `SELECT` 语句通过 `product_view` 从基于表中查询数据：

```sql
SELECT
  product_name,
  category_name,
  price
FROM
  product_view;
```

在此查询中，我们像使用常规表一样使用 `product_view` 。当 `PostgreSQL` 收到查询时，它会执行以下步骤：

- 首先，执行产品视图的定义查询。
- 其次，从定义查询返回的结果集中选择数据。

从技术上讲，它将执行以下查询：

```sql
SELECT
  product_name,
  brand_name,
  price
FROM
  (
    SELECT
      product_id,
      product_name,
      price,
      category_name
    FROM
      products
      JOIN categories USING (category_id)
  ) t;
```

在此示例中，我们使用 `product_view` 来简化包含联接的查询。此外，我们可以在其他查询中重用 `product_view` 。

例如，我们可以通过从 `product_view` 中查询来检索每个 `categories` 中最昂贵的产品价格，如下所示：

```sql
SELECT
  category_name,
  MAX(price) price
FROM
  product_view
GROUP BY
  category_name
ORDER BY
  price;
```

此查询比使用联接的查询更简单：

```sql
SELECT
  category_name,
  MAX(price) price
FROM
  products
  JOIN categories USING (category_id)
GROUP BY
  category_name
ORDER BY
  price;
```

# 替换视图

如果要修改现有视图，可以使用 `CREATE VIEW` 语句中的 `OR REPLACE` 子句：

```sql
CREATE OR REPLACE VIEW view_name 
AS query;
```

`CREATE OR REPLACE VIEW` 将创建 `view_name`（如果不存在），如果存在，则替换现有视图。

以下示例通过添加 `brands` 表中的 `brand_name` 列来替换 `product_view` ：

```sql
CREATE OR REPLACE TABLE product_view AS
SELECT
  product_id,
  product_name,
  price,
  category_name,
  brand_name
FROM
  products
  JOIN categories USING (category_id)
  JOIN brands USING (brand_id);
```

# 基于其他视图创建视图

`PostgreSQL` 允许您基于其他视图创建视图。例如，以下内容根据 `product_view` 创建一个名为 `smartphone_view` 的视图：

```sql
CREATE OR REPLACE VIEW smartphone_view AS
SELECT
  product_id,
  product_name,
  price,
  brand_name
FROM
  product_view
WHERE
  category_name = 'Smartphones';
```

# 删除视图

`DROP VIEW` 允许您从数据库中永久删除视图：

```sql
DROP VIEW IF EXISTS view_name;
```

`PostgreSQL` 不允许您删除具有另一个视图依赖于它的视图。要删除具有依赖项的视图，您可以使用 `CASCADE` 选项：

```sql
DROP VIEW IF EXISTS view_name CASCADE;
```

`CASCADE` 选项将删除依赖于 `view_name` 的视图（和其他对象），进而删除依赖于这些对象的所有对象。

例如，以下语句删除 `product_view` 及其依赖视图：

```sql
DROP VIEW IF EXISTS product_view CASCADE;
```

# 总结

- 视图是存储在 `PostgreSQL` 数据库服务器中的命名查询。
- 视图可以帮助简化复杂的查询。

