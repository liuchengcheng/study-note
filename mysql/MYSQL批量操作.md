# MYSQL批量操作

### 批量增加

```mysql
    insert into a(id,name) values (1,'1111'),(2,'sdfsfs')
```

### 批量更新

```mysql
    update
    	b_sku
    set
    	status = case
    		WHEN skuid = 10000536274 THEN 1
    		WHEN skuid = 10000536275 THEN 1
    		WHEN skuid = 10000536276 THEN 1
    		WHEN skuid = 10000536277 THEN 1
    		WHEN skuid = 10000536278 THEN 1
    	end,
    	name = case
    		WHEN skuid = 10000536274 THEN '测试商品，请勿下单：商家ID:20032 类目ID：9733 size1'
    		WHEN skuid = 10000536275 THEN '测试商品，请勿下单：商家ID:20032 类目ID：9733 size0'
    		WHEN skuid = 10000536276 THEN '测试商品，请勿下单：商家ID:20032 类目ID：9733 size2'
    		WHEN skuid = 10000536277 THEN '测试商品，请勿下单：商家ID:20032 类目ID：9733 size3'
    		WHEN skuid = 10000536278 THEN '测试商品，请勿下单：商家ID:20032 类目ID：9733 size4'
    	end
    WHERE
    	skuid IN(
    		10000536274,
    		10000536275,
    		10000536276,
    		10000536277,
    		10000536278
    	)

```