## 一、插入数据(insert into ___ values)

```mysql
#全字段插入
/*
	* 值列表必须与字段列表顺序一致
	* 值列表的每个数据类型必须与字段类型一致
*/
insert into 表名 values(字段列表顺序对应的所有值);
--
insert into t_3 values(1,'440111200011111101','Jim','Green');


#部分字段插入
/*
	* 字段列表可以顺序随意✨
	* 值列表必须与指定的字段列表顺序一致
	* 值列表元素的类型必须与字段列表的类型一致
*/
insert into 表名 (字段列表) values(字段列表对应的值顺序列表);
--
insert into t_3 (id,name) values(2,'Tom');

#批量插入数据💖
/*
组装成批量插入SQL指令
	*字段为全字段（逻辑主键不存在没问题）：全字段批量插入SQL
	*部分字段：组织字段列表并检查其他字段是否允许默认值
*/
-- 全字段
insert into t_30 values(null,'Tom','Computer',90),(null,'Lily','Computer',92);

-- 部分字段插入
insert into t_30 (stu_name,course) values('Tony','English'),('Ray','Math');
```

批量插入可以一次性解决多条数据插入，能够有效降低客户端占用问题，提升数据操作效率

* MySQL8以后默认有事务安全，即批量要么都成功要么都失败，不会出现部分问题



## 二、更新数据(update ___  set)

**更新数据**：即更新某个已有<span style="color: red;">字段的值</span>

```mysql
#更新数据
update 表名 set 字段 = 新值[,字段 = 新值] [where条件筛选];

-- 更新单个
update t_3 set sfz = '440100200010100001';
-- 更新多个
update t_3 set name = 'Lily',sfz = '440100200010100002' where id = 1;

-- 限制更新：对会员选3个发送10元红包（添加到账户）
update t_41 set account = account + 10 limit 3;
```



## 三、删除、清空数据

```mysql
#删除操作
/*
	1、数据删除是不可逆的操作
	2、数据删除通常都匹配条件部分删除
*/
delete from 表名 [where条件];
--
delete from t_3 where id = 2;

-- 清空数据
/*
	【清空数据的本质是先删除表，后创建表】
	清空数据表是一种比delete更彻底的数据删除方式，所以使用之前必须要慎重
*/
truncate t_41;
```



## 四、其他

### 1、蠕虫复制(insert into ___ select)

**蠕虫复制**：从已有表中复制数据直接插入到另外一张表（同一张表）

* 蠕虫复制的目标是**快速增加表中的数据**
  * 实现表中数据复制（用于==数据备份==或者==迁移==）
  * 实现数据的指数级递增（多用于测试）

```mysql
#复制语法
/*
	* 字段列表必须对应上
	* 字段类型必须匹配上
	* 数据冲突需要事先考虑
*/
insert into 表名 [(字段列表)] select 字段列表 from 表名;

--
create table t_35(
	id int primary key auto_increment,
    stu_name varchar(20) not null,
    course varchar(20) not null,
    score decimal(5,2)
)charset utf8;

-- 全部复制
insert into t_35 select * from t_30;

-- 选择复制
nsert into t_35 (stu_name,course,score) select stu_name,course,score from t_35;
```



### 2、主键冲突

1、主键冲突的解决方案有三种，但是需要根据具体的业务来选择合适的方式

* 忽略新数据：`insert ignore`
* 更新部分数据：`insert ... on duplicate key update`
* 全部替换：`replace into`

2、从效率上来讲，`insert into`不考虑冲突的效率最高，三种解决冲突的方式都会有效率下降（需要检索），其中三种本身的效率依次是：忽略新数据 > 更新部分数据 > 替换全部

```mysql
#方案一 :以原始数据为主
insert ignore into 表名 [(字段列表)] values(值列表); /*ignore*/
--
insert ignore into t_36 values('username','12345678',12345678);


#方案二 :更新部分数据
insert into 表名 [(字段列表)] values(值列表) on duplicate key update 字段 = 新值[,字段=新值...];
/*
	* 如果主键不冲突：新增
	* 如果主键冲突：更新指定字段
	* 上述方式适用于字段较多，但是可能冲突时数据变化的字段较少
*/
--
insert into t_37 values('username',12345678) on duplicate key update logintime = unix_timestamp();	# 当前时间戳



#方案三 :以新增数据为主
replace into 表名 [(字段列表)] values(值列表); /*replace ,效率✨没有insert高（需要检查是否冲突） */
/*
	* replace遇到主键重复就会先删除、后新增
	* 如果有较多字段需要更新：建议使用替换
*/
--
replace into t_38 values('username',unix_timestamp(),'{phone:uc}');

```

### 3、建立副本表

```mysql
#数据 + 结构副本
create t_1_1 (
    select *
    from t_1
);

#仅 结构副本
create t_1_2
like ren.t_1;  /*ren为t_1上面的库名*/
```



