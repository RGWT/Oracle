# 实验2：用户及权限管理

## 实验目的

掌握用户管理、角色管理、权根维护与分配的能力，掌握用户之间共享对象的操作技能。

## 实验内容

Oracle有一个开发者角色resource，可以创建表、过程、触发器等对象，但是不能创建视图。本训练要求：

- 在pdborcl插接式数据中创建一个新的本地角色con_res_view，该角色包含connect和resource角色，同时也包含CREATE VIEW权限，这样任何拥有con_res_view的用户就同时拥有这三种权限。
- 创建角色之后，再创建用户new_user，给用户分配表空间，设置限额为50M，授予con_res_view角色。
- 最后测试：用新用户new_user连接数据库、创建表，插入数据，创建视图，查询表和视图的数据。

## 实验步骤

### 对Oracle12c中的HR人力资源管理系统中的表查询分析：

#### 第一步：

- 以system登录到pdborcl，创建角色wangtao4（con_res_view）和用户wangtao44（new_user），并授权和分配空间：

```sql
[student@deep02 ~]$ sqlplus system/123@pdborcl
SQL*Plus: Release 12.2.0.1.0 Production on 星期二 3月 23 19:57:11 2021
Copyright (c) 1982, 2016, Oracle.  All rights reserved.
上次成功登录时间: 星期二 3月  23 2021 19:46:31 +08:00
连接到:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
SQL> CREATE ROLE wangtao4;
角色已创建。
SQL> GRANT connect,resource,CREATE VIEW TO wangtao4;
授权成功。
SQL> CREATE USER wangtao44 IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
用户已创建。
SQL> ALTER USER wangtao44 QUOTA 50M ON users;
用户已更改。
SQL> GRANT wangtao4 TO wangtao44;
授权成功。
SQL> exit
```

> 语句“ALTER USER new_user QUOTA 50M ON users;”是指授权new_user用户访问users表空间，空间限额是50M。



#### 第二步：

- 新用户new_user连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。

```sql
[student@deep02 ~]$ sqlplus wangtao44/123@pdborcl
SQL*Plus: Release 12.2.0.1.0 Production on 星期二 3月 23 20:03:26 2021
Copyright (c) 1982, 2016, Oracle.  All rights reserved.
连接到:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
SQL> show user;
USER 为 "WANGTAO44"
SQL> CREATE TABLE mytable (id number,name varchar(50));
表已创建。
SQL> INSERT INTO mytable(id,name)VALUES(1,'wangtao41');
已创建 1 行。
SQL> INSERT INTO mytable(id,name)VALUES (2,'wangtao42');
已创建 1 行。
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
视图已创建。
SQL> SELECT * FROM myview;
NAME
--------------------------------------------------
wangtao41
wangtao42
SQL> GRANT SELECT ON myview TO hr;
授权成功。
SQL> exit
```

#### 第三步

- 用户hr连接到pdborcl，查询wangtao44授予它的视图myview

```
[student@deep02 ~]$ sqlplus system/123@pdborcl

SQL*Plus: Release 12.2.0.1.0 Production on 星期二 3月 23 20:08:10 2021

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

上次成功登录时间: 星期二 3月  23 2021 19:57:12 +08:00

连接到:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL> SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

TABLESPACE_NAME
------------------------------
FILE_NAME
--------------------------------------------------------------------------------
        MB     MAX_MB AUT
---------- ---------- ---
USERS
/home/oracle/app/oracle/oradata/orcl/pdborcl/users01.dbf
       7.5 32767.9844 YES


SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
  8   where  a.tablespace_name = b.tablespace_name;

表空间名                           大小MB     剩余MB     使用MB    使用率%
------------------------------ ---------- ---------- ---------- ----------
SYSAUX                                660     39.625    620.375         94
UNDOTBS1                              100    57.3125    42.6875      42.69
USERS                                 7.5      .4375     7.0625      94.17
SYSTEM                                270          8        262      97.04

SQL>

```



## 数据库和表空间占用分析

> 当全班同学的实验都做完之后，数据库pdborcl中包含了每个同学的角色和用户。 所有同学的用户都使用表空间users存储表的数据。 表空间中存储了很多相同名称的表mytable和视图myview，但分别属性于不同的用户，不会引起混淆。 随着用户往表中插入数据，表空间的磁盘使用量会增加。



## 查看数据库的使用情况

以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。

```
$ sqlplus system/123@pdborcl

SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
```

- autoextensible是显示表空间中的数据文件是否自动增加。
- MAX_MB是指数据文件的最大容量。

## 实验参考

- Oracle地址：202.115.82.8 用户名：system,hr,new_user ， 密码123， 数据库名称：pdborcl，端口号：1521
- SQL-DEVELOPER修改用户的操作界面。

- sqldeveloper授权对象的操作界面。