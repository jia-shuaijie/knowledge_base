创建一个自增序列
```sql
CREATE SEQUENCE "public"."xxx_id_seq"
INCREMENT 1
MINVALUE  1
NO MAXVALUE
START 1
```

查询某个序列的下一个值
```sql
-- 当把nextval('xxx_id_seq'::regclass)设置到ID的默认值时可用实现自增操作
select nextval('xxx_id_seq'::regclass)
```

有时会碰到表内ID已经增长很多了还是序列却从0开始了,这种情况就需要将当前表内的最大值设置为序列的初始值
```sql
SELECT setval( 'xxx_id_seq', (SELECT MAX(id) FROM `表名称` ) +1 );
```
