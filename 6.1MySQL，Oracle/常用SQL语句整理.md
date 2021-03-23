查询 A 表和 B 表中有，但是不在 C 表中的，A，B，C 都有字段 d

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

查询一天的数据：

```sql
select * from tableA where to_days(column_time) = to_days(now());
select * from tableA where date(cloumn_time) = curdate();
```

查询出近一周的数据

```sql
select * from tableA where TO_DAYS(now()) - TO_DAYS(datecolumn) <=7;
select * from tableA where DATA_SUB(curdate(),INTERVAL 7 DAY) <= date(datecolumn)
//sqlserver
select * from 表 where datediff(week,时间字段,getDate()) = 0;
```

查询出近一个月的数据

```sql
select * from tableA where data_sub(curdate(),INTERVAL 30 DAT) <= date(datecolumn)
```

查询出近一年的数据：

```sql
select * from tableA where DATE_SUB(curdate(),INTRRVAL 1 YEAR) <= date(datecolumn)
select * from tableA where YEAR(datecolumn) = YEAR(NOW());
```
