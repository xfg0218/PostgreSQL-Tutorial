**摘要**：在本教程中，您将学习如何使用 `PostgreSQL CREATE FUNCTION` 语句创建用户定义的函数。

# `PostgreSQL CREATE FUNCTION` 语句入门

函数是执行特定任务的可重用代码片段。例如，`CONCAT()` 函数允许您将两个或多个字符串连接成一个字符串。`CONCAT()` 是 `PostgreSQL` 提供的内置函数。

`PostgreSQL` 允许您使用 `CREATE FUNCTION` 语句创建新函数。这个函数被称为用户定义函数，因为它是由你和其他开发人员创建的，而不是由 `PostgreSQL` 开箱即用的。

用户定义函数是执行特定任务的自定义函数，如内置函数。

此函数接受输入参数，执行一个或多个 `SQL` 语句，并返回一个或多个值。

通常，您编写一个用户定义的函数来封装复杂的逻辑，并使其在数据库中可重用。

下面显示 `CREATE FUNCTION` 语句的基本语法：

```sql
CREATE [OR REPLACE] FUNCTION function_name (parameters)
RETURNS return_type 
AS 
$$
  -- function body
$$ 
LANGUAGE SQL;
```

在此语法中：

- `CREATE [OR REPLACE] FUNCTION` 指示 `PostgreSQL` 创建一个新函数。如果函数名称已存在，则 `OR REPLACE` 选项将替换它。
- `function_name (parameters)` 是函数名称，后跟参数。
- `RETURNS return_type` 指定函数将返回的值的数据类型。如果函数不返回任何值，则 `return_type` 为 `VOID`
- `AS $ ... $` 表示包含在美元引号字符串 (`$`) 中的函数体。
- `LANGUAGE SQL` 指定函数正在使用 `SQL` 。除了 `SQL` 之外，您还可以使用其他过程编程语言，例如 `PL/pgSQL` 。

# 美元引号字符串文字 `($$)`

`PostgreSQL` 需要函数体作为字符串。如果使用常规字符串，则需要转义引号和其他特殊字符。

为了使函数体更具可读性，`PostgreSQL` 提供了美元引号的字符串语法：

```sql
$tag$<string_constant>$tag$
```

在此语法中：

- `tag` 是可选标识符。
- 在 `$tag$s` 之间，可以放置一个字符串常量，即函数体。而且您不必转义引号或特殊字符。

# 调用用户定义的函数

要调用用户定义的函数，请使用 `SELECT` 语句，后跟函数名称和参数：

```sql
SELECT function_name(arguments);
```

在此语法中，参数可以是与 `function_name` 中定义 `arguments` 参数相对应的一个或多个参数。

# `PostgreSQL CREATE FUNCTION` 语句示例

让我们探讨一些创建新用户定义函数的示例。

## 创建一个不返回值的函数

以下示例使用 `CREATE FUNCTION` 语句创建一个函数，该函数添加具有名称和位置的新仓库：

```sql
CREATE OR REPLACE FUNCTION add_warehouse (
  name VARCHAR, 
  location VARCHAR
) 
RETURNS VOID 
AS 
$$
    INSERT INTO warehouses(warehouse_name, address)
    VALUES(name,location);
$$ 
LANGUAGE SQL;
```

`add_warehouse` 函数采用两个参数：

- `name` 类型为 `VARCHAR` 表示仓库名称。
- `location` 具有相同的 `VARCHAR` 数据类型，表示仓库的地址。

不能将参数名称与函数内使用的表的列名相同。如果这样做，`PostgreSQL` 将发出错误。原因是 `PostgreSQL` 会混淆参数和列名。

`RETURNS VOID` 表示 `add_warehouse` 函数不返回任何值。

> 请注意，`RETURNS` 关键字包含字母 `S` ，而不是 `RETURN` 。

`AS` 关键字告诉函数体的开头。

`$` 之间是一个用美元引号括起来的字符串，表示函数体。

在函数体中，我们使用一个 `INSERT` 语句，将新行插入 `warehouses` 

```sql
INSERT INTO warehouses (warehouse_name, address)
VALUES(name, location);
```

在 `VALUES` 子句中，我们使用参数 `name` 和 `location` 。

`LANGUAGE SQL` 子句指示该函数使用 `SQL` 。

> 请注意，您可以用小写写法编写所有代码，包括 `create or replace function` 等关键字。

## 调用 `add_warehouse` 函数

首先，调用 `add_warehouse` 函数将新仓库添加到 `warehouses` 表中：

```sql
SELECT
  add_warehouse (
    'San Mateo',
    '2222 S Delaware St, San Mateo, CA 94403'
  );
```

其次，从 `warehouses` 表中检索数据以验证函数调用：

```sql
SELECT
  warehouse_name,
  address
FROM
  warehouses;
```

输出：

```sql
     warehouse_name      |                     address
-------------------------+-------------------------------------------------
 San Jose Warehouse      | 205 E Alma Ave, San Jose, CA 95112
 San Francisco Warehouse | 233 E Harris Ave, South San Francisco, CA 94080
 Los Angeles Warehouse   | 1919 Vineburn Avenue, Los Angeles, CA 90032
 San Mateo               | 2222 S Delaware St, San Mateo, CA 94403
```

输出在 `warehouses` 表中显示新仓库。

## 创建一个返回单个值的函数

以下示例使用 `CREATE FUNCTION` 语句创建返回总库存量的 `get_inventory_amount()` ：

```sql
CREATE OR REPLACE FUNCTION get_inventory_amount () 
  RETURNS DEC 
AS 
$$
SELECT
  SUM(quantity * price)
FROM
  inventories
  JOIN products USING (product_id);
$$ 
LANGUAGE SQL;
```

在此示例中，`get_inventory_amount()` 函数没有参数，并返回一个类型为 `DECIMAL` 或 `DEC` 的数字。

`get_inventory_amount` 函数执行一个 `SQL` 语句，该语句连接 `inventories` 和 `products` 表，并使用 `SUM` 聚合函数计算总库存金额。

以下语句调用 `get_inventory_amount()` 函数：

```sql
SELECT
  get_inventory_amount () AS inventory_amount;
```

输出：

```sql
 inventory_amount
------------------
       6843946.40
```

## 创建返回复合类型值的函数。

在 `PostgreSQL` 中，您可以定义一种复合类型，将多个字段分组为一个单元。`PostgreSQL` 复合类型的工作方式类似于其他编程语言中的记录或结构。

首先，定义一个名为 `product_info` 的复合类型，该类型由产品名称和价格组成：

```sql
CREATE TYPE product_info AS (
    product_name VARCHAR, 
    price DECIMAL
);
```

其次，创建一个返回 `product_info` 复合类型的值的函数：

```sql
CREATE OR REPLACE FUNCTION get_product_info (id INT) 
  RETURNS product_info 
AS 
$$
SELECT product_name, price
FROM products
WHERE product_id = id;
$$ 
LANGUAGE SQL;
```

在此示例中，函数 `get_product_info` 返回复合类型 `product_info` 的值，其中包括 `product_name` 和 `price` 。

第三，调用 `get_product_info` 检索产品 `ID 1` 的信息：

```sql
SELECT get_product_info(1);
```

输出：

```sql
       get_product_info
-------------------------------
 ("Samsung Galaxy S24",999.99)
```

该语句返回 `product_info` 复合类型的值。

要从此复合值中选择字段，可以将函数调用放在 `FROM` 子句中：

```sql
SELECT * FROM get_product_info(1);
```

输出：

```sql
    product_name    | price
--------------------+--------
 Samsung Galaxy S24 | 999.99
```

# 总结

- 使用 `CREATE FUNCTION` 语句创建新的用户定义函数。