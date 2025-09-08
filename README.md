# PostgreSQL-Tutorial

本 `PostgreSQL` 教程将从入门到高级全面教你学习 `PostgreSQL` 。

> 内容翻译来自 [`PostgreSQL-Tutorial`](https://www.pgtutorial.com/)


# 第 1 节 PostgreSQL入门

&#9654; [什么是 `PostgreSQL` ](第1节-PostgreSQL入门/什么是PostgreSQL.md) ？解释什么是 `PostgreSQL` 以及你可以用它做什么。

&#9654; [创建新表](第1节-PostgreSQL入门/创建新表.md) -- 了解如何在 `PostgreSQL` 中创建新表。

&#9654; [向表中插入数据](第1节-PostgreSQL入门/向表中插入数据.md) -- 向您展示如何向表中插入一行或多行数据。

&#9654; [从表中查询数据](第1节-PostgreSQL入门/从表中查询数据.md) -- 使用SELECT语句从表中查询数据。

&#9654; [字符串拼接](第1节-PostgreSQL入门/字符串拼接.md) -- 了解如何将多个字符串拼接成一个字符串。

&#9654; [删除表](第1节-PostgreSQL入门/删除表.md) -- 指导您从PostgreSQL数据库中删除表。

# 第 2 节 筛选表中的行

&#9654; [ `WHERE` ](第2节-过滤表中的行/WHERE.md) -- 使用 `WHERE` 子句从表中筛选行。

&#9654; [ `BETWEEN` ](第2节-过滤表中的行/BETWEEN.md) -- 检查一个值是否介于两个值之间。

&#9654; [ `IN` ](第2节-过滤表中的行/IN.md) -- 判断一个值是否在一组值中。

&#9654; [更新](第2节-过滤表中的行/Update.md) -- 更新表中一行或多行的数据。

&#9654; [删除](第2节-过滤表中的行/Delete.md) -- 从表中删除一行或多行。

# 第 3 节 使用表格数据

&#9654; [数据库设计](第3节-使用表格数据/数据库设计.md) -- 从头开始为库存管理系统设计数据库。

&#9654; [主键](第3节-使用表格数据/主键.md) -- 向您展示如何为表定义主键。

&#9654; [非空约束](第3节-使用表格数据/非空约束.md) -- 介绍NULL，并使用非空约束确保列不包含NULL。

&#9654; [ `DEFAULT` 约束](第3节-使用表格数据/DEFAULT约束.md) -- 为表列设置默认值。

&#9654; [ `CHECK` 约束](第3节-使用表格数据/CHECK约束.md) --向一个或多个列添加验证规则，以确保值满足条件。

&#9654; [唯一约束](第3节-使用表格数据/唯一约束.md) -- 确保一列或一组列中的值在同一表的各行中是唯一的。

&#9654; [外键](第3节-使用表格数据/外键.md) -- 了解如何为表创建外键。

# 第 4 节 排序和限制

&#9654; [ `ORDER BY` ](第4节-排序和限制/ORDER-BY.md) -- 按一个或多个列对行进行排序。

&#9654; [ `LIMIT` ](第4节-排序和限制/LIMIT.md) -- 仅返回查询结果中的一部分行。

&#9654; [ `FETCH` ](第4节-排序和限制/FETCH.md) -- 作用类似于 `LIMIT` 子句,用于跳过部分行并从查询结果中返回一部分行。

&#9654; [ `IS NULL` ](第4节-排序和限制/IS-NULL.md) -- 检查一个值是否为NULL。

# 第 5 节 连接表

&#9654; [ `Inner Join` ](第5节-连接表/Inner-Join.md) -- 连接两个表，并从第一个表中选择在右表中有匹配行的行。

&#9654; [ `Left Join` ](第5节-连接表/Left-Join.md) -- 将左表与右表连接，并从左表中选择所有行以及右表中匹配的行。

&#9654; [ `Right Join` ](第5节-连接表/Right-Join.md) -- 将左表与右表连接，并从右表中选择所有行以及左表中匹配的行。

&#9654; [ `Self Join` ](第5节-连接表/Self-join.md) -- 使用内连接、左连接或右连接将一个表与自身连接，以比较同一表中的行。

&#9654; [ `Full Join` ](第5节-连接表/Full-join.md) -- 合并两个表中的行，并返回两个表中的匹配行和非匹配行。

&#9654; [ `Cross Join` ](第5节-连接表/Cross-Join.md) --连接两个表并返回一个结果集，该结果集包含两个表中所有可能的行组合。

&#9654; [ `Natural Join` ](第5节-连接表/Natural-Join.md) -- 基于列名连接两个表。

# 第 6 节 行分组

&#9654; [ `GROUP BY` ](第6节-行分组/GROUP-BY.md) -- 按表中一列或多列的值将行分组，并对每个组应用聚合函数。

&#9654; [ `HAVING` ](第6节-行分组/HAVING.md) -- 根据条件筛选行组。

&#9654; [ `GROUPING SETS` ](第6节-行分组/GROUPING-SETS.md) -- 在单个查询中创建多个分组。

&#9654; [ `ROLLUP` ](第6节-行分组/ROLLUP.md) -- 创建层级汇总。

&#9654; [ `CUBE` ](第6节-行分组/CUBE.md) -- 创建指定列的所有可能聚合组合。

# 第 7 节 集合运算

&#9654; [ `UNION` ](第7节-集合运算/UNION.md) --将一个结果集附加到另一个结果集，并返回单个结果集。

&#9654; [ `INTERSECT` ](第7节-集合运算/INTERSECT.md) -- 返回两个结果集的交集。

&#9654; [ `EXCEPT` ](第7节-集合运算/EXCEPT.md) -- 找出两个查询的两个结果集之间的差异。

# 第 8 节 PostgreSQL数据类型

&#9654; [ `Boolean` ](第8节-PostgreSQL数据类型/Boolean.md) -- 在数据库中存储布尔数据，包括 `true、false` 和 `NULL` 值。

&#9654; [ `Char` ](第8节-PostgreSQL数据类型/char.md) --在数据库中存储固定长度和填充字符串。

&#9654; [ `Varchar` ](第8节-PostgreSQL数据类型/Varchar.md) --在数据库中存储可变长度的字符串。

&#9654; [ `Text` ](第8节-PostgreSQL数据类型/Text.md) --存储无大小限制的可变长度字符数据。

&#9654; [ `Integer` ](第8节-PostgreSQL数据类型/Integer.md) -- 使用各种整数类型存储整数。

&#9654; [ `Decimal` ](第8节-PostgreSQL数据类型/Decimal.md) -- 使用 `decimal` 或 `numeric` 类型存储具有精度的数值数据。

&#9654; [ `Date` ](第8节-PostgreSQL数据类型/Date.md) -- 在表中存储不含时间数据的日期。

&#9654; [ `Time` ](第8节-PostgreSQL数据类型/Time.md) --在表中存储不含日期的时间数据。

&#9654; [ `Timestamp` ](第8节-PostgreSQL数据类型/Timestamp.md) -- 在数据库中存储不带时区的本地日期和时间。

&#9654; [ `Timestamp with a time zone` ](第8节-PostgreSQL数据类型/Timestamp-with-a-time-zone.md) -- 了解如何使用 `TIMESTAMPTZ` 数据类型处理带时区的时间戳值。

&#9654; [ `Interval` ](第8节-PostgreSQL数据类型/Interval.md) -- 存储时间间隔值。

&#9654; [ `UUID` ](第8节-PostgreSQL数据类型/UUID.md) -- 向您展示如何将 `UUID` 类型用于主键列。

# 第 9 节 子查询

&#9654; [ `Subquery` ](第9节-子查询/Subquery.md) -- 了解如何编写嵌套在另一个查询中的查询，以形成更灵活的查询

&#9654; [ `Correlated Subquery` ](第9节-子查询/Correlated-Subquery.md) -- 向您展示如何使用关联子查询来选择依赖于外部查询值的数据。

&#9654; [ `Subquery with IN operator` ](第9节-子查询/Subquery-with-IN-operator.md) -- 向您展示如何在 `WHERE` 子句中使用带IN运算符的子查询来筛选行。

&#9654; [ `EXISTS` ](第9节-子查询/EXISTS.md) -- 了解如何使用 `EXISTS` 运算符检查子查询返回的行是否存在。

&#9654; [ `ANY` ](第9节-子查询/ANY.md) -- 将一个值与子查询返回的一组值进行比较，如果至少有一个比较结果为真，则返回真。

&#9654; [ `ALL` ](第9节-子查询/ALL.md) -- 将一个值与子查询返回的一组值进行比较，如果所有比较结果都为真，则返回真。

# 第 10 节 CTE公用表表达式

&#9654; [ `CTE` ](第10节-CTE公用表表达式/CTE.md) -- 了解如何使用公用表表达式 `CTE` 简化复杂查询。

&#9654; [ `Recursive CTE` ](第10节-CTE公用表表达式/Recursive-CTE.md) -- 向你展示如何使用递归 `CTE` 执行递归查询。

# 第 11 节 选择不同的行

&#9654; [ `SELECT DISTINCT` ](第11节-选择不同的行/SELECT-DISTINCT.md) -- 通过一列或多列的值从结果集中消除重复行。

&#9654; [ `SELECT DISTINCT ON` ](第11节-选择不同的行/SELECT-DISTINCT-ON.md) -- 了解如何将行分组为不同的组，并选择每个组中的第一行。

# 第 12 节 数据库视图

&#9654; [ `View` ](第12节-数据库视图/view.md) -- 了解视图以及如何使用视图创建可重用的查询并简化复杂查询。

&#9654; [ `Materialized Views` ](第12节-数据库视图/Materialized-Views.md) -- 创建物化视图以提高耗时查询的性能。

# 第 13 节 条件表达式

&#9654; [ `CASE expression` ](第13节-条件表达式/CASE-expression.md) -- 在查询中添加 `if-else` 逻辑，以构建更灵活的查询。

&#9654; [ `COALESCE` ](第13节-条件表达式/COALESCE.md) -- 返回第一个非空参数，允许您将 `NULL` 替换为非 `NULL` 值。

&#9654; [ `NULLIF` ](第13节-条件表达式/NULLIF.md) -- 如果两个参数相同则返回 `NULL` ,使你能够用 `NULL` 替换某个值。

&#9654; [ `GREATEST` ](第13节-条件表达式/GREATEST.md) -- 返回一组值中的最大值。

&#9654; [ `LEAST` ](第13节-条件表达式/LEAST.md) -- 返回值列表中的最小值。

# 第 14 节 增强表结构

&#9654; [ `ALTER TABLE` ](第14节-增强表结构/ALTER-TABLE.md) -- 通过添加列、删除列、重命名表等方式修改表结构。

&#9654; [ `Sequence` ](第14节-增强表结构/Sequence.md) -- 了解如何使用序列生成连续整数。

&#9654; [ `Identity Column` ](第14节-增强表结构/Identity-Column.md) -- 使用标识列基于隐式序列自动生成整数。

&#9654; [ `Adding columns to a table` ](第14节-增强表结构/Adding-columns-to-a-table.md) -- 向您展示如何向表中添加一个或多个列。

&#9654; [ `Renaming tables` ](第14节-增强表结构/Renaming-tables.md) -- 了解如何将表名更改为新名称。

&#9654; [ `Renaming columns` ](第14节-增强表结构/Renaming-columns.md) -- 指导你如何更改现有表列的名称。

&#9654; [ `Dropping columns` ](第14节-增强表结构/Dropping-columns.md) -- 向您展示如何从表中删除一个或多个列。

# 第 15 节 高级文本搜索

&#9654; [ `LIKE` ](第15节-高级文本搜索/LIKE.md) -- 查找与模式匹配的字符串。

&#9654; [ `Regular Expressions` ](第15节-高级文本搜索/Regular-Expressions.md) -- 了解如何使用匹配运算符 `(~)` 来搜索与 `POSIX` 正则表达式匹配的字符串。

&#9654; [ `SIMILAR TO` ](第15节-高级文本搜索/SIMILAR-TO.md) -- 搜索与包含通配符 `(% and _ )` 或正则表达式的模式相匹配的字符串。

&#9654; [ `Full-text search` ](第15节-高级文本搜索/Full-text-search.md) -- 执行全文搜索

# 第 16 节 用户定义函数

&#9654; [ `Create functions` ](第16节-用户定义函数/Create-functions.md) -- 了解如何创建用户定义函数。

&#9654; [ `Drop functions` ](第16节-用户定义函数/Drop-functions.md) -- 向您展示如何删除一个或多个用户定义函数。

# 第 17 节 存储过程

&#9654; [ `Create stored procedures` ](第17节-存储过程/Create-stored-procedures.md) -- 展示如何使用 `CREATE PROCEDURE` 语句创建存储过程。

&#9654; [ `Drop procedure` ](第17节-存储过程/Drop-procedure.md) -- 了解如何从数据库中移除存储过程。

# 第 18 节 索引

&#9654; [ `Index `](第18节-索引/Index.md) -- 利用索引提高查询性能。

&#9654; [ `CREATE INDEX` ](第18节-索引/CREATE-INDEX.md) -- 为表的一个或多个列创建索引。

&#9654; [ `DROP INDEX` ](第18节-索引/DROP-INDEX.md) -- 从数据库中删除索引。

&#9654; [ `Unique index` ](第18节-索引/Unique-index.md) -- 强制表中一列或多列的值具有唯一性。

&#9654; [ `Expression Index` ](第18节-索引/Expression-Index.md) -- 基于涉及表列的表达式结果创建索引,这可以优化包含表达式的查询。

&#9654; [ `Partial Indexes` ](第18节-索引/Partial-Indexes.md) -- 创建部分索引，其中包含索引列中的部分行,以减小索引大小并提高查询性能。

&#9654; [ `Index-only Scans` ](第18节-索引/Index-only-Scans.md) -- 利用仅索引扫描从索引中完整检索数据。

# 第 19 节 非规范化数据类型

&#9654; [ `Composite types` ](第19节-非规范化数据类型/Composite-types.md) -- 解释什么是复合类型以及如何管理复合类型,例如创建新的复合类型。

&#9654; [ `Drop Type` ](第19节-非规范化数据类型/Drop-Type.md) -- 了解如何从数据库中移除用户定义的类型。

&#9654; [ `Array` ](第19节-非规范化数据类型/Array.md) -- 使用数组数据类型在一个列中存储多个相同类型的值。

&#9654; [ `XML` ](第19节-非规范化数据类型/XML.md) -- 使用 `XML` 数据类型在数据库中存储 `XML` 文档或 `XML` 片段。

&#9654; [ `Enum` ](第19节-非规范化数据类型/Enum.md) -- 定义一个接受小型固定值列表的列。

&#9654; [ `JSON` ](第19节-非规范化数据类型/JSON.md) -- 使用 `JSON` 数据类型在数据库中存储 `JSON` 。

&#9654; [ `Cast` ](第19节-非规范化数据类型/Cast.md) -- 将一种类型的值转换为另一种类型。

# 第 20 节 触发器

&#9654; [ `Triggers` ](第20节-触发器/Triggers.md) -- 了解如何使用触发器自动响应表上发生的事件。

&#9654; [ `Drop Trigger ` ](第20节-触发器/Drop-Trigger.md) -- 指导你如何从数据库中的表中删除触发器。

&#9654; [ `Disabling Triggers` ](第20节-触发器/Disabling-Triggers.md) -- 向您展示如何关闭表的触发器，以避免在执行批量数据加载时产生性能开销。

&#9654; [ `Event Triggers` ](第20节-触发器/Event-Triggers.md) -- 了解如何使用事件触发器来监控和控制数据库结构的变化。

&#9654; [ `BEFORE INSERT Trigger`](第20节-触发器/BEFORE-INSERT-Trigger.md) -- 使用插入前触发器可在向表中插入行之前自动调用函数。

&#9654; [ `AFTER INSERT Trigger` ](第20节-触发器/AFTER-INSERT-Trigger.md) -- 在表上发生插入事件后,使用插入后触发器自动调用函数。
