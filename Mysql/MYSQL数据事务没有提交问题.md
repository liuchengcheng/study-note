# MYSQL数据事务没有提交问题

* 查看事务
```mysql
select * from information_schema.innodb_trx;
show FULL PROCESSLIST
```

* 杀掉对应的进程
```
kill 13143
```
