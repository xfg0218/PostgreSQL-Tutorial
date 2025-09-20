**摘要**：在本教程中，您将学习如何使用 `PostgreSQLALTER TABLE ADD COLUMN` 语句向表添加新列。

# `PostgreSQL ALTER TABLE ADD COLUMN` 语句入门

由于新要求，您可能需要向表添加一列或多列。在 `PostgreSQL` 中，您可以使用 `ALTER TABLE ... ADD COLUMN` 语句来执行此作。

以下是该语句的基本语法：

```sql
ALTER TABLE table_name
ADD COLUMN new_column data_type constraint;
```

在此语法中：

- 首先，在 `ALTER TABLE` 子句中提供要添加列的表的名称。
- 其次，在 `ADD COLUMN` 子句中指定新的列名、数据类型和约束。

`ALTER TABLE ... ADD COLUMN` 将新列附加到表的列列表末尾。

> `PostgreSQL` 不允许您像 `MySQL` 那样在列列表中的指定位置插入新列。但是我将很快介绍一个解决方法。

如果要同时添加多个列，可以使用多个 `ADD COLUMN` 子句：

```sql
ALTER TABLE table_name
ADD COLUMN new_column1 data_type constraint,
ADD COLUMN new_column2 data_type constraint,
ADD COLUMN new_column3 data_type constraint;
```

# 创建示例表

首先，打开终端并使用 `psql` 工具连接到 `PostgreSQL` 服务器：

```sql
psql -U postgres -d inventory
```

其次，创建一个名为 `vendors` 的新表：

```sql
CREATE TABLE vendors (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL
);
```

第三，显示 `vendors` 表：

```sql
\d vendors
```

输出：

```sql
                                Table "public.vendors"
 Column |          Type          | Collation | Nullable |           Default
--------+------------------------+-----------+----------+------------------------------
 id     | integer                |           | not null | generated always as identity
 name   | character varying(255) |           | not null |
Indexes:
    "vendors_pkey1" PRIMARY KEY, btree (id)

```

# 向表添加一列

首先，将名为 `address` 的新列添加到 `vendors` 中：

```sql
ALTER TABLE vendors
ADD COLUMN address TEXT;
```

其次，使用 `\d` 命令显示表结构：

输出：

```sql
                                 Table "public.vendors"
 Column  |          Type          | Collation | Nullable |           Default
---------+------------------------+-----------+----------+------------------------------
 id      | integer                |           | not null | generated always as identity
 name    | character varying(255) |           | not null |
 address | text                   |           |          |
Indexes:
    "vendors_pkey1" PRIMARY KEY, btree (id)
```

输出显示 `vendors` 表列列表末尾的 `address` 列。

# 向表添加多列

首先，将 `email` 和 `phone` 两列添加到 `vendors` 表中：

```sql
ALTER TABLE vendors
ADD COLUMN email VARCHAR(255) NOT NULL,
ADD COLUMN phone VARCHAR(25) NOT NULL;
```

其次，显示 `vendors` 表的结构：

```sql
\d vendors
```

输出：

```sql
                                 Table "public.vendors"
 Column  |          Type          | Collation | Nullable |           Default
---------+------------------------+-----------+----------+------------------------------
 id      | integer                |           | not null | generated always as identity
 name    | character varying(255) |           | not null |
 address | text                   |           |          |
 email   | character varying(255) |           | not null |
 phone   | character varying(25)  |           | not null |
Indexes:
    "vendors_pkey1" PRIMARY KEY, btree (id)
```

输出在列列表的末尾显示新的 `email` 和 `phone` 列。

# 将新列添加到包含数据的表

首先，在 `vendors` 表中插入三行：

```sql
INSERT INTO
  vendors (name, address, email, phone)
VALUES
  (
    'Samsung',
    '129 Samsung-ro, Yeongtong-gu, Suwon-si, Gyeonggi-do, South Korea',
    'contact@samsung.com',
    '+82-2-2255-0114'
  ),
  (
    'Apple',
    'One Apple Park Way, Cupertino, CA 95014, USA',
    'contact@apple.com',
    '+1-408-996-1010'
  ),
  (
    'Google',
    '1600 Amphitheatre Parkway, Mountain View, CA 94043, USA',
    'contact@google.com',
    '+1-650-253-0000'
  );
```

其次，将 `website` 列添加到具有 `NOT NULL` 约束的 `vendors` 中：

```sql
ALTER TABLE vendors
ADD COLUMN website VARCHAR NOT NULL;
```

`PostgreSQL` 发出以下错误：

```sql
ERROR: column "website" of relation "vendors" contains null values
```

添加 `website` 列时，该列的默认值为 `NULL` 这违反了 `NOT NULL` 约束。

要使其正常工作，您需要按照以下步骤作：

- **步骤 1** . 添加没有 NOT NULL 约束的website列。
- **步骤 2** . 更新现有行的值，以确保website列不包含 NULL。
- **步骤 3** . 将 NOT NULL 约束添加到website列。

第三，添加没有 `NOT NULL` 约束的 `website` 列：

```sql
ALTER TABLE vendors
ADD COLUMN website VARCHAR;
```

第四，验证变化：

```sql
\d vendors
```

输出：

```sql
                                 Table "public.vendors"
 Column  |          Type          | Collation | Nullable |           Default
---------+------------------------+-----------+----------+------------------------------
 id      | integer                |           | not null | generated always as identity
 name    | character varying(255) |           | not null |
 address | text                   |           |          |
 email   | character varying(255) |           | not null |
 phone   | character varying(25)  |           | not null |
 website | character varying      |           |          |
Indexes:
    "vendors_pkey1" PRIMARY KEY, btree (id)
```

第五，更新所有行的 `website` 列中的值：

```sql
UPDATE vendors
SET
  website = 'https://www.samsung.com'
WHERE
  name = 'Samsung';

UPDATE vendors
SET
  website = 'https://www.apple.com'
WHERE
  name = 'Apple';

UPDATE vendors
SET
  website = 'https://www.google.com'
WHERE
  name = 'Google';
```

如果您有大量数据需要更新，您可以编写一个脚本，从外部源（API、CSV 文件等）读取数据并将其加载到 `website` 列。

第六，在 `website` 列中添加 `NOT NULL` 约束：

```sql
ALTER TABLE vendors
ALTER COLUMN website SET NOT NULL;
```

最后，验证更改：

```sql
\d vendors
```

输出：

```sql
                                 Table "public.vendors"
 Column  |          Type          | Collation | Nullable |           Default
---------+------------------------+-----------+----------+------------------------------
 id      | integer                |           | not null | generated always as identity
 name    | character varying(255) |           | not null |
 address | text                   |           |          |
 email   | character varying(255) |           | not null |
 phone   | character varying(25)  |           | not null |
 website | character varying      |           | not null |
Indexes:
    "vendors_pkey1" PRIMARY KEY, btree (id)
```

# `PostgreSQL ALTER TABLE ADD COLUMN` 在特定位置添加列

`PostgreSQL` 不支持在指定位置添加新列。幸运的是，您有一个解决方法：

- 步骤 1.将现有表重命名为新表。
- 步骤 2.使用所需的列顺序重新创建表。
- 步骤 3.将数据从旧表复制到新表。
- 步骤 4.删除旧表。

例如，我们将在 `vendors` 表的 `phone` 列后面添加一个 `contact_person` ：

首先，重命名 `vendors` 表：

```sql
ALTER TABLE vendors
RENAME TO vendors_copy;
```

其次，在 `phone` 列后面使用新的 `contact_person` 列重新创建 `vendors` 表：

```sql
CREATE TABLE vendors (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  address TEXT,
  email VARCHAR(255) NOT NULL,
  phone VARCHAR(25) NOT NULL,
  contact_person VARCHAR(255),
  website VARCHAR NOT NULL
);
```

第三，将数据从 `vendors_copy` 复制到 `vendors` 表：

```sql
INSERT INTO
  vendors (
    id,
    name,
    address,
    email,
    phone,
    contact_person,
    website
  ) 
OVERRIDING SYSTEM VALUE
SELECT
  id,
  name,
  address,
  email,
  phone,
  NULL,
  website
FROM
  vendors_copy;
```

子句 `OVERRIDING SYSTEM VALUE` 允许将值插入具有 `GENERATED ALWAYS AS IDENTITY IDENTITY` 约束的标识列中。

`contact_person` 将 `NULL` 作为默认值。

第五，验证 `vendors` 表的列布局：

```sql
\d vendors
```

输出：

```sql
                                    Table "public.vendors"
     Column     |          Type          | Collation | Nullable |           Default
----------------+------------------------+-----------+----------+------------------------------
 id             | integer                |           | not null | generated always as identity
 name           | character varying(255) |           | not null |
 address        | text                   |           |          |
 email          | character varying(255) |           | not null |
 phone          | character varying(25)  |           | not null |
 contact_person | character varying(255) |           |          |
 website        | character varying      |           | not null |
Indexes:
    "vendors_pkey" PRIMARY KEY, btree (id)
```

最后，删除旧表 `vendors_copy`：

```sql
DROP TABLE vendors_copy;
```

# 使用迁移库

如果要向生产数据库中的表添加列，则应使用迁移库，以便代码和数据库结构同时进入生产环境。如果您在更改代码之前更改表结构，应用程序可能无法按预期工作。

例如，如果您有一个将数据插入表的 `API` 。该表有一个新的 `NOT NULL` 列，但 `API` 不需要它。其他使用 `API` 的系统可以通过应用层的验证检查，但在到达数据库时失败。

# 总结

- 使用 `ALTER TABLE ... ADD COLUMN` 语句向表添加一列或多列。