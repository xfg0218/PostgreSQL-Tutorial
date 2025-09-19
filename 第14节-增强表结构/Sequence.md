**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 序列对象生成唯一的整数值。

# `PostgreSQL` 序列入门

在 `PostgreSQL` 中，序列是生成唯一整数值的数据库对象。

要创建新序列，请使用 `CREATE SEQUENCE` 语句。以下是基本语法：

```sql
CREATE SEQUENCE sequence_name
    [START WITH start_value]
    [INCREMENT BY increment_value]
    [MINVALUE min_value | NO MINVALUE]
    [MAXVALUE max_value | NO MAXVALUE]
    [CYCLE | NO CYCLE]
    [CACHE cache_value | NO CACHE]
    [OWNED BY table_name.column_name ];
```

在此语法中：

- `sequence_name` 定义了一个序列名称，该序列名称在数据库中必须是唯一的。
- `START WITH start_value` 指定序列的起始值 - `start_value` 默认为 1。
- `INCREMENT BY increment_value` 确定要递增序列的值。`increment_value` 默认值为 1。
- `MINVALUE min_value` 设置序列的最小值。使用 `NO MINVALUE` 将默认值设置为 `1` 表示升序，将默认值设置为 `-1` 表示降序序列。
- `MAXVALUE max_value` 设置序列的最大值。使用 `NO MAXVALUE` 将 `max_value` 的默认值设置为升序的最大正整 `-1` 序序列的最大正整数。
- `CYCLE` 指示序列在达到限制时应重新启动升序序列的 `MINVALUE` 或降序序列的 `MAXVALUE` 。如果序列值达到限制，则使用 `NO CYCLE` 抛出错误。
- `CACHE cache_value` 指示 `PostgreSQL` 在内存中预先分配和存储多个序列号以加快访问速度—— `cache_value` 默认为 `1` 。如果要关闭缓存，请使用 `NO CACHE` 。
- `OWNED BY table_name.column_name` 将序列与表列相关联。

# 序列函数

`PostgreSQL` 提供了一些有用的函数来处理序列：

- `nextval('sequence_name')` 将序列递增到下一个值并返回该值。
- `currval('sequence_name')` 返回 `nextval()` 函数为当前会话中的序列最近获得的值。
- `setval('sequence_name', value, is_called)` 手动设置序列的当前值。
- `lastval()` 返回 `nextval()` 函数最近在当前会话中生成的值。

# 创建升序示例

首先，创建一个新的升序序列 `（asc_seq）` ，从 `1` 开始，以 `1` 递增：

```sql
CREATE SEQUENCE asc_seq
START WITH 1 
INCREMENT BY 1;
```

其次，将序列 `asc_seq` 前推进到下一个值：

```sql
SELECT nextval('asc_seq');
```

输出：

```sql
 nextval
---------
       1
```

如果您调用 `nextval()` 函数，它会继续将 `asc_seq` 前推进到下一个值：

```sql
SELECT nextval('asc_seq');
```

输出：

```sql
 nextval
---------
       2
```

第三，获取 `asc_seq` 序列的当前值：

```sql
SELECT currval('asc_seq');
```

输出：

```sql
 currval
---------
       2
```

# 创建降序示例

首先，创建一个新的降序序列 `（desc_seq）` ，递减为 `-1` ：

```sql
CREATE SEQUENCE desc_seq
INCREMENT BY -1;
```

其次，将序列前进到下一个值：

```sql
SELECT nextval('desc_seq');
```

输出：

```sql
 nextval
---------
      -1
```

如果再次调用 `nextval()` 函数，它会将 `desc_seq` 序列推进到下一个值：

```sql
SELECT nextval('desc_seq');
```

输出：

```sql
 nextval
---------
      -2
```

第三，获取desc_seq序列的当前值：

```sql
SELECT currval('desc_seq');
```

输出：

```sql
 currval
---------
      -2
```

# 将序列与表列相关联

首先，创建一个新表来存储转移单标题：

```sql
CREATE TABLE transfer_order_headers (
  order_no INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  warehouse_id INT NOT NULL,
  movement_type VARCHAR(20) NOT NULL
);
```

其次，创建一个新的表 `transfer_order_items` 来存储订单项目。

```sql
CREATE TABLE transfer_order_items (
  order_no INT,
  item_no INT,
  product_id INT NOT NULL,
  quantity INT NOT NULL,
  from_bin VARCHAR(20) NOT NULL,
  to_bin VARCHAR(20) NOT NULL,
  PRIMARY KEY (order_no, item_no),
  FOREIGN KEY (order_no) REFERENCES transfer_order_headers(order_no)
);
```

第三，创建一个与 `transfer_order_items` 表的 `item_no` 列关联的新序列：

```sql
CREATE SEQUENCE item_no_seq 
START 10 
INCREMENT 10 
MINVALUE 10 
OWNED BY transfer_order_items.item_no;
```

第四，使用 `item_no_seq` 序列生成的值设置 `item_no` 列的默认值：

```sql
ALTER TABLE transfer_order_items 
ALTER COLUMN item_no
SET DEFAULT nextval('item_no_seq');
```

第五，创建新的转移单：

```sql
INSERT INTO
  transfer_order_headers (warehouse_id, movement_type)
VALUES
  (1, 'Picking') 
RETURNING *;
```

输出：

```sql
 order_no |          created_at           | warehouse_id | movement_type
----------+-------------------------------+--------------+---------------
        1 | 2024-12-08 22:45:43.173269-05 |            1 | Picking
```

最后，为将产品 `ID 1` 和 `2` 从库 `A-01` 移动到 `B-01` 的转移单创建三个物料：

```sql
INSERT INTO
  transfer_order_items (order_no, product_id, quantity, from_bin, to_bin)
VALUES
  (1, 1, 5, 'A-01', 'B-01'),
  (1, 2, 10, 'A-01', 'B-01') 
RETURNING *;
```

输出：

```sql
 order_no | item_no | product_id | quantity | from_bin | to_bin
----------+---------+------------+----------+----------+--------
        1 |      10 |          1 |        5 | A-01     | B-01
        1 |      20 |          2 |       10 | A-01     | B-01
```

序列 `item_no_seq` 自动生成 `item_no` 列的值。

# 删除序列

使用 `DROP SEQUENCE` 语句删除序列：

```sql
DROP SEQUENCE [IF EXISTS] sequence_name
[CASCADE | RESTRICT];
```

在此语法中：

- 首先，指定要删除的序列名称。
- 其次，使用 `CASCADE` 选项删除序列及其依赖对象，进而删除依赖于依赖对象的对象，依此类推。

例如，以下语句从数据库中删除 `asc_seq` ：

```sql
DROP SEQUENCE IF EXISTS asc_seq;
```

# 列出所有序列

要列出数据库中的所有序列，请使用以下查询：

```sql
SELECT * FROM pg_class
WHERE relkind = 'S';
```

# 总结

- 使用序列根据规范自动生成唯一整数。
- 使用 `CREATE SEQUENCE` 语句创建新序列。
- 使用 `nextval` 函数将序列推进到下一个值。
- 使用 `currval` 函数获取 `nextval` 函数最近生成的值。
- 使用 `setval` 函数手动设置序列的当前值。
- 使用 `OWNED BY` 子句将序列与表列相关联。
- 使用 `DROP SEQUENCE` 语句删除序列。
