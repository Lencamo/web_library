### 1、去重distinct

查询选项是在select关键字之后，有两个互斥值

* all：默认，表示保留所有记录
* <span style="color:red;">distinct</span>：去重，重复的记录（所有字段都重复）

```mysql
create table t_39(
	id int primary key auto_increment,
    goods_name varchar(50) not null,
    goods_price decimal(10,2) default 0.00,
    goods_color varchar(20),
    goods_weight int unsigned comment '重量，单位克'
)charset utf8;

insert into t_39 values(null,'mate10',5499.00,'blue',320),
(null,'mate10',5499.00,'gray',320),
(null,'nokia3301',1299,'black',420);

# 考虑所有字段的去重（不含逻辑主键）
select distinct goods_name,goods_price,goods_color,goods_weight from t_39;
select goods_name,goods_price,goods_color,goods_weight from t_39; # 保留所有

# 不考虑颜色去重
select distinct goods_name,goods_price,goods_weight from t_39;
select all goods_name,goods_price,goods_weight from t_39;
```



### 2、运算符

* 比较运算符
  * \>（大于）、\<（小于）、=（等于）、\>\=（大于等于）、\<\=（小于等于）、\<\>（不等于）
  * between  A  and  B：A和B之间（A小于B），包括A和B本身（数值比较）
  * <span style="color:red;">in</span> (数据1,数据2,...数据N)：在列举的数据之中
  * <span style="color:red;">like</span> 'pattern'：像上面样的，用于字符串比较
    * \_：单下划线，匹配对应位置的一个任意字符（ab_：ab开头+一个字符，匹配abc，ab1，但不能匹配abcd）
    * %：匹配当前位置（往后）任意数量任意字符（ab%：ab开头+任意数量任意字符，匹配abc，ab1，abcd）
* 逻辑运算符
  * and（逻辑与）、or（逻辑或）、not（逻辑非）
* null运算符
  * is null（为空）、is not null（不为空）

```mysql
# 成绩条件：成绩是数值，又是比大小，可以直接使用比较运算符
select * from t_35 where score < 60;

# 成绩条件：区间60到90，可以有两种解决方案 区间: []
select * from t_35 where score between 60 and 90;
select * from t_35 where score >= 60 and score <= 90;

# 成绩条件：成绩为null，所以不能用比较符号查，只能使用is null实现
select * from t_35 where score is null;

# in / like
select * from Student where name like '王%';💖
select * from Student where id in(1,2,3);
```

### 3、统计函数（聚集函数）

分组统计需要使用统计函数

* group_concat()：将组里的某个字段全部保留
* any_value()：不属于分组字段的任意一个组里的值
* count()：求对应分组的记录数量
  * count(字段名)：统计某个字段值的数量（==NULL不统计==）
  * count(*)：统计整个记录的数量（较多）
* sum()：求对应分组中某个字段是和
* max()/min()：求对应分组中某个字段的最大/最小值
* avg()：求对应分组中某个字段的平均值

```mysql
create table t_40(
id int primary key auto_increment,
name varchar(10) not null,
gender enum('男','女','保密'),
age tinyint unsigned not null,
class_name varchar(10) not null comment '班级名称'
)charset utf8;

insert into t_40 values(null,'鸣人','男',18,'木叶1班'),
(null,'佐助','男',18,'木叶1班'),
(null,'佐井','男',19,'木叶2班'),
(null,'大蛇丸','男',28,'木叶0班'),
(null,'卡卡西','男',29,'木叶0班'),
(null,'小樱','女',18,'木叶1班'),
(null,'雏田','女',18,'木叶1班'),
(null,'我爱罗','男',19,'木叶1班'),
(null,'向日葵','女',6,'木叶10班'),
(null,'博人','男',8,'木叶10班'),
(null,'鼬','男',28,'木叶0班');

#查询
-- group_concat() 所有结果在一个记录里
select count(*),group_concat(name),class_name from t_40 group by class_name;
-- any_value() 任一一个结果在记录里
select count(*),any_value(name),class_name from t_40 group by class_name;

```

### 4、排序

可以设定分组结果的排序方式

* group by 字段名 [ASC]：升序排序（默认）
* group by 字段名 ==DESC==：降序排序

排序分为升序和降序：默认是升序

* order by 字段 [ASC]：升序
* order by 字段 DESC：降序



### 5、函数查询

#### ①字符串函数

[concat()函数](http://www.yiibai.com/mysql/sql-concat-in-mysql.html) - 将两个或多个字符串组合成一个字符串。✨

[length()函数&char_length()函数](http://www.yiibai.com/mysql/string-length.html) - 以字节和字符获取字符串的长度。✨

[left()函数](http://www.yiibai.com/mysql/left-function.html) - 获取指定长度的字符串的左边部分。

[replace()函数](http://www.yiibai.com/mysql/string-replace-function.html) - 搜索并替换字符串中的子字符串。

[substring()函数](http://www.yiibai.com/mysql/substring.html) - 从具有特定长度的位置开始提取一个子字符串。✨

[trim()函数](http://www.yiibai.com/mysql/trim.html) - 从字符串中删除不需要的字符。

[find_in_set()函数](http://www.yiibai.com/mysql/find_in_set.html) - 在逗号分隔的字符串列表中找到一个字符串。

[format()函数](http://www.yiibai.com/mysql/format-function.html) - 格式化具有特定区域设置的数字，舍入到小数位数。



#### ②日期与时间函数

[curdate()函数](http://www.yiibai.com/mysql/curdate.html) - 返回当前日期。

[datediff()函数](http://www.yiibai.com/mysql/datediff.html) - 计算两个`DATE`值之间的天数。

[day()函数](http://www.yiibai.com/mysql/day.html) - 获取指定日期月份的天(日)。

[date_add()函数](http://www.yiibai.com/mysql/date_add.html) - 将时间值添加到日期值。

[date_sub()函数](http://www.yiibai.com/mysql/date_sub.html) - 从日期值中减去时间值。

[date_format()函数](http://www.yiibai.com/mysql/date_format.html) - 根据指定的日期格式格式化日期值。

[dayname()函数](http://www.yiibai.com/mysql/dayname.html) - 获取指定日期的工作日的名称。

[dayofweek()函数](http://www.yiibai.com/mysql/dayofweek.html) - 返回日期的工作日索引。

[extract()函数](http://www.yiibai.com/mysql/extract.html) - 提取日期的一部分。

[now()函数](http://www.yiibai.com/mysql/now.html) - 返回当前日期和时间。

[month()函数](http://www.yiibai.com/mysql/month.html) - 返回一个表示指定日期的月份的整数。

[str_to_date()函数](http://www.yiibai.com/mysql/str_to_date.html) - 将字符串转换为基于指定格式的日期和时间值。

[sysdate()函数](http://www.yiibai.com/mysql/sysdate.html) - 返回当前日期。

[timediff()函数](http://www.yiibai.com/mysql/timediff.html) - 计算两个`TIME`或`DATETIME`值之间的差值。

[timestampdiff()函数](http://www.yiibai.com/mysql/timestampdiff.html) - 计算两个`DATE`或`DATETIME`值之间的差值。✨

[week()函数](http://www.yiibai.com/mysql/week.html) -  返回一个日期的星期数值。

[weekday()函数](http://www.yiibai.com/mysql/weekday.html) - 返回一个日期表示为工作日/星期几的索引。

[year()函数](http://www.yiibai.com/mysql/year.html) - 返回日期值的年份部分。



#### ③聚合函数

[MySQL聚合函数](http://www.yiibai.com/mysql/aggregate-functions.html) - 提供最常用的MySQL聚合函数的简要概述。

[avg()函数](http://www.yiibai.com/mysql/avg.html) -  计算一组值或表达式的平均值。

[count()函数](http://www.yiibai.com/mysql/count.html) - 计算表中的行数。

[instr()函数](http://www.yiibai.com/mysql/instr.html) - 返回子字符串在字符串中第一次出现的位置。 ✨

[sum()函数](http://www.yiibai.com/mysql/sum.html) - 计算一组值或表达式的总和。

[min()函数](http://www.yiibai.com/mysql/min.html) - 在一组值中找到最小值。

[max()函数](http://www.yiibai.com/mysql/max-function.html) - 在一组值中找到最大值。

[group_concat()函数](http://www.yiibai.com/mysql/group_concat.html) - 将字符串从分组中连接成具有各种选项(如`DISTINCT`，`ORDER BY`和`SEPARATOR`)的字符串。

[MySQL标准偏差函数](http://www.yiibai.com/mysql/standard-deviation.html) - 显示如何计算人口标准偏差和样本标准偏差。
