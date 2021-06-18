- 期末考核要求
  - 自行设计一个信息系统的数据库项目，自拟`某项目`名称。
  - 设计项目涉及的表及表空间使用方案。至少5张表和5万条数据，两个表空间。
  - 设计权限及用户分配方案。至少两类角色，两个用户。
  - 在数据库中建立一个程序包，在包中用PL/SQL语言设计一些存储过程和函数，实现比较复杂的业务逻辑，用模拟数据进行执行计划分析。
  - 设计自动备份方案或则手工备份方案。
  - 设计容灾方案。使用两台主机，通过DataGuard实现数据库整体的异地备份(可选)。

## 实验注意事项，完成时间： 2021-6-15日前上交

- 实验在自己的计算机上完成。

- 文档

  ```
  必须提交
  ```

  到你的Oracle项目中的test6目录中。文档必须上传两套，因此你的test6目录中必须至少有两个文件：

  - 一个是Markdown格式的，文件名称是test6.md。
  - 另一个是Word格式的，文件名称是test6_design.docx。参见[test6_design.docx](https://github.com/Windgwh/oracle/blob/main/test6/test6_design.docx)，文件不用打印。

- 上交后，通过这个地址应该可以打开你的源码：https://github.com/你的用户名/oracle/tree/master/test6

- 文档中所有设计和数据都必须是你自己独立完成的真实实验结果。不得抄袭，杜撰。

## 评分标准

| 评分项     | 评分标准                     | 满分 |
| ---------- | ---------------------------- | ---- |
| 文档整体   | 文档内容详实、规范，美观大方 | 10   |
| 表设计     | 表，表空间设计合理，数据合理 | 20   |
| 用户管理   | 权限及用户分配方案设计正确   | 20   |
| PL/SQL设计 | 存储过程和函数设计正确       | 30   |
| 备份方案   | 备份方案设计正确             | 20   |



## 1.创建表空间

- space_wtoo1（图1-1）

```sql
Create Tablespace space_wt001
datafile
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED,
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED
EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```
![1-1](D:\Unknown\github\Oracle\test6\pictures\1-1.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/1-1.png)

- space_wtoo2（图1-2）

```sql
Create Tablespace space_wt001
datafile
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED,
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED
EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```

![1-2](D:\Unknown\github\Oracle\test6\pictures\1-2.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/1-2.png)

- 结果图（1-3）

![1-3](D:\Unknown\github\Oracle\test6\pictures\1-3.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/1-3.png)



## 2.创建角色及用户

#### 用户默认使用表空间space_wt001

#### 创建第一个角色和用户

- 创建角色wt1将connect,resource,create view授权给wt1
- 创建用户wt_1
- 分配60M空间给wt_1并将角色qhl1授权给用户wt_1

（图2-1）

```sql
CREATE ROLE wt1;

GRANT connect,resource,CREATE VIEW TO wt1;

CREATE USER wt_1 IDENTIFIED BY 123 DEFAULT TABLESPACE space_wt001 TEMPORARY TABLESPACE temp;

ALTER USER wt_1 QUOTA 60M ON space_wt001;

GRANT wt1 TO wt_1;
```

![2-1](D:\Pt\暂存\oracle\2-1.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/2-1.png)

结果图（2-2）

![2-2](D:\Unknown\github\Oracle\test6\pictures\2-2.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/2-2.png)

#### 创建第2个角色和用户

- 创建角色wt2将connect,resource,create view授权给wt2
- 创建用户wt_2
- 分配60M空间给wt_2并将角色qhl1授权给用户wt_2

（图2-3）

```sql
CREATE ROLE wt2;

GRANT connect,resource,CREATE VIEW TO wt2;

CREATE USER wt_2 IDENTIFIED BY 123 DEFAULT TABLESPACE space_wt002 TEMPORARY TABLESPACE temp;

ALTER USER wt_2 QUOTA 60M ON space_wt002;

GRANT wt2 TO wt_2;
```

![2-3](D:\Pt\暂存\oracle\2-3.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/2-3.png)

结果图（2-4）

![2-4](D:\Unknown\github\Oracle\test6\pictures\2-4.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/2-3.png)



#### 3. 在用户wt_1下创建表

**创建管理员表**

- id为主键（图3-1）

```SQL
CREATE TABLE ADMINISTRATOR 
(
  ID NUMBER(*, 0) NOT NULL 
, PASSWORD VARCHAR2(20 BYTE) NOT NULL 
, ADMIN VARCHAR2(20 BYTE) NOT NULL 
, CONSTRAINT ADMINISTRATOR_PK PRIMARY KEY 
  (
    ID 
  )
  USING INDEX 
  (
      CREATE UNIQUE INDEX ADMINISTRATOR_PK ON ADMINISTRATOR (ID ASC) 
      LOGGING 
      TABLESPACE SPACE_WT001 
      PCTFREE 10 
      INITRANS 2 
      STORAGE 
      ( 
        BUFFER_POOL DEFAULT 
      ) 
      NOPARALLEL 
  )
  ENABLE 
) 
LOGGING 
TABLESPACE SPACE_WT001 
PCTFREE 10 
INITRANS 1 
STORAGE 
( 
  BUFFER_POOL DEFAULT 
) 
NOCOMPRESS 
NO INMEMORY 
NOPARALLEL;
```

![3-1](D:\Unknown\github\Oracle\test6\pictures\3-1.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/3-1.png)

**创建用户表**

- id为主键
- 根据注册日期按范围分区
- 分为2018和2019年两个分区，每年按季度划4个子分区（图3-2）

```sql
CREATE TABLE BOOKUSER 
(
  ID NUMBER(*, 0) NOT NULL 
, PASSWORD VARCHAR2(20 BYTE) NOT NULL 
, USERNAME VARCHAR2(50 BYTE) NOT NULL 
, PHONE VARCHAR2(20 BYTE) NOT NULL 
, ADDRESS VARCHAR2(30 BYTE) NOT NULL 
, REGISTRATIONDATE DATE NOT NULL 
, CART_ID NUMBER(*, 0) NOT NULL 
, CONSTRAINT U_PK PRIMARY KEY 
  (
    ID 
  )
  USING INDEX 
  (
      CREATE UNIQUE INDEX U_PK ON BOOKUSER (ID ASC) 
      LOGGING 
      TABLESPACE SPACE_WT001 
      PCTFREE 10 
      INITRANS 2 
      STORAGE 
      ( 
        BUFFER_POOL DEFAULT 
      ) 
      NOPARALLEL 
  )
  ENABLE 
) 
TABLESPACE SPACE_WT001 
PCTFREE 10 
INITRANS 1 
STORAGE 
( 
  BUFFER_POOL DEFAULT 
) 
NOCOMPRESS 
NOPARALLEL 
PARTITION BY RANGE (REGISTRATIONDATE) 
SUBPARTITION BY RANGE (REGISTRATIONDATE) 
(
  PARTITION DATE2018 VALUES LESS THAN (TO_DATE(' 2018-12-31 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY 
  (
    SUBPARTITION DATE2018_3 VALUES LESS THAN (TO_DATE(' 2018-03-31 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  , SUBPARTITION DATE2018_6 VALUES LESS THAN (TO_DATE(' 2018-06-30 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  , SUBPARTITION DATE2018_9 VALUES LESS THAN (TO_DATE(' 2018-09-30 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  , SUBPARTITION DATE2018_12 VALUES LESS THAN (TO_DATE(' 2018-12-31 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  )  
, PARTITION DATE2019 VALUES LESS THAN (TO_DATE(' 2019-12-31 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY 
  (
    SUBPARTITION DATE2019_3 VALUES LESS THAN (TO_DATE(' 2019-03-31 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  , SUBPARTITION DATE2019_6 VALUES LESS THAN (TO_DATE(' 2019-06-30 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  , SUBPARTITION DATE2019_9 VALUES LESS THAN (TO_DATE(' 2019-09-30 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  , SUBPARTITION DATE2019_12 VALUES LESS THAN (TO_DATE(' 2019-12-31 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
    NOCOMPRESS NO INMEMORY  
  )  
);
```

![3-2](D:\Unknown\github\Oracle\test6\pictures\3-2.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/3-2.png)

**创建商品表**（图3-3）

```sql
CREATE TABLE COMMODITY 
(
  ID NUMBER(*, 0) NOT NULL 
, PID NUMBER(*, 0) NOT NULL 
, BOOKSNAME VARCHAR2(20 BYTE) NOT NULL 
, PRICE NUMBER NOT NULL 
, DESCRIBE VARCHAR2(50 BYTE) NOT NULL 
, NUM NUMBER(*, 0) NOT NULL 
, ADMIN_ID NUMBER(*, 0) NOT NULL 
, CONSTRAINT COMMODITY_PK PRIMARY KEY 
  (
    ID 
  )
  USING INDEX 
  (
      CREATE UNIQUE INDEX COMMODITY_PK ON COMMODITY (ID ASC) 
      LOGGING 
      TABLESPACE SPACE_WT001 
      PCTFREE 10 
      INITRANS 2 
      STORAGE 
      ( 
        BUFFER_POOL DEFAULT 
      ) 
      NOPARALLEL 
  )
  ENABLE 
) 
LOGGING 
TABLESPACE SPACE_WT001 
PCTFREE 10 
INITRANS 1 
STORAGE 
( 
  BUFFER_POOL DEFAULT 
) 
NOCOMPRESS 
NO INMEMORY 
NOPARALLEL;
```

![3-3](D:\Unknown\github\Oracle\test6\pictures\3-3.jpg)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/3-3.png)

**创建购物车表**

- 用户表字段BOOKUSER_ID为购物车表的外键
- 购物车采用引用分区（图3-4）

```sql
CREATE TABLE CART 
(
  ID NUMBER(*, 0) NOT NULL 
, AMOUNT NUMBER(*, 0) NOT NULL 
, PID NUMBER(*, 0) NOT NULL 
, BOOKUSER_ID NUMBER(*, 0) NOT NULL 
, CONSTRAINT CART_PK PRIMARY KEY 
  (
    ID 
  )
  USING INDEX 
  (
      CREATE UNIQUE INDEX CART_PK ON CART (ID ASC) 
      LOGGING 
      TABLESPACE SPACE_WT001 
      PCTFREE 10 
      INITRANS 2 
      STORAGE 
      ( 
        BUFFER_POOL DEFAULT 
      ) 
      NOPARALLEL 
  )
  ENABLE 
, CONSTRAINT CART_BOOKUSER FOREIGN KEY
  (
  BOOKUSER_ID 
  )
  REFERENCES BOOKUSER
  (
  CART_ID 
  )
  ENABLE 
) 
PCTFREE 10 
PCTUSED 40 
INITRANS 1 
STORAGE 
( 
  BUFFER_POOL DEFAULT 
) 
NOCOMPRESS 
NOPARALLEL 
PARTITION BY REFERENCE (CART_BOOKUSER) 
(
  PARTITION DATE2018_3 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
, PARTITION DATE2018_6 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
, PARTITION DATE2018_9 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
, PARTITION DATE2018_12 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
, PARTITION DATE2019_3 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
, PARTITION DATE2019_6 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
, PARTITION DATE2019_9 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
, PARTITION DATE2019_12 
  LOGGING 
  TABLESPACE SPACE_WT001 
  PCTFREE 10 
  INITRANS 1 
  STORAGE 
  ( 
    BUFFER_POOL DEFAULT 
  ) 
  NOCOMPRESS NO INMEMORY  
);
```

![3-4](D:\Unknown\github\Oracle\test6\pictures\3-4.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/3-4.png)

**创建完成后表结构如下**（图3-5）

![3-5](D:\Unknown\github\Oracle\test6\pictures\3-5.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/3-5.png)

#### 4. 插入用户、商品、购物车数据

（图4-1）

```sql
declare
  id number(38,0);
  username varchar2(50);
  phone varchar2(20);
  address varchar2(30);
  REGISTRATIONDATE date;
  booksname varchar2(50);
  price number(5,2);
  num number(38,0);
  amount number(38,0);
  
begin
  for i in 1..20000
  loop
    if i mod 2 =0 then
      REGISTRATIONDATE:=to_date('2018-5-6','yyyy-mm-dd')+(i mod 60);
    else
      REGISTRATIONDATE:=to_date('2019-5-6','yyyy-mm-dd')+(i mod 60);
    end if;

    --插入用户
    id:=SEQ_ORDER_ID.nextval; --应该将SEQ_ORDER_ID.nextval保存到变量中。
    username := 'aa'|| 'aa';
    username := 'wang' || i;
    phone := '131785693' || i;
	booksname := '唐诗三百首版本号' || i;
	address :='成都'|| '四川';
	price :=(dbms_random.value() * 100);
	num :=(i mod 5);
    insert /*+append*/ into bookuser (id,password,username,phone,address,REGISTRATIONDATE,cart_id)
      values (id,username,username,phone,address,REGISTRATIONDATE,id);
	--插入货品
		
	insert into commodity(id,pid,booksname,price,describe,num,admin_id)
		values (id,id,booksname,price,'good',num,1);
	--插入购物车
	amount :=(id mod 3 ) + 1;
	insert into cart(id,amount,pid,bookuser_id)
	 	values (id,amount,id,id);

    IF I MOD 1000 =0 THEN
      commit; --每次提交会加快插入数据的速度
    END IF;
  end loop;
 
end;
```

![4-1](D:\Unknown\github\Oracle\test6\pictures\4-1.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/4-1.png)

#### 5.创建程序包、存储过程、函数执行分析计划

**创建程序包**

- 函数getcartsumprice计算每个用户的购物车商品总金额
- 存储过程adduser插入用户信息（图5-1）

```sql
create or replace PACKAGE book_package Is
   function getcartsumprice(user_id number) return number;
   procedure adduser(password varchar2,username varchar2,phone varchar2,address varchar2,registerdate VARCHAR2);
end book_package;
```

![5-1](D:\Unknown\github\Oracle\test6\pictures\5-1.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-1.png)

**存储过程、函数执行分析**

使用自定义函数getcartsumprice（）查询id号为20011的用户购物车商品总价（图5-2）

```sql
create or replace PACKAGE body book_package Is
 
       function getcartsumprice(user_id number) return number as
          begin
            declare cart_sum number;
			query_sql varchar2(200);
            begin
			query_sql:='select sum(pricesum) from view_SinglePriceSum where ID=' || user_id;
              execute immediate query_sql into cart_sum;
			  return cart_sum;
            end;
        end getcartsumprice;
                  procedure addUser(password varchar2,username varchar2,phone varchar2,address varchar2,registerdate varchar2) as
            begin
              declare maxId number;
              begin
                select max(id) into maxId from bookuser;
                insert into bookuser values(maxId+1,password,username,phone,address,to_date(registerdate,'yyyy-mm-dd'),maxId+1);
                commit;
              end;
            end adduser;
    end book_package;
```

![5-2](D:\Unknown\github\Oracle\test6\pictures\5-2.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-2.png)

结果图（图5-3）

![5-3](D:\Unknown\github\Oracle\test6\pictures\5-3.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-3.png)

##### 使用存储过程adduser插入用户数据（图5-4）

```sql
set serveroutput on
declare
begin
BOOK_PACKAGE.addUser('131','cwd','125626','hongkong','2019-05-02');
end;
```

![5-4](D:\Unknown\github\Oracle\test6\pictures\5-4.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-4.png)

结果图（图5-5）

![5-5](D:\Unknown\github\Oracle\test6\pictures\5-5.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-5.png)

结果图（图5-6）

![5-6](D:\Unknown\github\Oracle\test6\pictures\5-6.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-6.png)

**执行计划分析**（图5-7）

```sql
select * from BOOKUSER b,COMMODITY co,CART ca where b.id=ca.BOOKUSER_ID and ca.PID=co.PID and
b.REGISTRATIONDATE between to_date('2018-1-1','yyyy-mm-dd') and to_date('2018-6-1','yyyy-mm-dd');
```

![5-7](D:\Unknown\github\Oracle\test6\pictures\5-7.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-7.png)

结果图（图5-8）

![5-8](D:\Unknown\github\Oracle\test6\pictures\5-8.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-8.png)

**表空间使用状况**（图5-9）

```sql
SELECT a.tablespace_name "表空间名",
total "表空间大小",
free "表空间剩余大小",
(total - free) "表空间使用大小",
total / (1024 * 1024 * 1024) "表空间大小(G)",
free / (1024 * 1024 * 1024) "表空间剩余大小(G)",
(total - free) / (1024 * 1024 * 1024) "表空间使用大小(G)",
round((total - free) / total, 4) * 100 "使用率 %"
FROM (SELECT tablespace_name, SUM(bytes) free
FROM dba_free_space
GROUP BY tablespace_name) a,
(SELECT tablespace_name, SUM(bytes) total
FROM dba_data_files
GROUP BY tablespace_name) b
WHERE a.tablespace_name = b.tablespace_name
```

![5-9](D:\Unknown\github\Oracle\test6\pictures\5-9.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-9.png)

结果图（图5-10）

![5-10](D:\Unknown\github\Oracle\test6\pictures\5-10.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/5-10.png)

#### 6.备份恢复

- 备份./rman_level0.sh（图6-1）

![6-1](D:\Unknown\github\Oracle\test6\pictures\6-1.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/6-1.png)

- 删除数据（图6-2）

![6-2](D:\Unknown\github\Oracle\test6\pictures\6-2.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/6-2.png)

- 恢复备份（图6-3）

![6-3](D:\Unknown\github\Oracle\test6\pictures\6-3.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/6-3.png)

- 数据已恢复（图6-4）

![6-4](D:\Unknown\github\Oracle\test6\pictures\6-4.png)

![Image text](https://github.com/RGWT/Oracle/blob/main/test6/pictures/6-4.png)