# MYSQL批量操作

### 批量增加

```mysql
    insert into test(id,name) values (1,'1111'),(2,'sdfsfs')
```

### 批量更新

```mysql
    update
    	test
    set
    	status = case
    		WHEN id = 10000536274 THEN 1
    		WHEN id = 10000536275 THEN 1
    		WHEN id = 10000536276 THEN 1
    		WHEN id = 10000536277 THEN 1
    		WHEN id = 10000536278 THEN 1
    	end,
    	name = case
    		WHEN id = 10000536274 THEN '测试商品'
    		WHEN id = 10000536275 THEN '测试商品'
    		WHEN id = 10000536276 THEN '测试商品'
    		WHEN id = 10000536277 THEN '测试商品'
    		WHEN id = 10000536278 THEN '测试商品'
    	end
    WHERE
    	id IN(
    		10000536274,
    		10000536275,
    		10000536276,
    		10000536277,
    		10000536278
    	)

```