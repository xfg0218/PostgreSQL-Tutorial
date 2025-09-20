**摘要**：在本教程中，您将学习如何使用 `PostgreSQL ALTER TABLE RENAME COLUMN` 语句重命名表的列。

# `PostgreSQL ALTER TABLE RENAME COLUMN` 语句入门

`ALTER TABLE RENAME COLUMN` 语句允许您重命名表列。

下面是 `ALTER TABLE RENAME COLUMN` 语句的语法：

```sql
ALTER TABLE table_name
RENAME COLUMN column_name 
TO new_column;
```

在此语法中：

- 首先，在 `ALTER TABLE` 子句中提供表的名称。
- 其次，在 `RENAME COLUMN` 子句中指定要更改的列。
- 第三，在 `TO` 子句中指定列的新列名。

为了使其更短，您可以省略 `RENAME COLUMN` 中的 `COLUMN` 关键字，如下所示：

```sql
ALTER TABLE table_name
RENAME column_name 
TO new_column;
```

如果您更改的列具有视图、外键约束、触发器、用户定义函数和存储过程等引用，则 `PostgreSQL` 会自动更改这些对象中的列名。

重命名列时，更新应用程序代码以反映这些更改至关重要。如果应用程序尝试访问旧列，可能会导致意外行为或错误。

若要简化重命名列过程，可以使用迁移库。迁移有助于有效管理架构更改。通常，迁移库允许您在部署期间同步数据库版本和应用程序代码。

# 重命名列示例 

首先，打开终端并使用 `psql` 连接到 `PostgreSQL` 服务器：

```sql
psql -U postgres
```

其次，创建一个名为 `sales_returns` 的表来存储客户的销售退货：

```sql
CREATE TABLE sales_returns (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  date DATE NOT NULL,
  product_name VARCHAR(255) NOT NULL,
  quantity INT NOT NULL DEFAULT 1,
  order_id INT,
  reason VARCHAR(255) NOT NULL
);
```

第三，将 `order_id` 列的名称更改为 `sales_order_id` ：

```sql
ALTER TABLE sales_returns 
RENAME order_id 
TO sales_order_id;
```

最后，验证更改：

```sql
\d sales_returns
```

# 重命名视图引用的列

首先，基于 `sales_returns` 表创建一个视图 `sales_return_reports` ：

```sql
CREATE VIEW sales_return_reports AS
SELECT
  product_name,
  date,
  SUM(quantity)
FROM
  sales_returns
GROUP BY
  product_name,
  date;
```

其次，将 `date` 列重命名为 `return_date` ：

```sql
ALTER TABLE sales_returns 
RENAME date 
TO return_date;
```

第三，检查 `sales_return_reports` 视图：

```sql
\d+ sales_return_reports
```

输出：

```sql
                               View "public.sales_return_reports"
    Column    |          Type          | Collation | Nullable | Default | Storage  | Description
--------------+------------------------+-----------+----------+---------+----------+-------------
 product_name | character varying(255) |           |          |         | extended |
 date         | date                   |           |          |         | plain    |
 sum          | bigint                 |           |          |         | plain    |
View definition:
 SELECT product_name,
    return_date AS date,
    sum(quantity) AS sum
   FROM sales_returns
  GROUP BY product_name, return_date;
```

输出指示视图定义中的 `date` 列更改为 `return_date` 。但是，视图仍使用 `date` 作为列别名。

# 如果存在，则重命名列

`PostgreSQL` 不支持 `ALTER TABLE RENAME COLUMN IF EXISTS` 语句的语法。如果重命名不存在的列，则会遇到错误。

如果要重命名列（如果存在），可以定义用户定义的函数：

```sql
CREATE OR REPLACE FUNCTION fn_rename_column(
    from_table VARCHAR, 
    old_column VARCHAR, 
    new_column VARCHAR
)
RETURNS VOID
AS
$$
BEGIN
    IF EXISTS (SELECT *
        FROM information_schema.columns
        WHERE table_name = from_table AND column_name = old_column)
    THEN
        ALTER TABLE from_table 
        RENAME COLUMN old_column TO new_column;
    END IF;
END $$; 
LANGUAGE plpgsql;
```

`fn_rename_column` 函数在执行 `ALTER TABLE` 语句之前检查该列是否存在。

# 总给

- 使用 `ALTER TABLE RENAME COLUMN` 语句重命名列。