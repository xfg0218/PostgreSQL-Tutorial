**总结**：在本教程中，您将学习如何使用 `ALTER TABLE RENAME TO` 语句在 `PostgreSQL` 中重命名表。

# `ALTER TABLE RENAME TO` 语句入门

在 `PostgreSQL` 中，重命名表简单明了。可以使用 `ALTER TABLE RENAME TO` 语句更改现有表的名称。

下面是 `ALTER TABLE RENAME TO` 语句的语法：

```sql
ALTER TABLE table_name
RENAME TO new_table;
```

在此语法中：

1. 首先，在 `ALTER TABLE` 子句中指定要重命名的表的名称。
2. 其次，在 `RENAME TO` 子句中提供新表名。

如果 `table_name` 不存在，您将遇到错误。

要仅在表存在时更改表的结构，可以使用 `IF EXISTS `选项：

```sql
ALTER TABLE IF EXISTS table_name
RENAME TO new_table;
```

使用 `IF EXISTS` 语句时，如果 `table_name` 不存在，您将收到通知而不是错误。

如果表具有依赖对象，例如视图和外键约束，则重命名表将自动更改依赖对象。

例如，如果视图引用了一个表，而你重命名了它，`PostgreSQL` 也会在视图定义中更改表名。

# 重命名表示例

首先，打开终端并连接到 `PostgreSQL` 服务器：

```sql
psql -U postgres
```

其次，创建名为 `vendors`  `groups` 的新表：

```sql
CREATE TABLE groups(   
    group_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    group_name VARCHAR(255) NOT NULL
);

CREATE TABLE vendors(
    vendor_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    vendor_name VARCHAR(255) NOT NULL,
    group_id INT NOT NULL,
    FOREIGN KEY(group_id) REFERENCES groups(group_id) ON DELETE CASCADE 
);
```

第三，将表 `groups` 重命名为 `vendor_groups`：

```sql
ALTER TABLE groups
RENAME TO vendor_groups;
```

最后，验证更改：

```sql
\d vendor_groups
```

输出：

```sql
                               Table "public.vendor_groups"
   Column   |          Type          | Collation | Nullable |           Default
------------+------------------------+-----------+----------+------------------------------
 group_id   | integer                |           | not null | generated always as identity
 group_name | character varying(255) |           | not null |
Indexes:
    "groups_pkey" PRIMARY KEY, btree (group_id)
Referenced by:
    TABLE "vendors" CONSTRAINT "vendors_group_id_fkey" FOREIGN KEY (group_id) REFERENCES vendor_groups(group_id) ON DELETE CASCADE
```

# 总结

- 使用 `PostgreSQL ALTER TABLE RENAME TO` 语句重命名表。
