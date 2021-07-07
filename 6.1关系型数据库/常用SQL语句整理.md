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



### 有关时间的操作

1：函数

```sql
-- 获取当前系统时间
now()
select now();
-- 进行日期格式化
select date_format(now(),'%Y%m%d');
-- %y 两位数字表示的年份
%Y 四位数字表示的年份
%m 两位数字表示的月份（01, 02, ..., 12）
%c 数字表示的月份（1, 2, ...., 12）
%S, %s 两位数字形式的秒（ 00,01, ..., 59）
%I, %i 两位数字形式的分（ 00,01, ..., 59）
%H 两位数字形式的小时，24 小时（00,01, ..., 23）
%h 两位数字形式的小时，12 小时（01,02, ..., 12）
%k 数字形式的小时，24 小时（0,1, ..., 23）
%l 数字形式的小时，12 小时（1, 2, ..., 12）
%T 24 小时的时间形式（hh:mm:ss）
%r 12 小时的时间形式（hh:mm:ss AM 或hh:mm:ss PM）
%p AM或PM
%W 一周中每一天的名称（Sunday, Monday, ..., Saturday）
%a 一周中每一天名称的缩写（Sun, Mon, ..., Sat）
%d 两位数字表示月中的天数（00, 01,..., 31）
%e 数字形式表示月中的天数（1, 2， ..., 31）
%D 英文后缀表示月中的天数（1st, 2nd, 3rd,...）
%w 以数字形式表示周中的天数（ 0 = Sunday, 1=Monday, ..., 6=Saturday）
%j 以三位数字表示年中的天数（ 001, 002, ..., 366）
%U 周（0, 1, 52），其中Sunday 为周中的第一天
%u 周（0, 1, 52），其中Monday 为周中的第一天
%M 月名（January, February, ..., December）
%b 缩写的月名（ January, February,...., December）

```

2：计算

```sql
-- 为日期增加一个时间间隔
set @dt = now();

select date_add(@dt, interval 1 day); -- add 1 day
select date_add(@dt, interval 1 hour); -- add 1 hour

-- 为日期减去一个时间间隔 date_sub()
-- 日期时间相减函数：
datediff(date1,date2)
timediff(time1,time2)
select datediff('2008-08-08', '2008-08-01'); -- 7
select datediff('2008-08-01', '2008-08-08'); -- -7


```

