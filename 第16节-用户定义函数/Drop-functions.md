**摘要**：在本教程中，您将学习如何使用 `PostgreSQL DROP FUNCTION` 语句从数据库中删除函数。

# `PostgreSQL DROP FUNCTION` 语句入门

`DROP FUNCTION` 语句允许您从数据库中永久删除用户定义的函数。

以下是 `DROP FUNCTION` 语句的语法：

```sql
DROP FUNCTION [IF EXISTS] function_name(parameters);
```

在此语法中：

- 首先，在 `DROP FUNCTION` 关键字后指定函数名称，并列出参数。
- 其次，如果要有条件地删除函数，请使用 `IF EXISTS` 子句，前提是它存在。

如果函数具有运算符和触发器等依赖对象，则不能删除它。

幸运的是，您可以通过显式使用 `CASCADE` 选项删除函数及其依赖对象：

```sql
DROP FUNCTION [IF EXISTS] function_name(parameters) CASCADE;
```

`RESTRICT` 选项可防止删除具有依赖对象的函数。默认情况下，`DROP FUNCTION` 语句使用 `RESTRICT` 选项。

要一次删除多个函数，可以在 `DROP FUNCTION` 关键字后指定一个以逗号分隔的函数名称列表：

```sql
DROP FUNCTION 
    function_name1(parameter), 
    function_name2(parameter), ...;
```

# `PostgreSQL DROP FUNCTION` 语句示例

让我们创建一些函数并演示删除它们的 `DROP FUNCTION` 语句。

首先，创建一个返回总库存数量的函数：

```sql
CREATE OR REPLACE FUNCTION get_total_inventory() 
    RETURNS int 
AS 
$$
    SELECT sum(quantity) FROM inventories;
$$ LANGUAGE sql;
```

其次，创建一个函数，返回一定范围内的产品、仓库和库存数量：

```sql
CREATE OR REPLACE FUNCTION get_inventory(min_qty int, max_qty int) 
RETURNS TABLE (product varchar, warehouse varchar, quantity int) 
AS 
$$
    SELECT product_name, warehouse_name, quantity 
    FROM inventories
    JOIN products USING (product_id) 
    JOIN warehouses USING (warehouse_id)
    WHERE quantity >= min_qty AND quantity <= max_qty;
$$ LANGUAGE sql;
```

第三，创建一个重载函数以特定数量退回库存：

```sql
CREATE OR REPLACE FUNCTION get_inventory(qty int) 
RETURNS TABLE (product varchar, warehouse varchar, quantity int) 
AS $$
    SELECT product_name, warehouse_name, quantity 
    FROM inventories
    JOIN products USING (product_id) 
    JOIN warehouses USING (warehouse_id)
    WHERE quantity = qty;
$$ LANGUAGE sql;
```

最后，创建两个简单的函数，用于加减两个整数：

```sql
CREATE OR REPLACE FUNCTION add(a int, b int) 
RETURNS INT 
AS 
$$
    SELECT a + b;
$$ LANGUAGE SQL;

CREATE OR REPLACE FUNCTION subtract(a int, b int) 
RETURNS INT 
AS
$$
    SELECT a - b;
$$ LANGUAGE SQL;
```

# 基本 `PostgreSQL DROP FUNCTION` 语句示例

以下 `DROP FUNCTION` 语句删除 `get_total_inventory` 函数：

```sql
DROP FUNCTION IF EXISTS get_total_inventory;
```

由于没有其他函数与 `get_total_inventory` 函数同名，因此我们不需要指定参数列表。

# 删除重载函数示例

以下 `DROP FUNCTION` 语句尝试删除 `get_inventory` 函数：

```sql
DROP FUNCTION IF EXISTS get_inventory;
```

它发出错误，因为两个函数具有相同的名称 `get_inventory` ：

```sql
function name "get_inventory" is not unique
```

要修复它，您需要显式指定参数列表，以便 `PostgreSQL` 知道您要删除哪个函数。

例如，以下语句删除接受整数 (`int`)  参数的 `get_inventory` 函数：

```sql
DROP FUNCTION IF EXISTS get_inventory(int);
```

# 删除多个函数示例

以下示例使用 `DROP FUNCTION` 语句一次删除两个函数：

```sql
DROP FUNCTION add, subtract;
```

# 总结

- 使用 `PostgreSQL DROP FUNCTION` 从数据库中删除函数。
