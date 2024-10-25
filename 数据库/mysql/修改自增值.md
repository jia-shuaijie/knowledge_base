mysql集群这个方法会失效!

将自增值从指定值开始
```sql
alter table users AUTO_INCREMENT=10000;
```

创建表时默认自增序列从指定数值开始
```sql
CREATE TABLE TABLE_1 ( 
	id INT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT, 
	-- ID列为无符号整型，该列值不可以为空，并不可以重复，而且自增。
	NAME VARCHAR(5) NOT NULL)
	AUTO_INCREMENT = 100;
````