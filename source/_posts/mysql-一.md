
---
title: mysql
date: 2020-08-25 01:28:32
tags: mysql
category: 后端
cover: '/img/desk.png'
---
# 数据库能做什么
# 数据库的基本概念
- 实体
- 数据库(DB)
- 数据库管理系统(DBMS)
- SQL
- SQL规范
  - SQL语句不区分大小写，建议SQL关键字大写，表名和列表小写
  - 命令用分号结尾
  - 命令可以缩进和换行，一种类型的关键字放在一行
  - 可以写单行和多行注释 , #和--是单行注释，/***/多行注释
# 数据表
# MYSQL简介
## 特点
## 安装
## 配置
> C:\Program Files\MySQL\MySQL Server 5.5\my.ini
- port 端口号
- basedir 安装目录
- datadir 数据存放访目录
- character_set_server 字符集
- default-storage-engine 存储引擎
- sql-mode 语法模式
- max-connections 最大连接数

## MYSQL启动和停止
```js
net start MySQL
net stop MySQL
```
## 显示有哪些表
```cmd
mysql -h 127.0.0.1 -P 3306 -uroot -p123456
exit
```
## 显示当前数据库
```cmd
select database();
```
## 查询表结构
```cmd
desc user;
```

# 创建表
# 数据完整性
## 域完整性
### 列类型
- 字符串
- 日期时间
- 数值
  - 整数
  - 小数
  
### 默认值
### 非空约束
# 实体完整性
- 主键约束
  - 表中一列或者几列组合的值能用来唯一标识表中的每一行，这样的列或者列组合称为表的主键，主键表的数据不同重复
  - 如果两列或者多列组合起来唯一标识表中的每一行，则该主键又称为"组合键"
- 外键
- 唯一约束
- 标识列
  - 当表中没有合适的列作为主键时可以考虑增加标识列，标识列是一个无实际业务含义的列，仅仅用来区分每条记录。
  - 标识列的值是自动生成的，不能在该列上输入数据
- 外键约束
  - 主表没有记录，子表中不能添加相应的记录
  - 修改和删除主表记录不能让子表记录孤立，必须相应修改和删除

# 数据操作语言
```sql
-- 关键字大写
-- PRIMARY KEY AUTO_INCREMENT,
CREATE TABLE `student`
(
id INT(11) NOT NULL,
name VARCHAR(64) NOT NULL,
age INT(11) DEFAULT NULL,
city VARCHAR(64) DEFAULT '北京'
)
SELECT * FROM student
DESC student

-- 如何修改和增加表里的字段
ALTER TABLE student ADD COLUMN idcard VARCHAR(225) NULL
-- 修改列
ALTER TABLE student MODIFY idcard VARCHAR(128) NOT NULL
-- 删除列
ALTER TABLE student DROP idcard

-- 增加约束
ALTER TABLE student ADD PRIMARY KEY

-- 增加唯一约束
ALTER TABLE student ADD UNIQUE INDEX uq_student_idcard (idcard)

-- 增加默认约束
ALTER TABLE student MODIFY COLUMN city VARCHAR(64) DEFAULT '南京'

-- 主外键
ALTER TABLE score ADD CONSTRAINT fk_score_student_id FOREIGN KEY(student_id) REFERENCES student(id)

-- sql
-- 插入
INSERT INTO student(name,idcard,age,city)
VALUES('张三','123456',10,'北京')
INSERT INTO student(name,idcard,age,city)
VALUES('李四','123451',18,'广州')
SELECT * FROM student
-- 更新
-- 1 -> 可以一次更新多列，可以用逗号隔开 2.可以指定更新条件，如果有我个条件可以用and or not
UPDATE student SET age=40,city='上海' WHERE id = 1
-- 删除
DELETE FROM student WHERE id = 3
-- TRUNCATE 截断表 重置表标识列
```
