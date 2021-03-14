查询A表和B表中有，但是不在C表中的，A，B，C 都有字段 d

```sql
select * from A.a 
	left join B.b 
		on a.d = b.d
    where d not in (
        select a.d from A a,C c where a.d = c.d
    )
```



测

```sql
select * from A.a
		left join B.b
			on a.c = b.c and b.d > 20
		where 
```



主键自增，查询最后一条记录

```sql
select top 1 * from user order by id desc;
select * from user order by limit 1;
```

