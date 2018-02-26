# MYSQL记录查询慢的日志

- 设置记录查询慢的语句日志
```sql
1，slow_query_log
这个参数设置为ON，可以捕获执行时间超过一定数值的SQL语句。
```

- 记录日志的执行时间
```sql
2，long_query_time
当SQL语句执行时间超过此数值时，就会被记录到日志中，建议设置为1或者更短。
```

- 记录日志的文件名
```sql
3，slow_query_log_file
记录日志的文件名。
```

- 捕获到所有未使用索引的SQL语句
```sql
4，log_queries_not_using_indexes

这个参数设置为ON，可以捕获到所有未使用索引的SQL语句，尽管这个SQL语句有可能执行得挺快。
```