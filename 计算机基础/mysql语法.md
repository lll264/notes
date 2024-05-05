如果你的mysql导入[环境变量](https://so.csdn.net/so/search?q=环境变量&spm=1001.2101.3001.7020)，可以在命令行输入：

```
mysql -u root -p
```

然后输入密码登录数据库

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
insert into student(id,name,age,sex,high) value (001,"张三",10,"男",160);

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

![image-20240505164330300](mysql语法.assets/image-20240505164330300.png)
