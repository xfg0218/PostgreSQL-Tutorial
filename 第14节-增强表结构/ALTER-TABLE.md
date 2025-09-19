**摘要**：在本教程中，您将学习如何使用 `PostgreSQL ALTER TABLE` 语句增强表的结构。

# `PostgreSQL` 入门 `ALTER TABLE` 语句

在 `PostgreSQL` 中，`ALTER TABLE` 语句允许您有效地修改表结构。

以下是 `ALTER TABLE` 语句的基本语法：

```sql
ALTER TABLE table_name
action;
```

在此语法中：

- 首先，指定要修改结构的表名。
- 其次，提供您要执行的作。

以下是主要行动：

- `Renaming a table` 
- `Rename a column`.
- `Adding one or more columns to the table` 
- `Removing a column `
- `Applying a constraint to a column`
- `Changing the data type of a column`

假设我们有以下存储产品信息的 `materials` 表：

```sql
CREATE TABLE materials (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(40) NOT NULL,
  status bool NOT NULL
);
```

# 重命名表

您使用 `ALTER TABLE ... RENAME TO` 语句重命名表：

```sql
ALTER TABLE table_name
RENAME TO new_table_name;
```

在此语法中：

- 首先，在 `ALTER TABLE` 子句中指定表的名称。
- 其次，在 `RENAME TO` 子句中定义新表名。

以下示例使用 `ALTER TABLE ... RENAME TOmaterials` 表重命名为 `products` ：

```sql
ALTER TABLE materials
RENAME TO products;
```

# 重命名列

要重命名列，请使用 `ALTER TABLE ... RENAME COLUMN` 语句：

```sql
ALTER TABLE table_name
RENAME COLUMN column_name
TO new_column_name;
```

例如，以下示例将 `status` 列重命名为 `active`：

```sql
ALTER TABLE products
RENAME COLUMN status
TO active;
```

# 向表添加新列

使用 `ALTER TABLE...ADD COLUMN` 语句向表添加新列：

```sql
ALTER TABLE table_name
ADD COLUMN column_name data_type constraint;
```

在此语法中：

- 首先，提供要修改的表的名称。
- 其次，在 `ADD COLUMN` 子句中定义列名、类型和约束。

例如，以下语句将 `price` 列添加到 `products` 表：

```sql
ALTER TABLE products
ADD COLUMN price DEC(5, 2) NOT NULL;
```

可以使用 `ALTER TABLE` 语句添加多个列。

例如，以下 `ALTER TABLE ... ADD COLUMN` 语句将 `weight` 、`volume` 和 `model_no` 列添加到 `products` 表中：

```sql
ALTER TABLE products
ADD COLUMN weight DEC(5, 2),
ADD COLUMN volume DEC(5, 2),
ADD COLUMN model_no VARCHAR(25);
```

# 更改列数据类型

您使用 `ALTER TABLE ... ALTER COLUMN ... SET DATA TYPE` 语句来更改列的数据类型：

```sql
ALTER TABLE table_name
ALTER COLUMN column_name
SET DATA TYPE new_data_type;
```

在此语法中：

- 首先，在 `ALTER TABLE` 子句中指定表的名称。
- 其次，提供要修改的列的名称。
- 第三，在 `SET DATA TYPE` 子句中设置新的数据类型。

如果表有数据，则必须确保新类型与现有数据兼容。

例如，以下语句将 `products` 表中 `price` 列的大小扩展到 `DEC(11,2)`

```sql
ALTER TABLE products
ALTER COLUMN price
SET DATA TYPE DEC(11, 2);
```

# 对列应用约束

您使用 `ALTER TABLE ... ALTER COLUMN SET` 语句向列添加约束。

# `NOT NULL` 约束

以下语句将 `NOT NULL` 约束应用于列：

```sql
ALTER TABLE table_name
ALTER COLUMN column_name
SET NOT NULL;
```

例如，以下语句将 `NOT NULL` 约束添加到 `products` 表的 `weight` 和 `volume` 列：

```sql
ALTER TABLE products
ALTER COLUMN weight SET NOT NULL,
ALTER COLUMN volume SET NOT NULL;
```

在运行语句之前，权重和列不得有任何 `NULL` 值。

# `CHECK` 约束

以下语句将 `CHECK` 约束添加到列中：

```sql
ALTER TABLE table_name
ALTER COLUMN column_name
ADD CHECK expression;
```

以下语句将 `CHECK` 约束添加到 `products` 表的 `weight` 和 `volume` 列：

```sql
ALTER TABLE products
ADD CHECK (weight > 0),
ADD CHECK (volume > 0);
```

# `DEFAULT` 约束

以下语句使用 `DEFAULT` 约束为列设置默认值：

```sql
ALTER TABLE table_name
ALTER COLUMN column_name
SET DEFAULT default_value;
```

例如，以下语句将 `active` 列的值设置为 `true`：

```sql
ALTER TABLE products
ALTER COLUMN active
SET DEFAULT true;
```

# `UNIQUE` 约束

以下语句为一列或多列添加唯一约束：

```sql
ALTER TABLE table_name
ADD UNIQUE (column1, column2, ...);
```

例如，以下语句向 `products` 表的 `model_no` 添加唯一约束：

```sql
ALTER TABLE products
ADD UNIQUE (model_no);
```

# 删除列

`ALTER TABLE ... DROP COLUMN` 从表中删除一列：

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

例如，以下语句删除 `products` 表的 `active` 列：

```sql
ALTER TABLE products
DROP COLUMN active;
```

# 总结

- 使用 `ALTER TABLE` 语句更改表的结构。