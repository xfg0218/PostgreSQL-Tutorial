**摘要**：在本教程中，您将学习如何使用 `PostgreSQL DROP PROCEDURE` 从数据库中删除存储过程。

# `PostgreSQL DROP PROCEDURE` 语句简介

在 `PostgreSQL` 中，存储过程是一个命名的数据库对象，由一组可重用的 `SQL` 语句组成。可以使用 `CREATE PROCEDURE` 语句来定义存储过程。

如果存储过程不再使用，则可以使用 `DROP PROCEDURE` 语句将其从数据库中删除。

以下是 `DROP PROCEDURE` 语句的语法：

```sql
DROP PROCEDURE [IF EXISTS] procedure_name (parameter_list);
```

在此语法中：

- 首先，在 `DROP PROCEDURE` 关键字后指定要删除的存储过程名称。使用 `IF EXISTS` 子句可防止在存储过程不存在时出错。
- 其次，如果数据库具有多个同名的过程，则使用参数列表来标识存储过程。

如果您尝试删除具有依赖对象的存储过程，`PostgreSQL` 将引发错误。要删除存储过程以及依赖于它的对象，请使用 `CASCADE` 选项：

```sql
DROP PROCEDURE [IF EXISTS] procedure_name (parameter_list)
CASCADE;
```

默认情况下，`DROP PROCEDURE` 语句使用 `RESTRICT` 选项，如果该过程有任何依赖于该过程的对象，则该选项拒绝删除该过程：

```sql
DROP PROCEDURE [IF EXISTS] procedure_name (parameter_list)
RESTRICT;
```

您可以使用单个 `DROP PROCEDURE` 语句删除多个存储过程：

```sql
DROP PROCEDURE 
     procedure_name1(parameter_list),
     procedure_name2(parameter_list);
```

在此语法中，提供要在 `DROP PROCEDURE` 关键字后删除的存储过程名称的逗号分隔列表。

`PostgreSQL` 不跟踪存储过程之间的依赖关系。如果存储过程 `A` 调用存储过程 `B` ，则可以毫无问题地删除存储过程 `B` 。但是，如果执行存储过程 `A` ，则会收到错误。

# `PostgreSQL DROP PROCEDURE` 语句示例

让我们探讨一下使用 `DROP PROCEDURE` 语句的示例。

## 创建存储过程

首先，创建一个存储过程，将新品牌添加到 `brands` 表：

```sql
CREATE OR REPLACE PROCEDURE add_brand(
     p_name VARCHAR(255)
)
AS 
$$
    INSERT INTO brands (brand_name)
    VALUES (p_name);
$$
LANGUAGE SQL;
```

其次，创建一个增加清单的存储过程：

```sql
CREATE OR REPLACE PROCEDURE increase_inventory(
    p_product_id INT,
    p_warehouse_id INT,
    p_quantity INT
)
AS 
$$
    UPDATE inventories
    SET quantity = quantity + p_quantity 
    WHERE product_id = p_product_id AND warehouse_id = p_warehouse_id;
$$
LANGUAGE SQL;
```

第三，定义一个存储过程，记录将产品接收到仓库并相应地更新库存的交易：

```sql
CREATE OR REPLACE PROCEDURE receive_product(
    p_product_id INT,
    p_warehouse_id INT,
    p_user_id INT,
    p_quantity INT
)
AS 
$$
    INSERT INTO transactions (product_id, warehouse_id, user_id, type, quantity, transaction_date)
    VALUES (p_product_id, p_warehouse_id, p_user_id, 'receipt', p_quantity, CURRENT_DATE);

    CALL increase_inventory(p_product_id, p_warehouse_id, p_quantity);
$$
LANGUAGE SQL;
```

`receive_product` 存储过程调用 `increase_inventory` 存储过程。

## 删除存储过程

以下示例使用 `DROP PROCEDURE` 语句删除存储过程 `add_brand` ：

```sql
DROP PROCEDURE IF EXISTS add_brand;
```

由于 `inventory` 数据库有一个 `add_brand` 存储过程，因此我们不会在存储过程名称后使用参数列表。

## 删除调用另一个存储过程的存储过程

首先，删除 `increase_inventory` 存储过程：

```sql
DROP PROCEDURE increase_inventory;
```

它按预期工作。

其次，调用 `receive_product` 存储过程：

```sql
CALL receive_product(1, 1, 1, 10);
```

它返回以下错误：

```sql
ERROR:  procedure increase_inventory(integer, integer, integer) does not exist
```

由于我们的存储过程 `increase_inventory` 不再存在，因此 `receive_product` 存储过程失败了。

第三，删除存储过程 `receive_product` ：

```sql
DROP PROCEDURE receive_product;
```

# 总结

- 使用 `DROP PROCEDURE` 语句删除存储过程。