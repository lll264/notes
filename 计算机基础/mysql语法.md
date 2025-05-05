如果你的mysql导入[环境变量](https://so.csdn.net/so/search?q=环境变量&spm=1001.2101.3001.7020)，可以在命令行输入：

```
mysql -u root -p
```

然后输入密码登录数据库

# 增删改查

```mysql
创建数据库
create database my_database;

查询数据库
show databases;

删除数据库
drop database my_databases;

选择数据库
use my_database;

查询数据表
show tables;
```

```mysql
创建数据表
create table student(id int,name char(10),age int,sex char(5));

删除表
drop table student;

插入数据项
insert into student(id,name,age,sex,high) values (001,"张三",10,"男",160);

删除数据项
delete from student where high=140;

更新数据项
update student set age=12;
```

查询数据项

select * from student where age=12;

数据表添加列

alter table student add height int(10);

数据表删除列

alter table student drop height;

数据列改名

alter table student change column height high int(3);

数据列修改数据类型

alter table student modify column high char(10);

修改表名

alter table student rename to student_table;

# 多表查询

假设有两个表 `employees` 和 `departments`，我们想要获取所有员工及其所属的部门。

## 概述

基本多表查询，查询的结果集是两个表数据的笛卡尔积。通过条件可以进行筛选

```mysql
select * from tb_emp, tb_dept where tb_emp.dept_id = tb_dept.id ;
```

笛卡尔乘积是指在数学中，两个集合(A集合和B集合)的所有组合情况。

## 内连接

内连接: 合并具有同一列的两个以上的表的行, 结果集中不包含一个表与另一个表不匹配的行

```mysql
SELECT employees.id, employees.name, departments.department_name
FROM employees
INNER JOIN departments
ON employees.department_id = departments.id;
```

## 外连接

![在这里插入图片描述](D:\E\学习\Go\笔记\计算机基础\assets\9e6404e27a8e477d020b69cd9ad5ac6a.png)

外连接: 两个表在连接过程中除了返回满足连接条件的行以外还**保留了左（或右）表中不满足条件的行** ，这种连接称为左（或右） 外连接。**没有匹配的行时, 结果表中相应的列为空(NULL)。**

1. 如果是左外连接，则连接条件中左边的表也称为主表，右边的表称为从表。
2. 如果是右外连接，则连接条件中右边的表也称为主表，左边的表称为从表。

### 左连接

```mysql
SELECT employees.id, employees.name, departments.department_name
FROM employees
LEFT JOIN departments
ON employees.department_id = departments.id;
```

### 右连接

右连接返回右表中的所有行，即使左表中没有匹配的行。如果左表中没有匹配的行，则结果集中左表的列将包含 `NULL`。

```mysql
SELECT employees.id, employees.name, departments.department_name
FROM employees
RIGHT JOIN departments
ON employees.department_id = departments.id;
```

## 全连接

返回两个表中的所有行，MySQL 通过 UNION 实现。

```mysql
SELECT employees.id, employees.name, departments.department_name
FROM employees
LEFT JOIN departments
ON employees.department_id = departments.id
UNION
SELECT employees.id, employees.name, departments.department_name
FROM employees
RIGHT JOIN departments
ON employees.department_id = departments.id;
```

# 事务

# 索引
