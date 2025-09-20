**摘要**：在本教程中，您将学习如何使用 `PostgreSQL ALTER TABLE DROP COLUMN` 语句从表中删除列。

# `PostgreSQL ALTER TABLE DROP COLUMN` 语句入门

当列过时时，有必要将其从表中删除，以避免存储开销并提高数据库性能。

在 `PostgreSQL` 中，您可以使用 `ALTER TABLE DROP COLUMN` 语句从表中永久删除列。

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

在此语法中：

- 首先，指定要在 `ALTER TABLE` 子句中删除列的表名。
- 其次，在 `DROP COLUMN` 子句中提供要放置的列名。

如果删除不存在的列，`PostgreSQL` 将发出错误。为避免此错误，可以使用 `IF EXISTS` 选项：

```sql
ALTER TABLE table_name
DROP COLUMN IF EXISTS column_name;
```

在这种情况下，如果该列不存在，`PostgreSQL` 将发出通知而不是错误。当您需要知道该列是否存在并从应用程序中正确处理它时，此通知会很有帮助。

当您删除列时，`PostgreSQL` 将从该列中删除数据。它还会自动删除涉及列的表约束和索引。

如果删除表外其他对象引用的列，例如视图和外键约束，`PostgreSQL` 也会返回错误。在这种情况下，您可以使用 `CASCADE` 选项：

```sql
ALTER TABLE table_name
DROP COLUMN IF EXISTS column_name CASCADE;
```

`CASCADE` 选项将一次性删除列及其依赖对象，例如外键约束和视图。

要一次删除多个列，您可以使用多个 `DROP COLUMN` 子句：

```sql
ALTER TABLE table_name
DROP COLUMN column1,
DROP COLUMN column2,
DROP COLUMN column3;
```

# 删除一列示例

首先，创建一个存储供应商信息的供应商表 `suppliers` ，包括姓名、电话、电子邮件、传真和地址：

```sql
CREATE TABLE suppliers (
   id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
   name VARCHAR(255) NOT NULL,
   phone VARCHAR(25) NOT NULL UNIQUE,
   email VARCHAR(255) NOT NULL UNIQUE,
   fax VARCHAR(25) NOT NULL UNIQUE,
   address TEXT,
   notes TEXT
);
```

其次，从 `suppliers` 表中删除 `notes` 列。

```sql
ALTER TABLE suppliers 
DROP COLUMN notes;
```

第三，验证列移除情况：

```sql
\d suppliers
```

输出：

```sql
                                Table "public.suppliers"
 Column  |          Type          | Collation | Nullable |           Default
---------+------------------------+-----------+----------+------------------------------
 id      | integer                |           | not null | generated always as identity
 name    | character varying(255) |           | not null |
 phone   | character varying(25)  |           | not null |
 email   | character varying(255) |           | not null |
 fax     | character varying(25)  |           | not null |
 address | text                   |           |          |
Indexes:
    "suppliers_pkey" PRIMARY KEY, btree (id)
    "suppliers_email_key" UNIQUE CONSTRAINT, btree (email)
    "suppliers_fax_key" UNIQUE CONSTRAINT, btree (fax)
    "suppliers_phone_key" UNIQUE CONSTRAINT, btree (phone)
```

输出指示语句已成功删除 `notes` 列。

# 删除具有依赖对象的列

首先，创建一个名为 `fax_messages` 的表来存储传真消息：

```sql
CREATE TABLE fax_messages (
     id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
     fax VARCHAR(255) NOT NULL,
     message TEXT NOT NULL,
     sent_at TIMESTAMPTZ NOT NULL,
     FOREIGN KEY (fax) REFERENCES suppliers(fax) ON DELETE CASCADE
);
```

`fax_messages` 表将 `fax` 列作为引用 `suppliers` 表的 `fax` 列的外键约束。

其次，基于 `suppliers` 表创建一个名为 `faxes` 的视图：

```sql
CREATE VIEW faxes AS
SELECT name, fax
FROM suppliers;
```

第三，从 `suppliers` 表中删除 `fax` 列：

```sql
ALTER TABLE suppliers 
DROP COLUMN fax;
```

错误：

```sql
ERROR:  cannot drop column fax of table suppliers because other objects depend on it
DETAIL:  constraint fax_messages_fax_fkey on table fax_messages depends on column fax of table suppliers
view faxes depends on column fax of table suppliers
HINT:  Use DROP ... CASCADE to drop the dependent objects too.
```

`PostgreSQL` 发出错误，因为fax表具有以下依赖对象：

- 外键约束 `fax_messages_fax_fkey fax_messages` 表中。
- 视图 `faxes`

要将 `fax` 列与从属对象一起放置，可以使用 `CASCADE` 选项：

最后，使用 `CASCADE` 选项删除 `fax` 列及其从属对象：

```sql
ALTER TABLE suppliers 
DROP COLUMN fax CASCADE;
```

通知：

```sql
NOTICE:  drop cascades to 2 other objects
DETAIL:  drop cascades to constraint fax_messages_fax_fkey on table fax_messages
drop cascades to view faxes
```

该语句从 `suppliers` 表中删除 `fax` 列，删除外键约束 `fax_messages_fax_fkey` ，视图将 `faxes` 。

# 删除多列

首先，从 `suppliers` 表中删除 `phone` 和 `email` 列：

```sql
ALTER TABLE suppliers
DROP COLUMN phone,
DROP COLUMN email;
```

其次，显示表结构以验证更改：

```sql
\d suppliers;
```

输出：

```sql
                                Table "public.suppliers"
 Column  |          Type          | Collation | Nullable |           Default
---------+------------------------+-----------+----------+------------------------------
 id      | integer                |           | not null | generated always as identity
 name    | character varying(255) |           | not null |
 address | text                   |           |          |
Indexes:
    "suppliers_pkey" PRIMARY KEY, btree (id)
```

# 总结

- 使用 `PostgreSQL ALTER TABLE DROP COLUMN` 语句从表中删除一个或多个列。
- 将 `ALTER TABLE DROP COLUMN` 语句与 `CASCADE` 选项一起使用，以删除列及其从属对象。

