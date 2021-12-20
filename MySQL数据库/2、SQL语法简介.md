## 一、SQL简介

### **SQL**：

Structured Query Language，结构化查询语言，是一种针对关系型数据库特殊标准化的编程语言

* SQL是一种编程语言

* 能够实现用户数据库查询和程序设计
* SQL根据操作不同，分为几类
  * DDL：Data Definition Language，数据定义语言，用于创建<span style="color: red;">数据结构</span>
  * DML：Data Manipulation Language，数据操作语言，用于写<span style="color: blue;">数据内容</span>操作（增删改）
  * DQL：Data Query Language，数据==查询==语言，用于查询和检索数据 
  * DCL：Data Control Language，数据控制语言，用于用户权限管理
  * TPL：Transaction Process Language，事务处理语言，辅助DML进行事务操作（因此也归属于DML）   

### **SQL语法规则**：

SQL是一种结构化编程语言

* 基础SQL指令通常是以行为单位
* SQL指令需要语句结束符，默认是英文分号：`;`、\g、==\G==
  * \G：主要用于查询数据，立体展示结果
* SQL指令类似自然语言
* 编写的SQL中如果用到了<span style="color: red">关键字</span>或者<span style="color: red">保留字</span>，需要使用✨反引号``来包裹，让系统忽略
* ==_==表示匹配一个字符（固定位置），==%==表示匹配N个字符

### SQL注释：

①单行注释

```mysql 
#注释内容

-- 注释内容
```

②多行注释

```mysql
/*
	注释内容
*/

```



## 二、图解

<img src="https://upload-images.jianshu.io/upload_images/1505726-2c13837493927176.png" alt="img" style="zoom:80%;" />

