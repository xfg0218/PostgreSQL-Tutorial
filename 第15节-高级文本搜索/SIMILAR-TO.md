**摘要**：在本教程中，您将学习如何使用 `PostgreSQL SIMILAR TO` 运算符在与正则表达式匹配的列中搜索特定模式。

> 请注意，您需要对正则表达式有基本的了解才能继续本教程。

# ``PostgreSQL` 概述类似于 `Operator` 

在 `PostgreSQL` 中，如果正则表达式与整个字符串匹配，则 `SIMILAR TO` 运算符返回 `true` 。

下面是 `SIMILAR TO` 运算符的语法：

```sql
SELECT
  column1,
  column2
FROM
  table_name
WHERE
  column1 SIMILAR TO pattern;
```

在此语法中，仅当模式中的值与 `column1` 中的整个值匹配时，`SIMILAR TO` 运算符才返回 `true` ，否则返回 `false` 。

它不同于将字符串与正则表达式匹配的预期行为，正则表达式可以匹配字符串的任何部分。

除了正则表达式中使用的元字符外，还可以使用 `_` 和 `%` 通配符分别表示单个字符和零个或多个字符。

通常，点 （ `.` ） 字符是匹配除换行符之外的任何单个字符的元字符，但 `SIMILAR TO` 运算符不会将其视为元字符。

# 类似于 `Operator` 的 `PostgreSQL` 示例

让我们举一些在 `products` 表中使用 `SIMILAR TO` 运算符的示例：

![images](../images/poerator.png)

以下 SELECT 语句使用 SIMILAR TO 运算符来选择名称与正则表达式匹配的产品：

```sql
SELECT
  product_name
FROM
  products
WHERE
  product_name SIMILAR TO '\w+\s*(Galaxy|iPhone)\s*\w+';
```

输出：

```sql
    product_name
--------------------
 Samsung Galaxy S24
 Apple iPhone 15
```

让我们分解正则表达式 `\w+\s*(Galaxy|iPhone)\s*\w+`：

- `\w+` 匹配一个或多个单词。
- `\s*` 匹配零个或多个空格。
- `(Galaxy|iPhone)` 与单词 `Galaxy` 或 `iPhone` 匹配。
- `\s*` 匹配零个或多个空格。
- `\w+` 匹配一个或多个单词。

因此，正则表达式匹配以一个或多个单词、一个空格（ `Galaxy` 或 `iPhone` ）、一个空格和一个或多个单词开头的任何产品名称。

# 使用 `NOT SIMILAR TO` 运算符

要否定 `SIMILAR TO` 运算符，请使用 `NOT` 运算符：

```sql
SELECT
  column1,
  column2
FROM
  table_name
WHERE
  column1 NOT SIMILAR TO pattern;
```

如果 `column1` 中的值与模式不匹配，则  `NOT SIMILAR TO` 运算符返回 `true` ，否则返回 `false` 。

例如，以下查询使用 `NOT SIMILAR TO` 运算符检索名称不以一个或多个数字结尾的所有产品的产品名称：

```sql
SELECT
  product_name
FROM
  products
WHERE
  product_name NOT SIMILAR TO '%\d+';
```

输出：

```sql
       product_name
---------------------------
 Sony Xperia 1 VI
 Apple iPhone 15 Pro Max
 Sony Bravia XR A95K
 Samsung QN900C Neo QLED
 LG G3 OLED
 Sony HT-A7000 Soundbar
 Bose SoundLink Max
 Lenovo ThinkPad X1 Carbon
 Apple iMac 24
```

在此示例中：

- `%` 匹配零个或多个字符。请注意，`%` 是通配符，而不是正则表达式中使用的元字符。
- `\d+` 匹配一个或多个数字。

模式 `%\d+` 匹配以零个或多个字符开头并以一个或多个数字结尾的任何字符串。`NOT SIMILAR TO` 返回名称不以数字结尾的产品。

# 在 `SIMILAR TO` 运算符中使用 `ESCAPE`

与 `LIKE` 运算符一样，使用 `SIMILAR TO` 运算符时，可以使用 `ESCAPE` 选项在模式中定义转义字符：

```sql
SELECT
  column1,
  column2
FROM
  table_name
WHERE
  column1 SIMILAR TO pattern ESCAPE escape_character;
```

例如，以下语句使用带有 `ESCAPE` 选项的 `SIMILAR TO` 来查找描述包含字符 `%` 的产品：

```sql
SELECT
  product_name,
  description
FROM
  products
WHERE
  description SIMILAR TO '%$%%*' ESCAPE '$';
```

在此示例中：

- 第一个和最后一个 `%` 字符是通配符。
- 第二个 `%` 是通配符，但我们希望将其视为常规字符。为此，我们在 `ESCAPE` 选项中指定一个转义字符 `$` ，并在模式中的 `%` 之前使用转义字符 `$` 。


# 总结

- 使用 `SIMILAR TO` 运算符搜索与正则表达式匹配的值。
- 使用 `NOT` 运算符否定 `SIMILAR TO` 运算符的结果。

