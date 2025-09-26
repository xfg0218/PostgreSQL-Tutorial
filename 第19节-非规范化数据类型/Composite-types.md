**总结**：在本教程中，您将学习如何使用 `PostgreSQL` 复合类型并有效地作复合列。

# `PostgreSQL` 复合类型简介

在 `PostgreSQL` 中，复合类型表示行或记录的结构。复合类型是字段名称及其数据类型的列表。

`PostgreSQL` 允许您以与简单类型相同的方式使用复合类型。

例如，可以将复合类型用于表列、函数参数和函数返回类型。

# 创建复合类型

要创建新的复合类型，请使用 `CREATE TYPE` 语句，如下所示：

```sql
CREATE TYPE type_name AS (
    field1 data_type,
    field2 data_type,
    ...
);
```

在此语法中：

- 首先，在 `CREATE TYPE` 关键字后提供复合类型名称。
- 其次，在 `AS` 关键字后面的括号内列出字段名称及其相应的数据类型。

例如，以下语句创建名为 `coordinate` 的复合类型，其中包括 `latitude` 度和 `longitude`：

```sql
CREATE TYPE coordinate AS (
    latitude DEC,
    longitude DEC
);
```

# 使用复合类型

创建复合类型后，您可以像简单类型一样使用它。例如，您可以使用复合类型作为表列的类型。

以下语句创建一个名为 `warehouse_locations` 的表，该表使用 `coordinate` 复合类型：

```sql
CREATE TABLE warehouse_locations(
    warehouse_id INT PRIMARY KEY,
    location coordinate
);
```

# 构造复合值

要将复合值创建为文本常量，可以使用 `ROW` 关键字并将逗号分隔的字段值括在括号中，如下所示：

```sql
ROW(value1, value2, ...)
```

例如，您可以像这样构造 `coordinate` 复合类型的值：

```sql
ROW(37.318686, -121.871019)
```

以下语句将新行插入 `warehouse_locations` 表中：

```sql
INSERT INTO warehouse_locations(warehouse_id, location)
VALUES(1, ROW(37.318686, -121.871019))
RETURNING *;
```

输出：

```sql
 warehouse_id |        location
--------------+-------------------------
            1 | (37.318686,-121.871019)
```

可以使用点表示法显式指定要插入的复合列的字段：

```sql
composite_column.field_name
```

例如，以下语句将新行插入到 `warehouse_locations` 表中：

```sql
INSERT INTO
  warehouse_locations (
    warehouse_id,
    location.latitude,
    location.longitude
  )
VALUES
  (2, 37.650972, -122.398659)
RETURNING
  *;
```

输出：

```sql
 warehouse_id |        location
--------------+-------------------------
            2 | (37.650972,-122.398659)
```

# 访问复合类型的字段

您可以使用点表示法访问复合列的字段：

```sql
composite_column.field_name
```

有时，您需要将复合列放在括号中，如下所示：

```sql
(composite_column).field_name
```

要访问复合列的所有字段，您可以使用星号简写 `(*)` ：

```sql
(composite_column).*
```

例如，以下语句从 `warehouse_locations` 表中检索 `latitude` 度和 `longitude` 度以及 `warehouse_id`：

```sql
SELECT
  warehouse_id,
  (location).latitude,
  (location).longitude
FROM
  warehouse_locations;
```

输出：

```sql
 warehouse_id | latitude  |  longitude
--------------+-----------+-------------
            1 | 37.318686 | -121.871019
            2 | 37.650972 | -122.398659
```

在 `SELECT` 语句中，如果不使用括号，`PostgreSQL` 可能会将 `location` 类型误解为表名并发出错误。

上述查询等效于以下使用简写 `(*)` 的查询：

```sql
SELECT
    warehouse_id,
    (location).*
FROM
    warehouse_locations;
```

输出：

```sql
 warehouse_id | latitude  |  longitude
--------------+-----------+-------------
            1 | 37.318686 | -121.871019
            2 | 37.650972 | -122.398659
```

# 更新复合值

以下语句更新 `id` 为 `1` 的仓库位置的 `latitudelongitude`：

```sql
UPDATE warehouse_locations
SET
  location.latitude = 37.650971,
  location.longitude = -122.398658
WHERE
  warehouse_id = 1
RETURNING *;
```

输出：

```sql
 warehouse_id |        location
--------------+-------------------------
            1 | (37.650971,-122.398658)
```

访问 `UPDATE` 语句的 `SET` 子句中的字段时，不要使用括号，因为点表示法足以标识复合类型中的各个字段。在这里使用括号会导致错误。

要一次更新 `location` 列的所有字段，您还可以使用以下语法：

```sql
UPDATE warehouse_locations
SET
  location = ROW (37.650971, -122.398658)
WHERE
  warehouse_id = 1
RETURNING *;
```

输出：

```sql
 warehouse_id |        location
--------------+-------------------------
            1 | (37.650971,-122.398658)
```

# 根据复合值删除行

使用 `DELETE` 语句删除基于复合类型字段的行时，确实需要在 `WHERE` 子句中使用括号。原因是 `WHERE` 子句用表名限定列。如果不使用括号，`PostgreSQL` 可能会将复合误解为表。

例如，以下语句从 `warehouse_locations` 表中删除位置纬 `latitude` 等于 `37.650971` 的行：

```sql
DELETE FROM warehouse_locations
WHERE
  (location).latitude = 37.650971
RETURNING *;
```

输出：

```sql
 warehouse_id |        location
--------------+-------------------------
            1 | (37.650971,-122.398658)
```

# 应用约束

复合类型是灵活的。但是，它们不直接支持单个字段的约束，例如 `NOT NULL` 和 `CHECK`。

例如，如果将 `NOT NULL` 约束应用于 `warehouse_locations` 表：

```sql
ALTER TABLE warehouse_locations
ALTER COLUMN location
SET NOT NULL;
```

`NOT NULL` 约束确保整个复合值为 `NOT NULL` 而不是单个字段。

因此，以下语句无法在 `location` 列中插入 `NULL`：

```sql
INSERT INTO warehouse_locations(warehouse_id, location)
VALUES(3, NULL);
```

错误：

```sql
null value in column "location" of relation "warehouse_locations" violates not-null constraint
```

但是您可以在 `ROW` 中单独将 `NULL` 插入到纬 `latitudelongitude` 中：

```sql
INSERT INTO
  warehouse_locations (warehouse_id, location)
VALUES
  (3, ROW (NULL, NULL));
```

若要将 `NOT NULL` 或 `CHECK` 等约束应用于复合列的各个字段，可以在复合类型上创建一个域，并将约束应用于该域。例如：

首先，从 `warehouse_locations` 表中删除 `id` 为 `3` 的行（如果存在）：

```sql
DELETE FROM warehouse_locations 
WHERE warehouse_id = 3;
```

其次，在 `coordinate` 类型上创建一个域 `coordinate_domain`：

```sql
CREATE DOMAIN coordinate_domain 
AS coordinate 
CHECK (
  (VALUE).latitude IS NOT NULL
  AND (VALUE).longitude IS NOT NULL
);
```

第三，将 `location` 列的类型更改为 `coordinate_domain`：

```sql
ALTER TABLE warehouse_locations
ALTER COLUMN location TYPE coordinate_domain;
```

最后，尝试将 `NULL` 插入到 `location` 的各个字段中：

```sql
INSERT INTO
  warehouse_locations (warehouse_id, location)
VALUES
  (3, ROW (NULL, NULL));
```

这将导致预期的错误：

```sql
ERROR:  value for domain coordinate_domain violates check constraint "coordinate_domain_check"
```

# 隐式复合类型

创建表时，`PostgreSQL` 会自动创建相应的复合类型。

```sql
CREATE TABLE product_serials (
  serial_no VARCHAR(25) PRIMARY KEY,
  product_id INT NOT NULL
);
```

`PostgreSQL` 会自动创建一个名为 `product_serials` 的 `product_serials` 复合类型，其中包含两个字段 `serial_no` 和 `product_id` 。但是，它不会对类型进行约束。

# 总结

- 复合类型表示记录或行。
- 复合类型包含字段及其相应数据类型的列表。
- 使用复合类型类似于简单类型。
- 复合类型不直接支持对单个字段的约束，但您可以在复合类型上创建域并将约束应用于该域。
- `PostgreSQL` 会自动为每个表创建相应的隐式复合类型。
