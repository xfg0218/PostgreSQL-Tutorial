**摘要**：在本教程中，您将学习如何使用 `PostgreSQL ALTER TABLE ... DISABLE TRIGGER` 语句来禁用触发器。

# 如何禁用触发器

有时，您可能想要关闭触发器。例如，由于每个插入行的附加作，触发器在执行批量插入时可能会造成大量开销。

若要禁用触发器，可以使用具有以下语法的 `ALTER TABLE DISABLE TRIGGER` 语句：

```sql
ALTER TABLE table_name
DISABLE TRIGGER trigger_name | ALL;
```

在此语法中：

- 首先，在 `ALTER TABLE` 子句中指定触发器所属的表名。
- 其次，在 `DISABLE TRIGGER` 子句中提供要禁用的触发器名称。如果要关闭所有表触发器，可以使用 `ALL` 关键字，而不是单独禁用触发器。

# 禁用触发器示例

首先，创建一个表来记录 `products` 表中的价格变化：

```sql
CREATE TABLE price_change_logs (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    product_id INT NOT NULL,
    old_price DECIMAL(11,2) NOT NULL,
    new_price DECIMAL(11, 2) NOT NULL,
    changed_at TIMESTAMPTZ DEFAULT NOW(),
    FOREIGN KEY (product_id) REFERENCES products (product_id) ON DELETE CASCADE
);
```

其次，创建一个触发器函数，将价格变化记录到 `price_change_logs` 表中：

```sql
CREATE OR REPLACE FUNCTION log_price_changes()
RETURNS TRIGGER 
AS 
$$
BEGIN
    IF NEW.price <> OLD.price THEN
        INSERT INTO price_change_logs (product_id, old_price, new_price)
        VALUES (OLD.product_id, OLD.price, NEW.price);
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

第三，创建一个触发器，在 `products` 表中的价格后执行 `log_price_changes` 函数：

```sql
CREATE TRIGGER price_update_trigger
AFTER UPDATE OF price ON products
FOR EACH ROW
EXECUTE FUNCTION log_price_changes();
```

第四，将 `id` 为 `1` 的产品价格更新为 `799.99`：

```sql
UPDATE products
SET price = 799.99
WHERE product_id = 1;
```

`update` 语句会导致触发器 `price_update_trigger`，从而执行 `log_price_changes` 函数。该函数将价格变化记录到 `price_change_logs` 表中。

第五，从 `price_change_logs` 表中检索数据：

```sql
SELECT
  product_id,
  old_price,
  new_price
FROM
  price_change_logs;
```

输出：

```sql
 product_id | old_price | new_price
------------+-----------+-----------
          1 |    999.99 |    799.99
```

第六，禁用 `products` 表的触发 `price_update_trigger`：

```sql
ALTER TABLE products
DISABLE TRIGGER price_update_trigger;
```

第七，将产品 `ID 2` 的价格更新为 `999.99`：

```sql
UPDATE products
SET price = 999.99
WHERE product_id = 2;
```

第八，从 `price_change_logs` 表中检索数据：

```sql
SELECT
  product_id,
  old_price,
  new_price
FROM
  price_change_logs
WHERE
  product_id = 2;
```

它没有产品 `ID 2` 的日志，因为触发器已禁用。

最后，禁用 `products` 表的所有触发器：

```sql
ALTER TABLE products 
DISABLE TRIGGER ALL;
```

# 启用触发器

要启用触发器，请使用 `ALTER TABLE ... ENABLE TRIGGER` 语句：

```sql
ALTER TABLE table_name
ENABLE TRIGGER trigger_name | ALL;
```

在此语法中：

- 首先，在 `ALTER TABLE` 子句中提供触发器所属的表名。
- 其次，在 `ENABLE TRIGGER` 子句中指定要启用的触发器名称。您可以使用 `ALL` 关键字启用表的所有触发器。

例如，以下语句启用 `products` 表的触发器 `price_update_trigger` ：

```sql
ALTER TABLE products
ENABLE TRIGGER price_update_trigger;
```

以下示例启用 `products` 表的所有触发器：

```sql
ALTER TABLE products
ENABLE TRIGGER price_update_trigger;
```

# 总结

- 使用 `PostgreSQL ALTER TABLE ... DISABLE TRIGGER` 语句来禁用触发器。
- 使用 `PostgreSQL ALTER TABLE ... ENABLE TRIGGER` 语句来启用触发器。