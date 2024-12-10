#dead tuple
为了完成数据库的MVCC多版本控制，以及数据的undo的功能，每行数据可能存在多个版本的数据。而过期版本的数据被弃用后，就会产生众多的死行 dead tuple
如果不及时清理这些dead tuple，轻则影响磁盘空间的利用，重则影响数据库的性能。


#AutoVacuum
## 参数
### autovacuum
默认autovacuum = on，表示是否开启autovacuum

### log_autovacuum_min_duration
默认log_autovacuum_min_duration = -1，单位是ms，表示在规定时长内未完成的vacuum予以记录日志

### autovacuum_max_workers
autovacuum 最大的清理进程数

### autovacuum_work_mem
autovacuum 进程的内存使用

### autovacuum_naptime
两次vacuum启动的时间间隔

### autovacuum_analyze_threshold
自动analyze操作的最小行数，有利于对SQL语句进行更精准匹配到最好的执行计划

### autovacuum_analyze_scale_factor
表示进行analyze操作所需的变更量阈值，当表的insert、update、delete的tuple总数大于pg_ckass.reltuples * autovacuum_analyze_scale_factor
+ autovacuum_analyze_threshold时，触发analyze

### autovacuum_vacuum_scale_factor
autovacuum所需的变更量阈值，当单表的udpate、delete的tuple总数大于pg_class.reltuples * autovacuum_vacuum_scale_factor + autovacuum_vacuum_threshold时
触发vacuum操作

### autovacuum_vacuum_cost_limit
对vacuum进行评估阈值，如果超过这个时间阈值那么autovacuumn进程则会休眠autovacuum_vacuum_cost_delay时长





