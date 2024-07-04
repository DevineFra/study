# 分区
## 语法
### RANGE
```sql
CREATE TABLE Sales ( cust_id INT NOT NULL, name VARCHAR(40),   
store_id VARCHAR(20) NOT NULL, bill_no INT NOT NULL,   
bill_date DATE PRIMARY KEY NOT NULL, amount DECIMAL(8,2) NOT NULL)   
PARTITION BY RANGE (year(bill_date))(   
PARTITION p0 VALUES LESS THAN (2016),   
PARTITION p1 VALUES LESS THAN (2017),   
PARTITION p2 VALUES LESS THAN (2018),   
PARTITION p3 VALUES LESS THAN (2020));
```

### HASH
```sql
CREATE TABLE Stores (   
    cust_name VARCHAR(40),   
    bill_no VARCHAR(20) NOT NULL,   
    store_id INT PRIMARY KEY NOT NULL,   
    bill_date DATE NOT NULL,   
    amount DECIMAL(8,2) NOT NULL  
)  
PARTITION BY HASH(store_id)  
PARTITIONS 4;
```

### LIST
```sql
CREATE TABLE Stores (   
    cust_name VARCHAR(40),   
    bill_no VARCHAR(20) NOT NULL,   
    store_id INT PRIMARY KEY NOT NULL,   
    bill_date DATE NOT NULL,   
    amount DECIMAL(8,2) NOT NULL 
)
PARTITION BY LIST(store_id) (   
    PARTITION pEast VALUES IN (101, 103, 105),   
    PARTITION pWest VALUES IN (102, 104, 106),   
    PARTITION pNorth VALUES IN (107, 109, 111),   
    PARTITION pSouth VALUES IN (108, 110, 112)
);
``` 

### 子分区
```sql
CREATE TABLE table10 (BILL_NO INT, sale_date DATE, cust_code VARCHAR(15), AMOUNT DECIMAL(8,2))  
PARTITION BY RANGE(YEAR(sale_date) )  
SUBPARTITION BY HASH(TO_DAYS(sale_date))  
SUBPARTITIONS 4 (  
    PARTITION p0 VALUES LESS THAN (1990),  
    PARTITION p1 VALUES LESS THAN (2000),  
    PARTITION p2 VALUES LESS THAN (2010),  
    PARTITION p3 VALUES LESS THAN MAXVALUE  
);
```

## 注意事项
* range/hash分区键必须是整数值
* 分区键必须是主键的一部分，保证主键查询的时候可以直接定位到分区

# PL/SQL
## 语法结构
```
BEGIN
  declare ...
  sql_statement
END
```
* 标签可用于begin ... end块中的loop/repeat/while [begin_label:] loop/ end loop[end_label]
* 块标签的范围不包括块内声明的处理程序的代码。

## 变量
### 局部变量
```sql
#声明
declare var_name type [default var_value]
#赋值
set var_name = var_value
select filed into var_name, [var_name] from xxxx

```
* declare声明变量必须再begin ... end块中

### 用户变量
```sql
# 赋值
set @var_name = var_value
# 查询
select @var_name
```
* 用户变量不需要提前声明，使用即为声明

### 会话变量
```sql
# 赋值
set @@var_name = var_value
# 查询
select @@var_name
```

### 全局变量
```sql
select @@global.var_name
```

## 游标
### 语法结构
```sql
创建
declare cursor_name cursor for select_statement
打开
open cursor_name
获取下一行
fetch cursor_name into var_name[, var_name]
关闭游标
close cursor_name
```

## 异常处理
### DECLARE ... CONDITION
用于声明异常条件并为其命名，condition_name可以供给后面declare handler中引用
```sql
DECLARE condition_name CONDITION FOR condition_value

condition_value: {
    mysql_error_code
  | SQLSTATE [VALUE] sqlstate_value
}

```

### DECLARE ... HANDLER
用于指定处理一个或多个异常条件的处理程序。如果出现某一条件，就会执行指定的语句。  
```sql
DECLARE handler_action HANDLER
    FOR condition_value [, condition_value] ...
    sql_statement

handler_action: {
    CONTINUE 继续执行
  | EXIT 退出
  | UNDO 不支持
}
condition_value: {
    mysql_error_code 整数错误码
  | SQLSTATE [VALUE] sqlstate_value
  | condition_name
  | SQLWARNING
  | NOT FOUND
  | SQLEXCEPTION
}
```

## 条件控制语句
### case
```sql
CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list] ...
    [ELSE statement_list]
END CASE

CASE
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list] ...
    [ELSE statement_list]
END CASE
```
* 第一种写法使用case_calue与每个when进行比较，相等则执行stateement_list
* 第二种写法对每个when后的search_condition进行处理判断是否结果为True，则执行statement_list

### if
```sql
IF search_condition THEN statement_list
    [ELSEIF search_condition THEN statement_list] ...
    [ELSE statement_list]
END IF
```

### iterate
```sql
iterate label
```
再次启动循环语句，iterate只能出现在loop、repeat、while

### leave
```sql
leave label
```
用于跳出流程控制语句，只能出现在loop、repeat、while

### 循环
```sql
[begin_label:] LOOP
    statement_list
END LOOP [end_label]

[begin_label:] REPEAT
    statement_list
UNTIL search_condition
END REPEAT [end_label]

[begin_label:] WHILE search_condition DO
    statement_list
END WHILE [end_label]

```

### 退出
```sql
return expr
```
* 存储函数中必须至少有一个 RETURN 语句。如果函数有多个退出点，则可能有多个 RETURN 语句。  
* 存储过程和触发器不使用 RETURN 语句，而是使用 LEAVE 语句退出程序。  

## 应用-存储过程
### 语法结构
```sql
delimiter //
create [or replace] PROCEDURE 过程名( in|out|inout 参数名 数据类型 , ...)
begin
  sql语句;
[exception]
  exception_handler
end //
delimiter ;
call 过程名(参数值);
```

## 应用-触发器
### 语法结构
## 语法
```sql
delimiter $$
create [or replace] trigger trigger_name BEFORE|AFTER trigger_EVENT 
ON TABLE_NAME FOR EACH ROW 
BEGIN                                                        
   on_trigger_sql1; 
   on_trigger_sql2; 
   更多sql语句...                                      
END; 
$$
delimiter ; 
```

## 例子
```sql
delimiter //
create procedure demo(in t1 integer, inout t2 varchar(32))
begin
  declare unknown_column condition for sqlstate '42S22';
  declare done bool default false;
  declare x integer;
  declare y varchar(32);
  declare cur1 cursor for select salary, info from emp;
  declare continue handler for not found set done = true;
  declare exit handler for unknown_column set done = true;
  open cur1;
  read_loop: loop
    fetch cur1 into x, y;
    if done then
      leave read_loop;
    end if;
    if y != t2 then
      set y = concat(y, t2);
    end if;
    insert into sal(salary, inf, tag) values (x, y, t1);
  end loop;
  close cur1;
end //
delimiter ;
```

# Prepared Statement
## 语法结构
```sql
set @sql = "select xx from xxx where id = ?"
# 准备
prepare stmt_name from @sql
# 执行
set @var_name = xxx
execute stmt_name using @var_name
# 释放
deallocate prepare stmt_name
```
