MySQL中有四种数据类型规范

* 整数类型：只能存储整数
* 小数类型：可以存储有效数值
* 字符串类型：存储字符串数据
* 时间日类类型：存储时间日期格式数据

数值型存储在MySQL中分为有符号（有负数）和<span style="color:red;">无符号（纯正数）</span>

- ==unsigned==修饰整数，表示无符号（从0开始）

可以通过zerofill来强制让不够宽度的数据补充前置0来达到显示宽度

* ==zerofill==默认要求整型为无符号
* zerofill通常用来制作一些规范宽度的数据



## 一、整数类型

### 1、分类及选择

①MySQL中为了**数据空间**的有效使用，设定了五种整数类型

* 迷你整型：tinyint，<span style="color:red;">使用**1个字节**存储整数</span>，最多存储256个整数（-128~127）
* 短整型：smallint，使用**2个字节**存储整数
* 中整型：mediumint，使用**3个字节**存储整数
* 标准整型：int，<span style="color:red;">使用**4个字节**存储整数</span>
* 大整型：bigint，使用**8个字节**存储

②数值型存储在MySQL中分为有符号（有负数）和<span style="color:red;">无符号（纯正数）</span>

- ==unsigned==修饰整数，表示无符号（从0开始）

#### 应用：

```mysql
#设计一个表记录个人信息：年龄、头发数量
-- 年龄：没有负数，正常年龄也不超过200岁，迷你整型无符号即可
-- 头发数量：没有负数，大概在几百万根，所以标准整型无符号即可
create table t_7(
	age tinyint unsigned, # unsigned修饰整数，表示无符号（从0开始）
    haircount int unsigned
)charset utf8;


#设计一个表记录4S店的汽车销量信息：库存数量、销量、采购量
-- 4S店经常是先卖后进货，所以库存可能为负数，一个店铺的库存数通常不会太多，那么小整型即可
-- 销量通常全国一个月也就几万台，所以基本上小整型就够了，但是为了保证后续可能存在的爆发，那么中整型也是绝对够了的，而且不会是负数
-- 采购通常是正向采购，所以不会出现负数，而采购数量与销量持平就好
create table t_8(
    stock smallint,
    sales mediumint unsigned,
    purchase mediumint unsigned
)charset utf8;
```

### 2、宽度控制

**显示宽度**：int(L)，整数在数据库中显示的符号（数字+符号）个数

* 显示宽度一般是类型能表示的最大值对应的数字个数（通过desc查看表字段显示）
* 显示宽度包含符号（如果允许为负数，`-`负号会增加一个宽度）
* 显示宽度可以主动控制：创建字段时加括号确定
* 显示宽度不会影响类型能表示的最大数值
* 可以通过**zerofill**让不够宽度的数值补充到对应宽度：在字段类型后使用zerofill

```mysql
#间接查看宽度
desc t_1;

#宽度控制
-- ①显示最大宽度
create table t_9(
	a tinyint,
    b tinyint unsigned
)charset utf8;
desc t_1;

-- ②自定义宽度
alter table t_9 add c tinyint(2) unsigned;

-- ③宽度设置不影响数据大小👀
insert into t_9 values(1,1,1); #小于显示宽度
insert into t_9 values(100,100,100); #大于显示宽度
```

#### 总结：

1、显示宽度是显示整型能表示的最多符号数量

2、显示宽度能主动设置，但是绝对不会改变类型本身能表示的数据大小

3、可以通过zerofill来强制让不够宽度的数据补充前置0来达到显示宽度

* zerofill默认要求整型为无符号
* zerofill通常用来制作一些规范宽度的数据



## 二、小数类型

### 1、浮点型float、double

①浮点数又称之为精度数据，分为两种

* 单精度：float，使用4个字节存储，精度范围为<span style="color:red;">6-7位</span>有效数字
* 双精度：double，使用8个字节存储，精度范围为14-15位有效数字

②精度可以指定整数和小数部分

* 默认不指定，整数部分不超过最大值，小数部分保留2位
* 可以指定：float/double(总长度,小数部分长度)

③浮点数超过精度范围✨会==自动==进行**四舍五入**

- 所以：浮点数是用来记录一些不需要特别精确的数值或者小数数值的

#### 应用：

```mysql
/*
确定数据的大小或者精度的要求范围
	* 6-7位有效数字使用float
	* 14-15位有效数字使用double
*/

#记录宇宙中恒星、行星的数量
create table t_10(
	h_star float unsigned,
    x_star float(20,0) unsigned
)charset utf8;
--
insert into t_10 values(1234567890,1.2E10);  /* 实际存储结果：1234570000 ，1200000000 */


#记录商品的价格
create table t_11(
	goods_name varchar(20),
    goods_price float
)charset utf8;
--
insert into t_11 values('Nokia3310',199.99);   /* 实际存储结果：199.99 */
insert into t_11 values('Nokia6100',1999.9999);  /* 实际存储结果：2000 */
```

### 2、定点型decimal

①定点数是用来存储精确的小数的

②定点数可以指定长度

* decimal：默认
  * 整数部分为10位
  * 小数部分为0
* <span style="color:red;">decimal(有效位数,小数位数)</span>
  * 整数部分为：有效位数 - 小数位数
  * 有效数位不超过65个

③定点数的存储模式不是固定长度，每9个数字使用4个字节存储，所以数据越大占用的存储空间越长

```mysql
#记录个人资产情况：资产和负债
create table t_12(
    money decimal(14,2),
    bet decimal(10,2)
)charset utf8;

--
insert into t_12 values(1111111111.12,1111111.999);
insert into t_12 values(1111111111.12,99999999.999); # 错误：进位导致正数部分超过指定范围
```



## 三、字符串类型

### 1、定长型char(L)

定长的访问效率较高，但是空间利用率不高

* 固定长度的数据使用定长

* 定长最大数据长度指定不超过255字符

定长是固定存储空间，对应的是字符长度，而不是字节长度

* 实际存储空间：L字符 * 字符集对应字节数

```mysql
##记录个人信息：身份证信息和手机号码
# 身份证为固定长度18位（数字）
# 手机号码是11位固定长度（数字）
create table t_13(
	id_number char(18),
    phone_number char(11)
)charset utf8;
insert into t_13 values('440111999912120304','13512345678');
```

### 2、变长型varchar(L)

变长型的L也是指**字符**而不是字节

* L指定的是最大存储的数据长度
* L最大值理论是65535
* 实际存储空间：实际字符数 * 字符集对应字节数 + 记录长度

变长需要额外产生1-2个字节，用来记录实际数据的长度

* 数据长度小于256个，多1个字节
* 数据长度大于256个，多2个字节

```mysql
##记录个人信息：用户名、密码、姓名、身份证
# 用户名不确定长度，最长不超过50个字符
# 密码不确定长度，最潮超过15个字符
# 姓名不确定长度，最长不超过10个字符
# 身份证固定长度，18个字符
create table t_14(
	`username` varchar(50),
    `password` varchar(15),
    `name` varchar(10),
    `id_number` char(18)
)charset utf8;

insert into t_14 values('username','password','name','444111999912121111');
```

### 3、文本text、blob

**文本字符串**：text/blob，专门用来存储较长的文本

* 文本字符串通常在超过255个字符时使用
* 文本字符串会<span style="color:red;">自动根据文本长度选择适合的具体类型</span>
* 一般在文本超过255个字符时，都会使用text（blob现在极少使用）

**文本字符串包含两大类**

* text：普通字符
  * tinytext：迷你文本，不超过`2 ^ 8 -1`个字符
  * text：普通文本，不超过 `2 ^ 16 - 1`个字符
  * mediumtext：中型文本，不超过 `2 ^ 24 - 1` 个字符
  * longtext：长文本，不超过 `2 ^ 32 - 1` 个字符（4G）
* blob：二进制字符（与text类似）
  * tinyblob
  * blob
  * mediumblob
  * longblob

```mysql
##记录新闻信息：标题、作者和内容
# 标题一般不会超过50个字符，varchar
# 作者一般不会超过10个字符：varchar
# 内容通常都很长，使用text
create table t_15(
	author varchar(10),
    title varchar(50),
    content text
)charset utf8;

insert into t_15 values('佚名','给联合国的一封信','给联合国的一封信...');
```

### 4、枚举enum

**枚举**：一种映射存储方式，以较小的空间存储较多的数据

* 枚举是在定义时确定可能出现的可能
* 枚举在定义后数据只能出现定义时其中的一种
* 枚举<span style="color:red;">类似一种单选框</span>
* 枚举使用1-2个字节存储，最多可以设计65535个选项
* 枚举实际存储是使用数值，映射对应的元素数据，从1开始
* 枚举语法：enum(元素1,元素2,...元素N)

```mysql
#记录人群类型：小朋友、少年、青年、中年、老年，每个人实际只属于一种类别
-- 要保证未来数据只能出现在某种可能中，所以要先列出来，可以使用enum
create table t_16(
	type enum('小朋友','少年','青年','中年','老年')
)charset utf8;

insert into t_16 values('少年');
insert into t_16 values('仙人');	# 不存在的数据不能插入


#enum是建立映射关系，然后实际存储✨是数字，数值是按照元素顺序从1开始
-- 可以使用字段 + 0来判定数据具体的效果（字符串转数值为0）
select type,type + 0 from t_16;
insert into t_16 values(5);
```
| 枚举数据 | 映射值         |
| -------- | -------------- |
| 数据1    | 1              |
| 数据2    | 2              |
| ...      | ...            |
| 数据N    | N（小于65535） |

```mermaid
graph LR
A(指令开始)-->B[插入数据]
B-->C[读取映射关系<br>元素==数值<br>数值==数值]
C-->D[数值存储到字段]
D-->E((结束))
```

### 5、集合set

**集合**：set，一种映射存储方式，以较小的空间存储较多的数据

* 集合是在定义时确定可能出现的元素进行穷举
* 集合在定义后数据只能出现定义时其中的元素（可以是多个）
* 集合<span style="color:red;">类似一种多选框</span>
* 集合使用1-8个字节存储数据，最多可以设计64个元素
* 集合实际存储是使用数值（==二进制位==），映射对应的元素数据，每个元素对应一个比特位
  * 数据存在：对应位为1
  * 数据不存在：对应位为0
* 集合语法：set(元素1,元素2,...元素N)

```mysql
#记录个人的球类爱好，有篮球、足球、羽毛球、网球、乒乓球、排球、台球、冰球
-- 爱好可以是多种，并非固定的，但是只能从规定的类型中选择
create table t_17(
	hobby set('足球','篮球','羽毛球','网球','乒乓球','排球','台球','冰球')
)charset utf8;

insert into t_17 values('足球');
insert into t_17 values('冰球,台球,篮球');
```

| 枚举数据 | 映射值         |
| -------- | -------------- |
| 数据1    | 1              |
| 数据2    | 2              |
| ...      | ...            |
| 数据N    | N（小于65535） |

```mermaid
graph LR
A(指令开始)-->B[插入数据]
B-->C[读取映射关系<br>元素==数值<br>数值==数值]
C-->D[数值存储到字段]
D-->E((结束))
```



## 四、时间日期类型

时间日期类型（不常用：通常使用真正时间戳存储数据，然后PHP进行灵活解读）

### 1、年year

①year能够表示的范围是<span style="color:red;">1901-2155年</span>（256年）

* year允许用户使用两种方式设计（效果一样）
  * year
  * year(4)

②Year类型允许使用2位数来插入，系统会自动匹配对应的年份

* 69以前：系统加上2000
* 69以后：系统加上1900

③Year类型的特殊值是0000，可以使用00或者0000插入

```mysql
#记录个人的出生年份
create table t_18(
	y1 year,
    y2 year(4)
)charset utf8;


insert into t_18 values(1901,2155);   

insert into t_18 values(69,70);   #相当于： （2069,1970）
```

#### 总结：

对于记录古代历史，year往往不能满足我们的需求，所以通常使用char类型

### 2、时间戳timestamp🎉

**时间戳**：timestamp，基于<span style="color:red;">格林威治时间</span>的时间记录(最新的更改时间)

* MySQL中时间戳表现形式不是秒数，而是年月日时分秒格式
  * ==YYYY-MM-DD== ==HH:II::SS==
  * YYYYMMDDHHIISS
* timestamp使用4个字节存储
  * 表示范围是<span style="color:red;">1971年1月1日0时0分0秒</span>-2155年12月31日23是59分59秒
  * timestamp可以使用0000-00-00 00:00:00
* timestamp的特点是所对应的记录不论哪个字段被更新，该字段都会更新到当前时间

```mysql
#记录商品库存的最后更新时间
create table t_19(
	goods_name varchar(10),
    goods_inventory int unsigned,
    change_time timestamp
)charset utf8;

insert into t_19 values('Nokia3110',100,'1971-01-01 00:00:00');
insert into t_19 values('Nokia7100',100,'19710101000000');

#注意：
/*
在MySQL8以后，取消了timestamp的默认自动更新，如果需要使用，需要额外使用属性： 💖on update current_timestamp
*/
alter table t_19 add c_time timestamp on update current_timestamp;

update t_19 set goods_inventory = 80;
```

### 3、日期date

**日期**：date，用来记录年月日信息

* 使用3个字节存储数据，存储区间是1000 - 9999年，跨度很大，可放心使用
* 存储日期的格式为：YYYY-MM-DD
* 存储的范围是：==1001-01-01==✨~✨==9999-12-31==👏

```mysql
#记录个人生日
create table t_20(
	name varchar(10),
    birth date
)charset utf8;

insert into t_20 values('Jim','2000-12-12');
insert into t_20 values('Tom','10011212');
```

### 3、日期时间datetime

**日期时间**：datetime，用来综合存储日期和时间

* 使用8个字节存储数据
* 存储格式为：YYYY-MM-DD HH:II:SS
* 存储区间为：==1000-01-01== 00:00:00 到==9999-12-31== 23:59:59

```mysql
#记录个人具体的出生时间
create table t_21(
	name varchar(10),
    birth datetime
)charset utf8;

insert into t_21 values('Jim','2000-12-12 12:12:12');
insert into t_21 values('Tom','10011212182323');
```

**实际开发中**：因为编程语言（PHP）的强大，实际存储的时候通常不会使用这种类型

* 占用较大存储空间
* 处理不够灵活（固定格式）
* 使用int unsigned<span style="color:red;">存储时间戳</span>然后利用PHPdate进行格式处理

### 4、时间time

**时间**：time，用来记录时间或者时间段

* 使用3个字节存储数据
* 数据范围是 `-838:59:59` - `838:59:59`
* 数据插入的格式分为两种
  * 时间格式：[H]HH:II:SS（[]表示可以没有）
  * 时间段格式：D HH:II:SS（D表示天）

```mysql
# 具体登录时间可以使用💖时间戳（包含年月日时分秒信息）
# 也可以时间datetime格式，或者date+time双字段格式（具体后面学习范式时会知道该怎么用）
create table t_22(
	login_time1 int unsigned,
    login_time2 datetime,
    login_date date,
    login_time3 time
)charset utf8;

insert into t_22 values(12345678,'2000-12-12 12:12:12','2000-12-12','12:12:12');
insert into t_22 values(1234567,'2000-12-12 12:12:12','2000-12-12','3 12:12:12');
```



**应用：**

time类型通常被用来做<span style="color:red;">时间段</span>计算：如多少天后的什么时间点（可以理解为过期检查）



## 五、总结

MySQL中有很多类型用来规范数据格式

* 整数类型（常用）
  * 常用类型：tinyint、int
* 小数类型（常用）
  * 常用类型：decimal、float
* 字符串类型（常用）
  * 常用类型：char、varchar、text
* 时间日期类型（不常用：通常使用真正时间戳存储数据，然后PHP进行灵活解读）



## 六、数据库记录长度

MySQL中规定🤔一条记录所占用的存储长度最长==不超过65535==个**字节**

* 记录长度为表中所有字段预计占用的长度之和
* 所有字段只有允许Null存在，系统就会预留一个字节存储Null（多个Null也只要一个就好）
* 因为MySQL记录长度的存在，<span style="color:red;">varchar永远达不到理论长度</span>
  * GBK存储：65535（字符） * 2 + 2 = 131072（字节）
  * UTF8存储：65535（字符） * 3 + 2 = 196607（字节）
* 一般数据长度超过255个字符都会使用text/blob进行存储（数据存储不占用记录长度）

### 演示：

```mysql
#GBK表能存储的最大varchar字符串长度
create table t_32(
	content varchar(65535)
)charset gbk;	# 错误

create table t_32(
	content varchar(32767)  /* 32767 * 2 + 2 > 65535 */
)charset gbk;	# 错误

create table t_32(
	content varchar(32766)  /* 32766 * 2 + 2 = 65535 */
)charset gbk;	


#UTF8表能存储的最大varchar字符串长度
create table t_33(
	content varchar(65535)
)charset utf8;	# 错误

create table t_33(
	content varchar(21844)  /* 21844 * 3 + 2 = 65535 */
)charset utf8;


#Null也要占用一个字节💖
create table t_34(
    id tinyint,     /*这里默认为Null，占一个字节*/
	content varchar(21844)
)charset utf8;	# 错误   /* 21844 * 3 + 2 + 1 > 65535 */

create table t_34(
    id tinyint not null,
	content varchar(21844) not null
)charset utf8;
```

1、MySQL的记录长度是从设定表的时候就会检查所有字段加起来的预占用长度是否超过65535个字节

* 超过：创建失败
* 不超过：创建成功

2、创建表字段的时候要使用text/blob来避免长字符串出现，超过MySQL记录长度

3、Null是个细节，一条记录只要允许出现Null就会占用记录长度里的一个字节

