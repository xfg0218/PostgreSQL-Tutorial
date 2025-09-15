**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `UUID` 类型存储`UUID` 值。

# `PostgreSQL UUID` 类型入门

`UUID` 代表通用唯一标识符，是由 `RFC 4122` 定义的标准。

`UUID` 长128位，无需中央注册。它保证了在不同系统和时间上的唯一性。`PostgreSQL` 使用 `UUID` 类型来存储 `UUID` 值。

以下是定义 `UUID` 列的语法：

```sql
CREATE TABLE table_name (
   column_name UUID,
   ...
);
```

通常，我们使用 `UUID` 作为表的主键列的类型：

```sql
CREATE TABLE table_name (
   column_name UUID PRIMARY KEY,
   ...
);
```

原因是 `UUID` 具有唯一性，且不会向公众暴露内部序列。

例如，如果您有一个ID为 `10000` 的客户，这可能会暴露您的系统有 `10000` 个客户。

当竞争对手知道你拥有的客户数量时，他们可能会利用这些信息来获得竞争优势。

使用 `UUID` 作为主键时，`PostgreSQL` 和应用程序都可以生成它，同时保持唯一性。

例如，你可以生成一个密钥（ `UUID` ）来插入新客户，并使用该密钥创建客户档案。

```sql
// Generate UUID for a customer
// Use UUID to create a customer profile
```

如果使用标识列，您必须先插入新客户，获取 `PostgreSQL` 生成的 `ID` ，然后使用该 `ID` 创建客户档案。

# 生成 `UUID` 值的函数

`PostgreSQL` 提供了 `gen_random_uuid()`，允许您生成一个 `UUID` 值。例如，以下语句生成一个 `UUID` 值：

```sql
SELECT gen_random_uuid();
```

输出：

```sql
           gen_random_uuid
--------------------------------------
 68f375c8-1b55-4558-a176-72a38a520149
```

每次执行该函数时，它都会生成一个新的 `UUID` 值。

你可以使用 `gen_random_uuid()` 函数为 `UUID` 主键列生成默认值：

```sql
CREATE TABLE table_name(
   user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
   ...
);
```

# `PostgreSQL UUID` 数据类型示例

首先，创建一个表，名为 `customers` ，该表使用 `UUID` 类型作为主键列：

```sql
CREATE TABLE customers (
  customer_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL
);
```

在 `customers` 表中，`customer_id` 列是主键列。`customer_id` 列的类型为 `UUID`。

当你向表中插入一行数据时，`PostgreSQL` 会通过调用 `gen_random_uuid()` 函数，为 `customer_id` 列自动生成 `UUID` 值。 （注：为避免标签被误解析，在标签符号与内容间添加了空格，实际使用时可去除。）

其次，向 `customers` 表中插入数据：

```sql
INSERT INTO
  customers (name)
VALUES
  ('ABC Corp.'),
  ('XYZ Inc.') 
RETURNING *;
```

输出：

```sql
             customer_id              |   name
--------------------------------------+-----------
 f1af387f-ac03-4784-bbcb-b564a88cd682 | ABC Corp.
 aae1c410-06ff-48df-818b-dbed92a1efbc | XYZ Inc.
```

# 总结

- 为表的主键列使用 `UUID` 数据类型。
- 使用 `gen_random_uuid()` 函数生成 `UUID` 值。
