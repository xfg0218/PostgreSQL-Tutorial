**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 唯一索引来确保一个或多个表列中的值是唯一的。

# `PostgreSQL` 唯一索引概述

唯一索引可确保一列或多列中的值在表中的所有行中都是唯一的，从而保持数据的完整性。

如果尝试在具有唯一索引的列中插入已存在的值，则会遇到错误。

要创建唯一索引，请使用 `CREATE UNIQUE INDEX` 语句：

```sql
CREATE UNIQUE INDEX [index_name]
ON table_name (column1[, column2, ...])
[ NULLS [ NOT ] DISTINCT ];
```

在此语法中：

- 首先，在 `CREATE UNIQUE INDEX` 关键字后指定索引名称。如果省略它，`PostgreSQL` 将自动生成一个名称。
- 其次，提供要在其上创建索引的表名。
- 第三，列出索引中包含的一列或多列。
- 第三，`NULL NOT DISTINCT` 平等对待 `NULL`，而 `NULLS DISTINCT` 将 `NULL` 视为非重复值。默认值为 `NULLS DISTINCT` 这意味着索引列可能包含多个 `NULL`。


# `PostgreSQL` 唯一索引示例

首先，创建一个表 `warehouse_workers` 来存储仓库工作人员数据：

```sql
CREATE TABLE IF NOT EXISTS warehouse_workers (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255),
  phone VARCHAR(25),
  extension VARCHAR(25)
);
```

其次，为 `email` 列创建一个唯一索引：

```sql
CREATE UNIQUE INDEX ON warehouse_workers (email);
```

此唯一索引可确保表的 `email` 列中值的唯一性。

第三，在 `warehouse_workers` 表中插入新行：

```sql
INSERT INTO
  warehouse_workers (name, email, phone, extension)
VALUES
  (
    'Emma Smith',
    'emma.s@pgtutorial.com',
    '(408)-111-2222',
    '101'
  );
```

最后，尝试插入具有相同电子邮件地址的新行：

```sql
INSERT INTO
  warehouse_workers (name, email, phone, extension)
VALUES
  (
    'Emma Scott',
    'emma.s@pgtutorial.com',
    '(408)-111-2222',
    '102'
  );
```

PostgreSQL发出错误：

```sql
ERROR: duplicate key value violates unique constraint "warehouse_workers_email_idx"
DETAIL: Key (email)=(emma.s@pgtutorial.com) already exists.
```

唯一索引 `warehouse_workers_email_idx` 不允许 `email` 列中出现重复的电子邮件。

# 在多列上创建唯一索引

首先，创建一个包含 `phone` 和 `extension` 的唯一索引：

```sql
CREATE UNIQUE INDEX ON warehouse_workers (phone, extension);
```

其次，在 `warehouse_workers` 表中插入新行：

```sql
INSERT INTO
  warehouse_workers (name, email, phone, extension)
VALUES
  (
    'Mary Scott',
    'mary.s@pgtutorial.com',
    '(408)-111-2222',
    '102'
  );
```

尽管 `Mary` 的电话号码与 `Emma` 的电话号码相同，但该语句成功地在表中插入了一行。

原因是新的唯一索引强制执行 `phone` 和 `extension` 列中的唯一值。

第三，尝试插入具有相同电话和分机的新行：

```sql
INSERT INTO
  warehouse_workers (name, email, phone, extension)
VALUES
  (
    'Ava Garcia ',
    'ava.g@pgtutorial.com',
    '(408)-111-2222',
    '102'
  );
```

`PostgreSQL` 发出以下错误：

```sql
ERROR: duplicate key value violates unique constraint "warehouse_workers_phone_extension_idx"
DETAIL: Key (phone, extension)=((408)-111-2222, 102) already exists.
```

原因是 `warehouse_workers` 表中已经存在一对值 `(408)-111-2222, 102)`

# 唯一索引与主键

在 `PostgreSQL` 中，主键是一种特殊类型的唯一索引，它：

-  不允许 `NULL` 。
- 可以应用于每个表的一个或多个列。
- 本质上意味着独特性

相反，唯一索引允许 `NULL` ，除非指定 `NULL NOT DISTINCT` 选项。

# 唯一索引与唯一约束

唯一索引和唯一约束都确保表中一个或多个列中值的唯一性。

但是，唯一约束是逻辑模式规则，而唯一索引是 `PostgreSQL` 用来强制执行唯一性的物理结构。

当您创建唯一约束时，`PostgreSQL` 会自动生成唯一索引。

# 总结

- 使用唯一索引在一列或多列中强制执行唯一性值。