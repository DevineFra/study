# dead tuple
为了完成数据库的MVCC多版本控制，以及数据的undo的功能，每行数据可能存在多个版本的数据。而过期版本的数据被弃用后，就会产生众多的死行 dead tuple
如果不及时清理这些dead tuple，轻则影响磁盘空间的利用，重则影响数据库的性能。


# AutoVacuum
## vacuum和analyze
autovacuum除了负责自动的vacuum以外还负责收集用于查询规划的数据分布统计信息。
vacuum用于恢复表中“死元组”占用的空间。
analyze操作分析数据库表的内容并收集有关每个表的每一列中值分布的统计信息

注意：analyze的负面影响更重一些，因为虽然vacuum的成本主要与死行数量成正比，但analyze实际上必须在每次执行时重新构建统计信息，这可能需要相当多的IO，另外

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

## 调参原则
1. 在执行大量更新和删除的表上，应该尽量降低比例因子，一遍更频繁低进行清理。
2. 在合理的硬件上需要增加节流参数，一百年清理工作能跟上。
3. 可以使用alter table设置每个表的参数，但是需要考虑复杂度。
4. 平衡清理死行带来的查询提效以及节省空间和性能损耗的负面影响。





