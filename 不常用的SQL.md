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
    PARTITION pSouth VALUES IN (108, 110, 112));
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

# 触发器
触发器，就是一种特殊的存储过程。 触发器和存储过程一样是一个能够完成特定功能，存储在数据库服务器上的SQL片段。但是触发器无需调用，当对数据库中的数据执行DML操作时会自动触发这个SQL片段的执行，无需手动调用。  

## 触发器类型
* 行级触发器  
行级触发器创建在实体表上，每次表受到触发语句影响时会触发一个行级触发器。例如，一条语句更新多行数据，每条受影响的数据都会触发一次触发器。如果触发语句不影响任何行数据，则不会运行触发器。

* 语句级触发器  
语句级触发器创建在实体表上，每次执行触发语句时都会自动触发一次，无论该触发语句是否影响了表中的任何行数据。例如，一条语句更新了表中的 100 条数据，则语句级 UPDATE 触发器仅触发一次。

## 语法
```sql
CREATE [OR REPLACE] TRIGGER trigger_name triggering_statement
  [trigger_restriction]
BEGIN
 triggered_action;
END

triggering_statement:
  {BEFORE | AFTER }
  {INSERT | DELETE | UPDATE [OF column [, column ...]]}
  ON [schema.] table_name 
  [REFERENCING {OLD [AS] old | NEW [AS] new| PARENT as parent}]
  FOR EACH ROW
  [WHEN condition]
  [FOLLOWS | PRECEDES] other_trigger_name
```
