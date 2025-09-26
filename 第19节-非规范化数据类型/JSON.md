**摘要**：在本教程中，您将学习如何使用 `PostgreSQL JSON` 数据类型将 `JSON` 数据存储在数据库中。

# `JSON` 概述

`JSON`（即 `JavaScript` 对象表示法）是一种轻量级的数据交换格式，开发人员易于使用，计算机也易于管理。

以下是 `JSON` 的主要功能：

- **Human-readable**：`JSON` 使用简单的文本格式。
- **Lightweight**：`JSON` 具有最小的语法和结构。
- **Structured data**：`JSON` 使用数组和对象来表示数据。
- **Language-dependent**：`JSON` 可与许多编程语言无缝协作。

以下是您发现 `JSON` 有用的典型方案：

- RESTful API
- 配置文件（.json格式）
- 数据存储

`JSON` 使用两种结构来存储数据：

- **Objects**：用大括号 （`{}`） 括起来的键值对的集合。
- **Arrays**：括在方括号 (`[]`) 中的值的顺序列表。

对象和数组可以嵌套和混合。

# Objects

`JSON` 对象是用大括号括起来的键值对列表。每对包括：

- 键：出现在双引号(``)
- 值：是任何有效的 `JSON` 值，例如数字、字符串、对象、数组等。
- 冒号(``)分隔键和值。

例如，以下显示了一个 `JSON` 对象：

```sql
{"product_name": "iPhone 16", "price": 1299.99 }
```

`JSON` 对象有两个键，`product_name` 和 `price`，以及相应的值。

# Arrays

`JSON` 数组是括在方括号 （`[]`） 中的有序列表。这些值可以有不同的类型。

例如，下面是一个存储产品功能的 `JSON` 数组：

```sql
["Camera", "Face Recognition", "AI"]
```

# `JSON` 数据类型

除了对象和数组之外，`JSON` 还支持以下简单数据类型：

- string
- number
- boolean
- null

# `PostgreSQL JSON` 数据类型

`PostgreSQL` 有两种用于存储 `JSON` 的内置数据类型：

- `JSON`：存储 `JSON` 数据的精确副本。
- `JSONB`：以二进制格式存储 `JSON` 数据。

下表显示了 `PostgreSQL` 中 `JSON` 和 `JSONB` 类型之间的主要区别：

| Feature | JSON | JSONB |
|:----:|:----:|:----:|
| Storage | Text | Binary storage format |
| Size | 更大，因为 PostgreSQL 必须保留空格。 | Smaller |
| Indexing | 全文搜索索引 | 二进制索引 |
| Query performance | 由于解析速度较慢 | 由于二进制存储而更快 |
| Parsing | 每次解析 | 解析一次，以二进制格式存储 |
| Ordering of keys | 保存 | 未保存 |
| Duplicate keys | 允许重复键，保留最后一个值 | 不允许重复的键。 |
| Use cases | 仅当您想要保留键的顺序时。 | 在需要快速查询和索引的地方存储 JSON 文档 |

`PostgreSQL` 建议使用 `JSONB` 数据类型来实现更小的存储和更好的查询效率。`PostgreSQL` 提供 `JSONPATH` 数据类型，使查询 `JSON` 数据更加高效。

# `PostgreSQL JSONB` 示例

首先，创建一个名为 `phones 的新表来存储手机数据：

```sql
CREATE TABLE phones (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  properties JSONB
);
```

`properties` 列的数据类型是 `JSONB`。

其次，将三行包含 `JSON` 数据插入到 `phones` 表中：

```sql
INSERT INTO phones(name, properties ) VALUES
('iPhone 16 Pro', 
 '{
    "display": "6.1-inch OLED",
    "features": ["Face ID", "ProMotion 120Hz", "MagSafe", "iOS 18"]
 }'),

('Galaxy S23 Ultra', 
 '{
    "display": "6.8-inch AMOLED",
    "features": ["S Pen", "120Hz Display", "One UI 5", "Wireless Charging"]
 }'),

('Pixel 7 Pro', 
 '{
    "display": "6.7-inch OLED",
    "features": ["Magic Eraser", "Pure Android Experience", "Face Unlock", "5G Connectivity"]
 }');
```

第三，从 `phones` 表中检索数据：

```sql
SELECT
  id,
  name,
  properties
FROM
  phones;
```

输出：

```sql
 id |       name       |                                                       properties

----+------------------+-------------------------------------------------------------------------------------------------------------------------
  1 | iPhone 16 Pro    | {"display": "6.1-inch OLED", "features": ["Face ID", "ProMotion 120Hz", "MagSafe", "iOS 18"]}
  2 | Galaxy S23 Ultra | {"display": "6.8-inch AMOLED", "features": ["S Pen", "120Hz Display", "One UI 5", "Wireless Charging"]}
  3 | Pixel 7 Pro      | {"display": "6.7-inch OLED", "features": ["Magic Eraser", "Pure Android Experience", "Face Unlock", "5G Connectivity"]}
```

第四，用 `display` 键取回手机：

```sql
SELECT
  id,
  name,
  properties -> 'display' AS display
FROM
  phones;
```

输出：

```sql
 id |       name       |      display
----+------------------+-------------------
  1 | iPhone 16 Pro    | "6.1-inch OLED"
  2 | Galaxy S23 Ultra | "6.8-inch AMOLED"
  3 | Pixel 7 Pro      | "6.7-inch OLED"
```

在此示例中，我们使用运算符 `->` 通过键提取 `JSON` 值。`JSON` 值用双引号括起来。

若要将 `JSON` 值作为文本返回，请使用运算符 `->>`。例如：

```sql
SELECT
  id,
  name,
  properties ->> 'display' AS display
FROM
  phones;
```

输出：

```sql
 id |       name       |     display
----+------------------+-----------------
  1 | iPhone 16 Pro    | 6.1-inch OLED
  2 | Galaxy S23 Ultra | 6.8-inch AMOLED
  3 | Pixel 7 Pro      | 6.7-inch OLED
```

最后，检索每部手机的主要功能：

```sql
SELECT
  id,
  name,
  properties -> 'features' ->> 0 AS main_feature
FROM
  phones;
```

输出：

```sql
 id |       name       | main_feature
----+------------------+--------------
  1 | iPhone 16 Pro    | Face ID
  2 | Galaxy S23 Ultra | S Pen
  3 | Pixel 7 Pro      | Magic Eraser
```

- `properties -> features` 返回一个 `JSON` 数组。
- `properties -> features ->> 0` 将 `JSON` 数组的第一个元素作为文本返回。

# 总结

- 使用 `JSONB` 数据类型来存储数据。
- 使用运算符 `->` 通过键从 `JSON` 对象中提取 `JSON` 值。
- 使用运算符 `->>` 将 `JSON` 值提取为文本。