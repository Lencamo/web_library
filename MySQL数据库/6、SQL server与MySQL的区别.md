## 一、函数的区别

### 1、limit语句

mysql中

```mysql
SELECT age
FROM person
ORDER BY age ASC
LIMIT 1 OFFSET 2
```

SQL server中

```sql
SELECT TOP 3 WITH TIES *
FROM person
ORDER BY age ASC

#应用
-- 从 "Persons" 表中选取 50% 的记录
SELECT TOP 50 PERCENT * FROM Persons
```

### 3、DATEDIFF() 函数

mysql中

```mysql
#DATEDIFF() 函数返回两个日期之间的天数
-- DATEDIFF(date1,date2)
SELECT DATEDIFF('2008-12-30','2008-12-29') AS DiffDate
```

SQL server中

```sql
#DATEDIFF() 函数返回两个日期之间的时间
-- DATEDIFF(datepart,startdate,enddate)
SELECT DATEDIFF(day,'2008-12-29','2008-12-30') AS DiffDate

/*
注意：
	SQL server中的DATEDIFF() 函数
							等效于mysql中的💖TIMESTAMPDIFF() 函数
*/
-- TIMESTAMPDIFF(unit,begin,end);

```

### 4、DATE函数

mysql中

```mysql
1、NOW()	返回当前的日期和时间
	-- CURDATE()	返回当前的日期
	-- CURTIME()	返回当前的时间
	
2、DATE()	提取日期或日期/时间表达式的日期部分
	-- EXTRACT(unit FROM date) 返回日期/时间按的单独✨unit部分

3、DATE_FORMAT(date,format)	用不同的格式🎉format显示日期/时间

4、DATEDIFF(date1,date2)	返回两个日期之间的天数
	-- TIMESTAMPDIFF(unit,begin,end); 相当于SQL sever中的DATEDIFF() 函数
	
5、DATE_ADD()	给日期添加指定的时间间隔
   DATE_SUB()	从日期减去指定的时间间隔
```

SQL sever中

```mysql
1、GETDATE()	返回当前日期和时间

2、DATEPART(datepart,date) 返回日期/时间的单独✨datepart
	
3、CONVERT(data_type(length),data_to_be_converted,style)	用不同的格式🎉style显示日期/时间

4、DATEDIFF(datepart,startdate,enddate)	返回两个日期之间的时间

5、DATEADD()	在日期中添加或减去指定的时间间隔
```



## 二、外键约束

mysql中

```mysql
/*
	* 外键字段必须与对应表的主键字段类型、长度、字符集、引擎一致
	* 外键字段本身要求是一个索引（创建外键会自动生成一个索引）
*/
#创建专业表和学生表，学生表中的专业id指向专业表id
-- [constraint`外键名`] foreign key(当前表字段名) references 外部表(主键字段)
create table t_1(
	id int primary key auto_increment,
    name varchar(50) not null unique
)charset utf8;

create table t_49(
	id int primary key auto_increment,
    name varchar(50) not null,
    c_id int,
    foreign key(c_id) references t_47(id)
    /*
    	外键可以不指定名字，系统会自动生成
    constraint `c_id` foreign key(c_id) references t_47(id)
    */
)charset utf8;
```

SQL server中

```mysql
foreign key(Cid) references C(Cid) on update cascade on delete cascade
```

