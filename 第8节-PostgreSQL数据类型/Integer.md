**摘要**：在本教程中，你将了解 `PostgreSQL` 的整数类型，包括 `SMALLINT` 、`INTEGER` 和 `BIGINT` ，用于在数据库中存储整数。

# `PostgreSQL` 整数类型入门

`PostgreSQL` 支持以下用于存储整数的整数数据类型：

- `SMALLINT`
- `INTEGER`
- `BIGINT`

下表列出了每种整数类型的规格：

| Name | Storage Size | Minimum Value | Maximum Value |
|:----:|:---::|:---:|:---:|
| SMALLINT | 2 bytes | -32768 | 32767 |
| INTEGER | 4 bytes | -2147483648 | 2147483647 |
| BIGINT | 8 bytes | -9223372036854775808 | 9223372036854775807 |

如果您尝试插入或更新超出允许范围的值，`PostgreSQL` 会生成错误。

# SMALLINT

`SMALLINT` 数据类型需要2字节的存储空间，可存储的整数值范围为 `-32,767` 到 `32,767` 。

实际上，你可以使用 `SMALLINT` 类型来存储小范围的整数值，例如学生成绩、产品数量和人们的年龄。

例如，下面的语句 `CREATE TABLE` 语句创建了一个 `people` 表，用于存储包括姓名和年龄在内的个人信息： 

```sql
CREATE TABLE people (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  age SMALLINT NOT NULL CHECK (
    age >= 1
    AND age <= 150
  )
);
```

在 `people` 表中，`age` 列是一个 `SMALLINT` 列。由于 `age` 的取值范围可以是 `1` 到 `150` ，我们使用 `CHECK` 约束来执行这一规则。

# INTEGER

`INTEGER` 数据类型是整数类型中最常见的选择，因为它在存储大小、范围和性能之间实现了最佳平衡。

`INTEGER` 类型需要 4 字节的存储空间，可存储的数字范围为 `-2,147,483,648` 至 `2,147,483,647`。

`INT` 是 `INTEGER` 的同义词，因此您可以互换使用它们。

实际上，对于存储相当大的整数的列，你可以使用 `INTEGER` 数据类型。

例如，下面创建了一个包含 `INTEGER` 列的表： 

```sql
CREATE TABLE inventories (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  quantity INTEGER NOT NULL CHECK (quantity > 0)
);
```

# BIGINT

`BIGINT` 类型需要 8 字节的存储空间，可存储的数字范围是从 `-9,223,372,036,854,775,808` 到 `+9,223,372,036,854,775,807` 。

例如，以下代码创建了一个 `videos` 表，用于存储包含视频观看量在内的视频数据：

```sql
CREATE TABLE videos (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  view_count BIGINT CHECK (view_count > 0)
);
```

请注意，`BIGINT` 数据类型比 `SMALLINT` 和 `INT` 消耗更多存储空间，并且可能会降低数据库性能。

# Summary

- 使用 `PostgreSQL` 整数类型（包括 `SMALLINT` 、`INT` 和 `BIGINT` ）在数据库中存储整数。