**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 事件触发器来监视和控制数据定义语言(`DDL`)作。

# `PostgreSQL` 事件触发器概述

常规触发器与表相关联，并响应表上发生的事件，例如 `INSERT`、`UPDATE`、`DELETE` 和 `TRUNCATE`。

与常规触发器不同，事件触发器不与表相关联，而是与数据库相关联。事件触发器响应数据库架构更改事件，例如添加列、删除表等。

在 `PostgreSQL` 中，事件触发器可以跟踪以下事件：

- `ddl_command_start`：在任何 `DDL` 命令的开头触发。
- `ddl_command_end`：在任何 `DDL` 命令的末尾触发。
- `sql_drop`：物体掉落后触发。
- `table_rewrite`：当表的结构发生更改时触发。

# 创建事件触发器

您可以使用 `CREATE EVENT TRIGGER` 语句创建事件触发器：

```sql
CREATE EVENT TRIGGER trigger_name
ON event_name
WHEN condition
EXECUTE FUNCTION function_name();
```

在此语法中：

- `trigger_name`：事件触发器名称。
- `event_name`：要监控的 `DDL` 事件，例如 `ddl_command_start`。
- `condition`：用于过滤事件的可选条件。
- `function_name`：事件发生时要执行的函数。

在实践中，你会发现事件触发器对以下方案很有用：

- 审核数据库架构更改。
- 强制实施特定的数据库策略。
- 防止未经授权的数据库架构修改。
- 出于管理目的自动执行通知或日志。

# 事件触发器示例

首先，创建一个名为 `event_logs` 的表来存储所有数据库结构修改的日志：

```sql
CREATE TABLE event_logs (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    event_type VARCHAR NOT NULL,
    command_tag VARCHAR NOT NULL,
    object_id OID NOT NULL,
    object_name VARCHAR NOT NULL,
    schema_name VARCHAR NOT NULL,
    user_name VARCHAR NOT NULL,
    event_time TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

其次，创建一个函数，将 `DDL` 事件插入 `event_logs` 表中：

```sql
CREATE OR REPLACE FUNCTION log_events()
RETURNS EVENT_TRIGGER
AS 
$$
BEGIN
    INSERT INTO event_logs (
        event_type,
        command_tag,
        object_id,
        object_name,
        schema_name,
        user_name
    )
    SELECT
        tg_event,
        tg_tag,
        objid,
        object_identity,
        schema_name,
        current_user
    FROM pg_event_trigger_ddl_commands();
END;
$$ LANGUAGE plpgsql;
```

第三，创建一个事件触发器并将其与 `log_events` 函数关联：

```sql
CREATE EVENT TRIGGER log_schema_changes
ON ddl_command_end
EXECUTE FUNCTION log_events();
```

第四，通过添加一个名为 `net_weight` 的新列来修改 `products` 表，类型为 `decimal`：

```sql
ALTER TABLE products
ADD COLUMN net_weight DEC;
```

`ALTER TABLE` 语句将触发将数据插入 `event_logs` 表的 `log_schema_changes` 触发器。

最后，从 `event_logs` 表中检索数据：

```sql
SELECT
  event_type,
  command_tag,
  object_name,
  user_name
FROM
  event_logs;
```

输出：

```sql
   event_type    | command_tag |   object_name   | user_name
-----------------+-------------+-----------------+-----------
 ddl_command_end | ALTER TABLE | public.products | postgres
```

# 列出事件触发器

要获取所有事件触发器，您可以从 `PostgreSQL` 系统目录中的 `pg_event_trigger` 进行查询：

```sql
SELECT * FROM pg_event_trigger;
```

输出：

```sql
  oid  |      evtname       |    evtevent     | evtowner | evtfoid | evtenabled | evttags
-------+--------------------+-----------------+----------+---------+------------+---------
 46763 | log_schema_changes | ddl_command_end |       10 |   46762 | O          |
```

# 禁用事件触发器

若要关闭事件触发器，请使用 `ALTER EVENT TRIGGER` 语句：

```SQL
ALTER EVENT TRIGGER event_trigger_name
DISABLE;
```

例如，以下语句关闭 `log_schema_changes` 事件触发器：

```SQL
ALTER EVENT TRIGGER log_schema_changes
DISABLE;
```

# 启用事件触发器

要启用事件触发器，您还可以使用 `ALTER EVENT TRIGGER` 语句，但使用 `ENABLE` 选项：

```SQL
ALTER EVENT TRIGGER event_trigger_name
ENABLE;
```

例如，以下语句重新启用 `log_schema_changes` 事件触发器：

```SQL
ALTER EVENT TRIGGER log_schema_changes
ENABLE;
```

# 删除事件触发器

如果不再使用事件触发器，可以使用 `DROP EVENT TRIGGER` 语句将其删除：

```SQL
DROP EVENT TRIGGER event_trigger_name;
```

例如，您可以使用以下语句删除 `log_schema_changes` 事件触发器：

```SQL
DROP EVENT TRIGGER log_schema_changes;
```

# 防止未经授权的模式更改

以下示例演示如何使用事件触发器来防止未经授权的用户修改数据库结构：

**步骤 1**. 创建一个仅允许用户 `postgres` 修改数据库结构的函数：

```SQL
CREATE FUNCTION stop_ddl () 
  RETURNS EVENT_TRIGGER 
AS 
$$
BEGIN
   IF current_user <> 'postgres' THEN
      RAISE EXCEPTION 'The user % does not have the authorization to change the database structure', current_user;
   END IF;
END;
$$ 
LANGUAGE plpgsql;
```

如果用户不是 `postgres`，则 `stop_ddl` 函数会引发异常。

步骤 2.创建一个事件触发器 `authorize_schema_change`，每当 `DDL` 事件发生时执行 `stop_ddl()` 函数：

```SQL
CREATE EVENT TRIGGER stop_executing_ddl 
ON ddl_command_start
EXECUTE FUNCTION stop_ddl();
```

只有用户 `postgres` 可以执行 `DDL` 命令，而其他人将收到异常。

# 总结

- `PostgreSQL` 事件触发器是数据库级触发器。
- 使用 `PostgreSQL` 事件触发器来监视和控制数据库结构更改。
- 使用 `CREATE EVENT TRIGGER` 语句创建新的事件触发器。
- 使用 `ALTER EVENT TRIGGER ... DISABLE` 语句来禁用事件触发器。
- 使用 `ALTER EVENT TRIGGER ... ENABLE` 语句来启用事件触发器。
- 使用 `DROP EVENT TRIGGER` 语句从数据库中删除事件触发器。