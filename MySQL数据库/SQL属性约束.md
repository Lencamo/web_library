**属性**：建立在字段类型之后，对字段除类型之外的其他约束

* 属性是在定义表字段的时候针对每个字段进行属性设定
* 设定好的属性可以通过查看表字段==desc==进行查看
* 数据在进行增删改（写）操作时需要在满足字段的要求同时还要满足属性的要求

### 演示

```mysql
#查看表属性：desc 表名
desc t_1;

-- Field：字段名字
-- Type：数据类型
-- Null：是否为空（属性）
-- Key：索引类型（属性）
-- Default：默认值（属性）
-- Extra：额外属性
```

## 一、普通约束

### 1、NULL

数据是否为空确定

* 允许为空：不用考虑Null属性（默认为Null）
* 不允许为空：Not Null

```mysql
#用户信息表：用户名、密码、姓名、年龄、注册时间
create table t_23(
	username varchar(50) not null,
    password char(32) not null,
    name varchar(20),
    age tinyint unsigned,
    reg_time int unsigned not null
)charset utf8;

--
insert into t_23 values('username','password','Jim',20,123456789);

# 错误操作：reg_time✨不能为空，而默认为空，所以系统报错
insert into t_23 (username,password) values('username','password');
```



### 2、Default

**默认值**：default，在设计表字段的时候给定默认数据，在后续字段操作（数据新增）的时候系统没有检测到字段有数据的时候自动使用的值

* 默认值在字段设计的时候使用（默认值需要满足数据类型规范）
* 默认值通常设计的是字段容易出现的数据
  * 一般字段的默认值默认是Null
* 默认值触发
  * 在系统进行数据插入时自动检测触发
  * 主动使用default关键字触发默认值

```mysql
#用户开户：银行卡账号、身份证号码、姓名、账户余额
create table t_24(
	account varchar(19) not null,
    id_card char(18) not null,
    name varchar(20) not null,
    money decimal(16,2) default 0.00 not null   /* 设置账户余额默认为： 0.00 */
)charset utf8;

-- 使用
# 默认触发
insert into t_24 (account,id_card,name) /*未说明，系统自动检测*/
values('6226000000000001','44011120001212000x','Lily');

# 主动触发
insert into t_24 values('6226000000000002','440111200012120011','Tom',default);
insert into t_24 values('6226000000000003','440111200012120022','Jim',100);    /*自定义设置属性值*/
```



### 3、主键primary key

类似于支付宝账号注册，注册时不允许有相同的账号名称（通常，随机分配个人id，然后自己为id设置昵称。

1、主键的作用就是控制对应字段的数据具有<span style="color: red;">唯一性</span>（不能重复）,并且默认设置了<span style="color: red;">Not Null</span>

2、一张表只能有一个主键

3、虽然主键可以用来保证数据的唯一性，但是一般都是使用逻辑主键作为主键字段（保证唯一性还有其他方式，如唯一键）

- **逻辑主键**：数据没有具体业务意义，纯粹是一种数值数据
  * 逻辑主键通常是整数：int
  * 逻辑主键目的是方便检索和数据安全（不暴露数据真实信息）
- 复合主键：多个字段共同组成不能重复的数据
  * primary key(字段1,字段2,...字段N)
  * 联合主键使用不多，一般也不会超过2个字段

4、通常也不怎么使用复合主键

```mysql
#银行账户信息：账户、姓名、余额
-- 银行账户具有唯一性，不能重复，也不允许为空
create table t_25(
    account varchar(17) primary key,
    name varchar(20) not null,
    money decimal(16,2) not null default 0.00
)charset utf8;

-- 复合主键
create table t_26(
	account varchar(17),
    name varchar(20),
    money decimal(16,2) not null default 0.00,
    primary key(account,name)
)charset utf8;

-- 一般使用逻辑主键
create table t_27(
	id int unsigned primary key,   /*常用的逻辑主键一般为✨ int类型，并且为整数*/
    account varchar(17) not null,
    name varchar(20) not null,
    money decimal(16,2) not null default 0.00
)charset utf8;


-- 错误演示
insert into t_27 values(1,'6226000000000001','Lily',default);
# 错误：主键1已经存在
insert into t_27 values(1,'6226000000000002','Tom',default);

# 联合主键就是联合字段加起来不重复即可
insert into t_26 values('6226000000000001','Tom',default);
insert into t_26 values('6226000000000002','Tom',default);
```

#### 主键管理

在创建表并且已经有数据后的维护

* 删除主键
* 追加主键
* 修改主键（先删除后新增）

```mysql
#删除主键
alter table t_26 drop primary key;

#后期添加主键（前提：该字段为Not Null）
alter table t_26 add primary key(account,name);

```



### 4、自增长auto_increment

**自增长**：auto_increment，被修饰的字段在新增时，自动增长数据

* 自增长<span style="color: red;">只能是整数类型</span>，而且对应的字段<span style="color: red;">必须是一个索引</span>（通常逻辑主键）
* 一张表只能有一个自动增长
* 自增长数据可以理解为一种默认值，如果主动给值，那么自动增长不会触发

```mysql
#记录学生信息：学号和姓名
-- 学生信息：学号自动增长
create table t_28(
    id int primary key auto_increment,
	stu_no int(8) zerofill not null,
    stu_name varchar(20) not null
)charset utf8;


# 使用自增长（可以使用NULL✨或者default来默认触发）
insert into t_28 values(null,1,'Jim');
insert into t_28 values(default,2,'Tom');

# 主动控制：自增长的值会从当前最大的值开始自动增长
insert into t_28 values(10,3,'Lily');
insert into t_28 values(null,4,'Lucy');
```

#### 自增长管理

自增长由两个变量控制：

* 初始值：`auto_increment_offset`，默认是1
* 步长：`auto_increment_increment`，默认值也是1
* 查看自增长控制：`show variables like 'auto_increment%';`

```mysql
set auto_increment_increment = 2;	# 当前用户当前连接有效（局部）
set @@auto_increment_increment = 2;	# 所有用户一直有效（全局）
```

1、自增长通常不会修改，如果有规则要求必须修改，通常也会在数据库运行前修改好

2、如果碰到要修改操作的，通常会选择全局修改而不是局部修改



### 5、唯一键unique key🎉

**唯一键**：unique key，用来维护数据的唯一性

* 一个表中可以有多个唯一键
* 唯一键与主键的区别在于<span style="color: red;">唯一键允许数据为Null</span>💖（而且Null的数量不限）
  * 为空：普通唯一键
  * 不为空：not null（唯一键与主键效果一样）
* 唯一键与主键一样，可以提升字段数据当做条件查询的效率（==索引==）
* 复合唯一键：多个字段共同组成
  * unique key(字段1,字段2,...字段N)
  * 一般不会出现，最多2个字段组成

#### 示例：

```mysql
#学生成绩表：一个学生只能有一个学科成绩，但是可以有多个学科
-- 学号和学科编号共同组成唯一
create table t_30(
	id int primary key auto_increment,
    stu_name varchar(20) not null,
    course varchar(20) not null,
    score decimal(5,2),
    unique key(stu_name,course)  /*💖*/
    /*或者自定义索引值：unique key stu_course (stu_name,course)*/
)charset utf8;

insert into t_30 values(null,'Jim','Math',50);   /* 一个人 可以有多个学科*/
insert into t_30 values(null,'Jim','English',80);
```

1、唯一键的目标是保证对应字段数据的唯一性

* 唯一键不限定数据是否为Null（Null不参与唯一判定）
* 复合唯一键：允许多个字段共同组成唯一性

2、唯一键能够弥补主键只有一个的特性（不限定数据量）

3、唯一键使用的位置应该要确保该字段数据会用作数据检索条件

#### 唯一键管理

在表创建后对唯一键的管理

* 删除唯一键：一张表中不止一个唯一键，所以删除方式是相对麻烦：
  * `alter table 表名 drop index 唯一键名字；`
* 新增唯一键：
  * `alter table 表名 add unique key(字段列表)；`

```mysql
#查看 系统为我们创建的唯一键名字
show create tables t_3[/G]

#删除唯一键
alter table t_30 drop index `stu_name`; ✨/*该索引值：stu_name 为系统自动创建的*/

#增加唯一键
alter table t_30 add unique key `stu_course` (stu_name,course);
```

注意：追加唯一键<span style="color: red;">要保证字段里的数据具有唯一性</span>



### 6、comment属性

**描述**：comment，是用文字描述字段的作用的

* comment代表的内容是对字段的描述
  * 方便以后自己了解字段的作用
  * 方便团队了解字段的作用
* 描述如果涉及到字符集（中文）一定要在创建表之前<span style="color: red;">设置好客户端字符集</span>（否则会出现描述乱码）

```mysql
#查看描述信息💖
show create tables t_3[/G]

# 学生成绩表中通常是存储学生学号
# 学科通常也是学科代码
create table t_31(
	id int primary key auto_increment,
    stu_no varchar(10) not null comment '学号',
    course_no varchar(10) not null comment '课程号',
    score decimal(5,2) comment '考试成绩',
    unique key `stu_course` (stu_no,course_no) [comment '学号和课程号组成唯一键']
)charset utf8;
```



## 二、高级约束

### 1、check属性💖

**创建**：

My SQL:

```mysql
CREATE TABLE Persons
(
Id_P int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CHECK (Id_P>0)   /* 💖 */
)
```

SQL Server / Oracle / MS Access:

```sql
CREATE TABLE Persons
(
Id_P int NOT NULL CHECK (Id_P>0),  /* 💖 */
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
```

**撤销：**

MySQL：

```mysql
ALTER TABLE Persons
DROP CHECK chk_Person
```

SQL Server / Oracle / MS Access：

```sql
ALTER TABLE Persons
DROP CONSTRAINT chk_Person
```

#### 应用：

```mysql
#多选一
create table Student
(
	sno char(10) primary key,
	sfzh char(18) unique,
	cname varchar(16) not null,
	sex char(2) check(sex='男' or sex='女'),    /*
    sql sever中：check(sex in('男','女'))
    */
	dept varchar(30) default'软件学院',
	birthday datetime,
        score float check(score between 0 and 100),
	polity char(8) check(polity='党员' or polity='团员' or polity='群众')
);

#带函数(条件)的选择
create table S(
Sid	nchar(10) primary key,
sname nchar(10),	
sex	nchar(2) default '男',
birthday Date,	
age	TINYINT,	
entrydate date,	
plandscape nchar(10) default '团员',
nativeplace	nvarchar(20),	
class TINYINT,
CHECK(class = (substring(Sid,7,2))),    ✨
CHECK(age = (TIMESTAMPDIFF(YEAR,@birthday,CURDATE())))
);
```

