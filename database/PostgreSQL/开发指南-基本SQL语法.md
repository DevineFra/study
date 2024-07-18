# 批量dml
1. 原生insert into xx values(),(),...();
2. update/delete in
3. COPY FROM/TO
```sql
COPY table_name [ (column_name [, ...] ) ]
FROM { 'filename' | STDIN }
[ [ WITH ] ( option [, ...] ) ]
table_name: 目标表的名称。
(column_name[, ...]): 可选参数，指定要复制到表中的列名列表。如果省略，则默认为表中的所有列。
FROM: 指定数据来源，可以是一个文件名（需要是数据库服务器可访问的路径），或者是STDIN，表示从标准输入读取数据。
WITH: 后面跟着一系列选项，用于定义数据文件的格式和处理规则，常见的选项包括：
FORMAT: 指定数据文件的格式，常见的有csv（默认）、text、binary等。
DELIMITER: 数据字段之间的分隔符，默认为逗号``,`。
QUOTE: 字段周围的引用字符，默认为双引号"。
ESCAPE: 转义字符，默认为\。
HEADER: 如果数据文件的第一行是列名，则设置为true，否则为false或省略。
ENCODING: 指定文件的编码格式，如UTF8。
还有更多选项如NULL（表示空值的字符串）、FORCE_QUOTE（强制对某些列使用引号）等。

例如：COPY employees (id, name, age) FROM '/path/to/data.csv' WITH (FORMAT csv, DELIMITER ',', HEADER true, ENCODING 'UTF8');

COPY table_name [ (column_name [, ...] ) ]
TO { 'filename' | STDOUT }
[ [ WITH ] ( option [, ...] ) ]
```

# distinct on
```sql
SELECT DISTINCT ON (column1, column2, ...) column_list
FROM table_name
ORDER BY column1, column2, ...;

column1, column2, ...: 指定用于确定唯一性的列名。这些列用于决定哪些行被视为“相同的”，并从中选择第一行。
column_list: 你想从表中选择的列。可以包括DISTINCT ON子句中指定的列，也可以包括其他列。
ORDER BY: 这是关键部分，它指定了在每个DISTINCT ON分组内部如何排序行。通常，你需要根据相同的列或额外的列来排序，以确定哪一行是“第一行”。
```
DISTINCT：全局去重，整个结果集中的每一行都必须是唯一的，所有列值都相同才会被认为是重复行。
DISTINCT ON (column1, column2, ...)：局部去重，基于指定的列进行去重，即使其他列的值不同，只要这些指定列的组合值首次出现，该行就会被选中。

# 序列

## 创建序列对象
```sql
CREATE SEQUENCE sequence_name
    [ INCREMENT BY increment ]
    [ START WITH start ]
    [ MINVALUE minvalue ]
    [ MAXVALUE maxvalue ]
    [ CACHE cache ]
    [ CYCLE | NO CYCLE ]
    [ OWNED BY column_name ];
```

## 插入数据时使用序列
在插入数据时，可以通过nextval函数获取序列的下一个值：
```sql
INSERT INTO users (id, username) VALUES (nextval('seq_user_id'), 'Alice');
```
或者在创建表时直接定义字段为SERIAL类型，PG会自动创建一个序列并将其与该字段关联：
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50)
);
```

## 查看序列状态
可以使用SELECT语句查看序列的当前值、下一个值等信息：
```sql
SELECT last_value, next_value FROM seq_user_id;
```

## 修改序列
可以使用ALTER SEQUENCE命令来修改序列的属性，如增长步长、最大值等。

## 删除序列
使用DROP SEQUENCE命令可以删除不再需要的序列

# 索引
```sql
CREATE [UNIQUE] INDEX [CONCURRENTLY] indx_name ON table_name(column);
CREATE [UNIQUE] INDEX [CONCURRENTLY] indx_name ON table_name USING index_type(column);
```

## 类型转换
cast(col as newtype)
col::newtype

## 自定义函数
```sql
CREATE [ OR REPLACE ] FUNCTION function_name ( [ argument_name data_type [, ...] ] )
RETURNS return_data_type
AS $$
-- 函数体，可以是SQL语句或者PL/pgSQL代码
BEGIN
   -- 函数执行的逻辑
   ...
END;
$$ LANGUAGE plpgsql;
```

## 高级SQL
### with ordinary


