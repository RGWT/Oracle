# 实验1：SQL语句的执行计划分析与优化指导

## 实验目的

分析SQL执行计划，执行SQL语句的优化指导。理解分析SQL语句的执行计划的重要作用。

## 实验内容

- 对Oracle12c中的HR人力资源管理系统中的表进行查询与分析。
- 首先运行和分析教材中的样例：本训练任务目的是查询两个部门('IT'和'Sales')的部门总人数和平均工资，以下两个查询的结果是一样的。但效率不相同。
- 设计自己的查询语句，并作相应的分析，查询语句不能太简单。

## 实验步骤

### 对Oracle12c中的HR人力资源管理系统中的表查询分析：

#### 查询1：

```sql
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
from hr.departments d,hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT','Sales')
GROUP BY d.department_name;
```

- 运行结果如下：

![test1_first](https://raw.githubusercontent.com/RGWT/All-picture/master/20210316204635.png)


- 语句统计信息如下

![test1_1_output](https://raw.githubusercontent.com/RGWT/All-picture/master/20210316204040.png)



#### 查询2：

```sql
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
FROM hr.departments d,hr.employees e
WHERE d.department_id = e.department_id
GROUP BY d.department_name
HAVING d.department_name in ('IT','Sales');
```

- 运行结果如下

![test1_second](https://raw.githubusercontent.com/RGWT/All-picture/master/20210316204053.png)

- 语句统计信息如下

![test1_2_output](https://raw.githubusercontent.com/RGWT/All-picture/master/20210316204214.png)



​		执行上面两个比较复杂的返回相同查询结果数据集的SQL语句，通过分析SQL语句各自的执行计划，判断哪个SQL语句是最优的。最后将你认为最优的SQL语句通过sqldeveloper的优化指导工具进行优化指导，看看该工具有没有给出优化建议。



##### 查询语句1——优化建议：

![test1_1_optimization](https://raw.githubusercontent.com/RGWT/All-picture/master/20210316204815.png)



### 查询语句设计

查询shipping部门的员工编号以及对应的工作编号（已创建索引）：

```sql
set autotrace on

SELECT d.department_name,e.EMPLOYEE_id,e.job_id
from hr.departments d,hr.employees e
where d.department_id = e.department_id
and d.department_name in ('Shipping')
GROUP BY department_name,e.EMPLOYEE_id,e.job_id;
```

- 运行结果

![test1_1_me_output](https://raw.githubusercontent.com/RGWT/All-picture/master/20210316204257.png)
![](./test1_1_me_output)


## 实验参考地址

- Oracle地址：202.115.82.8 用户名：system ， 密码123， 数据库名称：pdborcl，端口号：1521
- 用户hr默认没有统计权限，运行上述命令时要报错：

```
无法收集统计信息, 请确保用户具有正确的访问权限。
统计信息功能要求向用户授予 v_$sesstat, v_$statname 和 v_$session 的选择权限。
```

怎样解决？



- 此时可以利用`GRANT`语句授予hr权限。


```SQL
GRANT SELECT ON v_$sesstat TO hr;
GRANT SELECT ON v_$statname TO hr;
GRANT SELECT ON v_$session TO hr;
```

