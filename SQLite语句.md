---
title: SQLite语句
date: 2016-07-09 10:31:23
tags:
categories: 提高篇
---

## 一、介绍
#### 1、SQL语句的特点
* 不区分大小写（比如数据库认为user和UsEr是一样的）
* 每条语句都必须以分号 ; 结尾

###### SQL中的常用关键字有
select、insert、update、delete、from、create、where、desc、order、by、group、table、alter、view、index等等

**数据库中不可以使用关键字来命名表、字段**

#### 2、SQL语句的种类
###### 数据定义语句（DDL：Data Definition Language）
* 包括create和drop等操作
* 在数据库中创建新表或删除表（create table或 drop table）
###### 数据操作语句（DML：Data Manipulation Language）
* 包括insert、update、delete等操作
* 上面的3种操作分别用于添加、修改、删除表中的数据
###### 数据查询语句（DQL：Data Query Language）
* 可以用于查询获得表中的数据
* 关键字select是DQL（也是所有SQL）用得最多的操作
* 其他DQL常用的关键字有where，order by，group by和having

## 二、使用
#### 1、创表
###### 格式
* create table 表名 (字段名1 字段类型1, 字段名2 字段类型2, …) ;
* create table if not exists 表名 (字段名1 字段类型1, 字段名2 字段类型2, …) ;

###### 示例
* create table t_student (id integer, name text, age inetger, score real) ;

#### 2、字段类型
###### SQLite将数据划分为以下几种存储类型：
* integer : 整型值
* real : 浮点值
* text : 文本字符串
* blob : 二进制数据（比如文件）

###### 实际上SQLite是无类型的
* 就算声明为integer类型，还是能存储字符串文本（主键除外）
* 建表时声明啥类型或者不声明类型都可以，也就意味着创表语句可以这么写：
* create table t_student(name, age);

**为了保持良好的编程规范、方便程序员之间的交流，编写建表语句的时候最好加上每个字段的具体类型**

#### 3、删表
###### 格式
* drop table 表名 ;
* drop table if exists 表名 ;
###### 示例
* drop table t_student ;

#### 4、插入数据（insert）
###### 格式
* insert into 表名 (字段1, 字段2, …) values (字段1的值, 字段2的值, …) ;

###### 示例
* insert into t_student (name, age) values (‘lnj’, 10) ;

**注：数据库中的字符串内容应该用单引号 ’ 括住**

#### 5、更新数据（update）
###### 格式
* update 表名 set 字段1 = 字段1的值, 字段2 = 字段2的值, … ; 

###### 示例
* update t_student set name = ‘jack’, age = 20 ; 

**注：上面的示例会将t_student表中所有记录的name都改为jack，age都改为20**

#### 6、删除数据（delete）
###### 格式
* delete from 表名 ;

###### 示例
delete from t_student ;

**注：上面的示例会将t_student表中所有记录都删掉**

#### 7、条件语句
**如果只想更新或者删除某些固定的记录，那就必须在DML语句后加上一些条件**
###### 条件语句的常见格式
```objc
where 字段 = 某个值 ;   // 不能用两个 =
where 字段 is 某个值 ;   // is 相当于 = 
where 字段 != 某个值 ; 
where 字段 is not 某个值 ;   // is not 相当于 != 
where 字段 > 某个值 ; 
where 字段1 = 某个值 and 字段2 > 某个值 ;  // and相当于C语言中的 &&
where 字段1 = 某个值 or 字段2 = 某个值 ;  //  or 相当于C语言中的 ||
```

###### 案例
```objc
将t_student表中年龄大于10 并且 姓名不等于jack的记录，年龄都改为 5
update t_student set age = 5 where age > 10 and name != ‘jack’ ;

删除t_student表中年龄小于等于10 或者 年龄大于30的记录
delete from t_student where age <= 10 or age > 30 ;

猜猜下面语句的作用
update t_student set score = age where name = ‘jack’ ;
将t_student表中名字等于jack的记录，score字段的值 都改为 age字段的值
```

#### 8、查询
###### 格式
* select 字段1, 字段2, … from 表名 ;
* select * from 表名;   //  查询所有的字段

###### 示例
* select name, age from t_student ;
* select * from t_student ;
* select * from t_student where age > 10 ;  //  条件查询

#### 9、起别名
###### 格式(字段和表都可以起别名)
* select 字段1 别名 , 字段2 别名 , … from 表名 别名 ; 
* select 字段1 别名, 字段2 as 别名, … from 表名 as 别名 ;
* select 别名.字段1, 别名.字段2, … from 表名 别名 ;

###### 示例
* select name myname, age myage from t_student ;
* 给name起个叫做myname的别名，给age起个叫做myage的别名
* select s.name, s.age from t_student s ;
* 给t_student表起个别名叫做s，利用s来引用表中的字段

#### 10、计算记录的数量
###### 格式
* select count (字段) from 表名 ;
* select count ( * ) from 表名 ;
###### 示例
* select count (age) from t_student ;
* select count ( * ) from t_student where score >= 60;

#### 11、排序
```objc
查询出来的结果可以用order by进行排序
select * from t_student order by 字段 ;
select * from t_student order by age ;

默认是按照升序排序（由小到大），也可以变为降序（由大到小）
select * from t_student order by age desc ;  //降序
select * from t_student order by age asc ;   // 升序（默认）

也可以用多个字段进行排序
select * from t_student order by age asc, height desc ;
先按照年龄排序（升序），年龄相等就按照身高排序（降序）
```

#### 12、limit
```objc
使用limit可以精确地控制查询结果的数量，比如每次只查询10条数据
格式：
select * from 表名 limit 数值1, 数值2 ;
示例：
select * from t_student limit 4, 8 ;
可以理解为：跳过最前面4条语句，然后取8条记录

limit常用来做分页查询，比如每页固定显示5条数据，那么应该这样取数据
第1页：limit 0, 5
第2页：limit 5, 5
第3页：limit 10, 5
…
第n页：limit 5*(n-1), 5

猜猜下面语句的作用
select * from t_student limit 7 ;
相当于select * from t_student limit 0, 7 ;
表示取最前面的7条记录
```

#### 13、简单约束
```objc
建表时可以给特定的字段设置一些约束条件，常见的约束有
not null ：规定字段的值不能为null
unique ：规定字段的值必须唯一
default ：指定字段的默认值
（建议：尽量给字段设定严格的约束，以保证数据的规范性）

示例
create table t_student (id integer, name text not null unique, age integer not null default 1) ;
name字段不能为null，并且唯一
age字段不能为null，并且默认为1
```

#### 14、主键约束
如果t_student表中就name和age两个字段，而且有些记录的name和age字段的值都一样时，那么就没法区分这些数据，造成数据库的记录不唯一，这样就不方便管理数据
###### 主键的设计原则
* 主键应当是对用户没有意义的
* 永远也不要更新主键
* 主键不应包含动态变化的数据
* 主键应当由计算机自动生成

###### 主键的声明
```objc
在创表的时候用primary key声明一个主键
create table t_student (id integer primary key, name text, age integer) ;
integer类型的id作为t_student表的主键

主键字段
只要声明为primary key，就说明是一个主键字段
主键字段默认就包含了not null 和 unique 两个约束

如果想要让主键自动增长（必须是integer类型），应该增加autoincrement
create table t_student (id integer primary key autoincrement, name text, age integer) ;
```

#### 15、外键约束
```objc
利用外键约束可以用来建立表与表之间的联系
外键的一般情况是：一张表的某个字段，引用着另一张表的主键字段

新建一个外键
create table t_student (id integer primary key autoincrement, name text, age integer, class_id integer, constraint fk_t_student_class_id_t_class_id foreign key (class_id) references t_class (id)) ; 
t_student表中有一个叫做fk_t_student_class_id_t_class_id的外键
这个外键的作用是用t_student表中的class_id字段引用t_class表的id字段
```
#### 16、表连接查询
```objc
什么是表连接查询
需要联合多张表才能查到想要的数据

表连接的类型
内连接：inner join 或者 join  （显示的是左右表都有完整字段值的记录）
左外连接：left outer join （保证左表数据的完整性）

示例
查询0316iOS班的所有学生
select s.name,s.age from t_student s, t_class c where s.class_id = c.id and c.name = ‘0316iOS’;
```

## 三、SQLite编码
#### 1、创建、打开、关闭数据库
```objc
创建或打开数据库
// path是数据库文件的存放路径
sqlite3 *db = NULL;
int result = sqlite3_open([path UTF8String], &db); 

代码解析：
sqlite3_open()将根据文件路径打开数据库，如果不存在，则会创建一个新的数据库。如果result等于常量SQLITE_OK，则表示成功打开数据库
sqlite3 *db：一个打开的数据库实例
数据库文件的路径必须以C字符串(而非NSString)传入

关闭数据库：sqlite3_close(db);
```
#### 2、执行不返回数据的SQL语句
```objc
执行创表语句
char *errorMsg = NULL;  // 用来存储错误信息
char *sql = "create table if not exists t_person(id integer primary key autoincrement, name text, age integer);";
int result = sqlite3_exec(db, sql, NULL, NULL, &errorMsg);

代码解析：
sqlite3_exec()可以执行任何SQL语句，比如创表、更新、插入和删除操作。但是一般不用它执行查询语句，因为它不会返回查询到的数据
sqlite3_exec()还可以执行的语句：
开启事务：begin transaction;
回滚事务：rollback;
提交事务：commit;
```

#### 3、带占位符插入数据
```objc
char *sql = "insert into t_person(name, age) values(?, ?);";
sqlite3_stmt *stmt;
if (sqlite3_prepare_v2(db, sql, -1, &stmt, NULL) == SQLITE_OK) {
    sqlite3_bind_text(stmt, 1, "母鸡", -1, NULL);
    sqlite3_bind_int(stmt, 2, 27);
}
if (sqlite3_step(stmt) != SQLITE_DONE) {
    NSLog(@"插入数据错误");
}
sqlite3_finalize(stmt);

代码解析：
sqlite3_prepare_v2()返回值等于SQLITE_OK，说明SQL语句已经准备成功，没有语法问题

sqlite3_bind_text()：大部分绑定函数都只有3个参数
第1个参数是sqlite3_stmt *类型
第2个参数指占位符的位置，第一个占位符的位置是1，不是0
第3个参数指占位符要绑定的值
第4个参数指在第3个参数中所传递数据的长度，对于C字符串，可以传递-1代替字符串的长度
第5个参数是一个可选的函数回调，一般用于在语句执行后完成内存清理工作
sqlite_step()：执行SQL语句，返回SQLITE_DONE代表成功执行完毕
sqlite_finalize()：销毁sqlite3_stmt *对象
```

#### 4、查询数据
```objc
char *sql = "select id,name,age from t_person;";
sqlite3_stmt *stmt;
if (sqlite3_prepare_v2(db, sql, -1, &stmt, NULL) == SQLITE_OK) {
    while (sqlite3_step(stmt) == SQLITE_ROW) {
        int _id = sqlite3_column_int(stmt, 0);
        char *_name = (char *)sqlite3_column_text(stmt, 1);
        NSString *name = [NSString stringWithUTF8String:_name];
        int _age = sqlite3_column_int(stmt, 2);
        NSLog(@"id=%i, name=%@, age=%i", _id, name, _age);
    }
}
sqlite3_finalize(stmt);
代码解析
sqlite3_step()返回SQLITE_ROW代表遍历到一条新记录
sqlite3_column_*()用于获取每个字段对应的值，第2个参数是字段的索引，从0开始
```

## 四、附件
```objc
/*简单约束*/
CREATE TABLE IF NOT EXISTS t_student(id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, age INTEGER);
CREATE TABLE IF NOT EXISTS t_student(id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT NOT NULL, age INTEGER NOT NULL);
CREATE TABLE IF NOT EXISTS t_student(id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT UNIQUE, age INTEGER);
CREATE TABLE IF NOT EXISTS t_student(id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, age INTEGER DEFAULT 1);

/*分页*/
SELECT * FROM t_student ORDER BY id ASC LIMIT 30, 10;

/*排序*/
SELECT * FROM t_student WHERE score > 50 ORDER BY age DESC;
SELECT * FROM t_student WHERE score < 50 ORDER BY age ASC , score DESC;

/*计量*/
SELECT COUNT(*) FROM t_student WHERE age > 50;

/*别名*/
SELECT name as myName, age as myAge, score as myScore FROM t_student;
SELECT name myName, age myAge, score myScore FROM t_student;
SELECT s.name myName, s.age myAge, s.score myScore FROM t_student s WHERE s.age > 50;

/*查询*/
SELECT name, age, score FROM t_student;
SELECT * FROM t_student;

/*修改指定数据*/
UPDATE t_student SET name = 'MM' WHERE age = 10;
UPDATE t_student SET name = 'WW' WHERE age is 7;
UPDATE t_student SET name = 'XXOO' WHERE age < 20;
UPDATE t_student SET name = 'NNMM' WHERE age < 50 and score > 10;

/*删除数据*/
DELETE FROM t_student;

/*更新数据*/
UPDATE t_student SET name = 'LNJ';

/*插入数据*/
 INSERT INTO t_student(age, score, name) VALUES ('28', 100, 'jonathan');
 INSERT INTO t_student(name, age) VALUES ('lee', '28');
 INSERT INTO t_student(score) VALUES (100);

/*插入数据*/
INSERT INTO t_student(name, age, score) VALUES ('lee', '28', 100);

/*添加主键*/
CREATE TABLE IF NOT EXISTS t_student (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, age INTEGER, score REAL);
CREATE TABLE IF NOT EXISTS t_student (id INTEGER PRIMARY KEY, name TEXT, age INTEGER, score REAL);
CREATE TABLE IF NOT EXISTS t_student (id INTEGER, name TEXT, age INTEGER, score REAL, PRIMARY KEY(id));

/*删除表*/
DROP TABLE t_student;
DROP TABLE IF EXISTS t_student;

/*创建表*/
CREATE TABLE t_student(id INTEGER , name TEXT, age , score REAL);
CREATE TABLE IF NOT EXISTS t_student(id INTEGER , name TEXT, age , score REAL);
```
#### 1.打开数据库
```objc
int sqlite3_open(
    const char *filename,   // 数据库的文件路径
    sqlite3 **ppDb          // 数据库实例
);
```
#### 2、执行任何SQL语句
```objc
int sqlite3_exec(
    sqlite3*,                                  // 一个打开的数据库实例
    const char *sql,                           // 需要执行的SQL语句
    int (*callback)(void*,int,char**,char**),  // SQL语句执行完毕后的回调
    void *,                                    // 回调函数的第1个参数
    char **errmsg                              // 错误信息
);
```
#### 3、检查SQL语句的合法性
```objc
int sqlite3_prepare_v2(
    sqlite3 *db,            // 数据库实例
    const char *zSql,       // 需要检查的SQL语句
    int nByte,              // SQL语句的最大字节长度
    sqlite3_stmt **ppStmt,  // sqlite3_stmt实例，用来获得数据库数据
    const char **pzTail
);
```
#### 4、查询一行数据
```objc
int sqlite3_step(sqlite3_stmt*); // 如果查询到一行数据，就会返回
```
#### 5、利用stmt获得某一字段的值
```objc
double sqlite3_column_double(sqlite3_stmt*, int iCol);  // 浮点数据
int sqlite3_column_int(sqlite3_stmt*, int iCol); // 整型数据
sqlite3_int64 sqlite3_column_int64(sqlite3_stmt*, int iCol); // 长整型数据
```



