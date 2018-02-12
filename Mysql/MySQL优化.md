# MySQL优化

### 1.选择合适的存储引擎：InnoDB

### 2.为每一个表分配一个InnoDB FILE

### 3.保证数据从内存中读取，将数据保存到内存中

```
   3.1 足够大的innodb_buffer_pool_size  SHOW GLOBAL STATUS LIKE 'innodb_buffer_pool_pages_%';
   3.2 server上是否有足够内存用来规划，运行 echo 1 > /proc/sys/vm/drop_caches 清除操作系统的文件缓存。能够看到真正的内存使用量
```

### 4.数据预热

### 5.不要让数据存到SWAP中

### 6.定期优化重建数据库
> mysqlcheck -o –all-databases 会让 ibdata1 不断增大。真正的优化仅仅有重建数据表结构：
```sql
CREATE TABLE mydb.mytablenew LIKE mydb.mytable;
INSERT INTO mydb.mytablenew SELECT * FROM mydb.mytable;
ALTER TABLE mydb.mytable RENAME mydb.mytablezap;
ALTER TABLE mydb.mytablenew RENAME mydb.mytable;
DROP TABLE mydb.mytablezap;
```

### 7.降低磁盘写入操作
> 7.1 使用足够大的写入缓存 innodb_log_file_size
> 7.2 innodb_flush_log_at_trx_commit
> 7.3 避免双写入缓冲

### 8.提高磁盘读写速度
> RAID0 尤其是在使用 EC2 这样的虚拟磁盘 (EBS) 的时候，使用软 RAID0 很重要

### 9.充分使用索引

### 10.分析查询日志和慢查询日志
```sql
log=/var/log/mysql.log
```
记录运行时间超过 1 秒的查询
```sql
long_query_time=1
log-slow-queries=/var/log/mysql/log-slow-queries.log
```

### 11.用 NOSQL 的方式使用 MYSQL
> B-TREE 仍然是最高效的索引之中的一个，全部 MYSQL 仍然不会过时。
> 用 HandlerSocket 跳过 MYSQL 的 SQL 解析层。MYSQL 就真正变成了 NOSQL。

### 12.其他
```sql
单条查询最后添加 LIMIT 1，停止全表扫描。
将非”索引”数据分离，比方将大篇文章分离存储，不影响其它自己主动查询。
不用 MYSQL 内置的函数。由于内置函数不会建立查询缓存。
PHP 的建立连接速度很快，全部能够不用连接池。否则可能会造成超过连接数。当然不用连接池 PHP 程序也可能将
连接数占满比方用了 @ignore_user_abort(TRUE);
使用 IP 而不是域名做数据库路径。避免 DNS 解析问题
```