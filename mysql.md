# 基础篇

## 通用语法及分类

- DDL: 数据定义语言，用来定义数据库对象（数据库、表、字段）
- DML: 数据操作语言，用来对数据库表中的数据进行增删改
- DQL: 数据查询语言，用来查询数据库中表的记录
- DCL: 数据控制语言，用来创建数据库用户、控制数据库的控制权限

### DDL（数据定义语言）

数据定义语言

#### 数据库操作

查询所有数据库：
`SHOW DATABASES;`
查询当前数据库：
`SELECT DATABASE();`
创建数据库：
`CREATE DATABASE [ IF NOT EXISTS ] 数据库名 [ DEFAULT CHARSET 字符集] [COLLATE 排序规则 ];`
删除数据库：
`DROP DATABASE [ IF EXISTS ] 数据库名;`
使用数据库：
`USE 数据库名;`

##### 注意事项

- UTF8字符集长度为3字节，有些符号占4字节，所以推荐用utf8mb4字符集

#### 表操作

查询当前数据库所有表：
`SHOW TABLES;`
查询表结构：
`DESC 表名;`
查询指定表的建表语句：
`SHOW CREATE TABLE 表名;`

创建表：
```mysql
CREATE TABLE 表名(
	字段1 字段1类型 [COMMENT 字段1注释],
	字段2 字段2类型 [COMMENT 字段2注释],
	字段3 字段3类型 [COMMENT 字段3注释],
	...
	字段n 字段n类型 [COMMENT 字段n注释]
)[ COMMENT 表注释 ];
```
**最后一个字段后面没有逗号**

添加字段：
`ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];`
例：`ALTER TABLE emp ADD nickname varchar(20) COMMENT '昵称';`

修改数据类型：
`ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);`
修改字段名和字段类型：
`ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];`
例：将emp表的nickname字段修改为username，类型为varchar(30)
`ALTER TABLE emp CHANGE nickname username varchar(30) COMMENT '昵称';`

删除字段：
`ALTER TABLE 表名 DROP 字段名;`

修改表名：
`ALTER TABLE 表名 RENAME TO 新表名`

删除表：
`DROP TABLE [IF EXISTS] 表名;`
删除表，并重新创建该表：
`TRUNCATE TABLE 表名;`

### DML（数据操作语言）

#### 添加数据

指定字段：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);`
全部字段：
`INSERT INTO 表名 VALUES (值1, 值2, ...);`

批量添加数据：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`
`INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`

##### 注意事项

- 字符串和日期类型数据应该包含在引号中
- 插入的数据大小应该在字段的规定范围内

#### 更新和删除数据

修改数据：
`UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, ... [ WHERE 条件 ];`
例：
`UPDATE emp SET name = 'Jack' WHERE id = 1;`

删除数据：
`DELETE FROM 表名 [ WHERE 条件 ];`

### DQL（数据查询语言）

语法：
```mysql
SELECT
	字段列表
FROM
	表名字段
WHERE
	条件列表
GROUP BY
	分组字段列表
HAVING
	分组后的条件列表
ORDER BY
	排序字段列表
LIMIT
	分页参数
```

#### 基础查询

查询多个字段：
`SELECT 字段1, 字段2, 字段3, ... FROM 表名;`
`SELECT * FROM 表名;`

设置别名：
`SELECT 字段1 [ AS 别名1 ], 字段2 [ AS 别名2 ], 字段3 [ AS 别名3 ], ... FROM 表名;`
`SELECT 字段1 [ 别名1 ], 字段2 [ 别名2 ], 字段3 [ 别名3 ], ... FROM 表名;`

去除重复记录：
`SELECT DISTINCT 字段列表 FROM 表名;`

转义：
`SELECT * FROM 表名 WHERE name LIKE '/_张三' ESCAPE '/'`
/ 之后的\_不作为通配符

#### 条件查询

语法：
`SELECT 字段列表 FROM 表名 WHERE 条件列表;`

条件：

| 比较运算符          | 功能                                        |
| ------------------- | ------------------------------------------- |
| >                   | 大于                                        |
| >=                  | 大于等于                                    |
| <                   | 小于                                        |
| <=                  | 小于等于                                    |
| =                   | 等于                                        |
| <> 或 !=            | 不等于                                      |
| BETWEEN ... AND ... | 在某个范围内（含最小、最大值）              |
| IN(...)             | 在in之后的列表中的值，多选一                |
| LIKE 占位符         | 模糊匹配（\_匹配单个字符，%匹配任意个字符） |
| IS NULL             | 是NULL                                      |

| 逻辑运算符         | 功能                         |
| ------------------ | ---------------------------- |
| AND 或 &&          | 并且（多个条件同时成立）     |
| OR 或 &#124;&#124; | 或者（多个条件任意一个成立） |
| NOT 或 !           | 非，不是                     |

例子：
```mysql
-- 年龄等于30
select * from employee where age = 30;
-- 年龄小于30
select * from employee where age < 30;
-- 小于等于
select * from employee where age <= 30;
-- 没有身份证
select * from employee where idcard is null or idcard = '';
-- 有身份证
select * from employee where idcard;
select * from employee where idcard is not null;
-- 不等于
select * from employee where age != 30;
-- 年龄在20到30之间
select * from employee where age between 20 and 30;
select * from employee where age >= 20 and age <= 30;
-- 下面语句不报错，但查不到任何信息
select * from employee where age between 30 and 20;
-- 性别为女且年龄小于30
select * from employee where age < 30 and gender = '女';
-- 年龄等于25或30或35
select * from employee where age = 25 or age = 30 or age = 35;
select * from employee where age in (25, 30, 35);
-- 姓名为两个字
select * from employee where name like '__';
-- 身份证最后为X
select * from employee where idcard like '%X';
```

#### 聚合查询（聚合函数）

常见聚合函数：

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

语法：
`SELECT 聚合函数(字段列表) FROM 表名;`
例：
`SELECT count(id) from employee where workaddress = "广东省";`

#### 分组查询

语法：
`SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组后的过滤条件 ];`

where 和 having 的区别：

- 执行时机不同：where是分组之前进行过滤，不满足where条件不参与分组；having是分组后对结果进行过滤。
- 判断条件不同：where不能对聚合函数进行判断，而having可以。

例子：

```mysql
-- 根据性别分组，统计男性和女性数量（只显示分组数量，不显示哪个是男哪个是女）
select count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性数量
select gender, count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性的平均年龄
select gender, avg(age) from employee group by gender;
-- 年龄小于45，并根据工作地址分组
select workaddress, count(*) from employee where age < 45 group by workaddress;
-- 年龄小于45，并根据工作地址分组，获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from employee where age < 45 group by workaddress having address_count >= 3;
```

##### 注意事项

- 执行顺序：where > 聚合函数 > having
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

#### 排序查询

语法：
`SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2;`

排序方式：

- ASC: 升序（默认）
- DESC: 降序

例子：

```mysql
-- 根据年龄升序排序
SELECT * FROM employee ORDER BY age ASC;
SELECT * FROM employee ORDER BY age;
-- 两字段排序，根据年龄升序排序，入职时间降序排序
SELECT * FROM employee ORDER BY age ASC, entrydate DESC;
```

##### 注意事项

如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序

#### 分页查询

语法：
`SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;`

例子：

```mysql
-- 查询第一页数据，展示10条
SELECT * FROM employee LIMIT 0, 10;
-- 查询第二页
SELECT * FROM employee LIMIT 10, 10;
```

##### 注意事项

- 起始索引从0开始，起始索引 = （查询页码 - 1） * 每页显示记录数
- 分页查询是数据库的方言，不同数据库有不同实现，MySQL是LIMIT
- 如果查询的是第一页数据，起始索引可以省略，直接简写 LIMIT 10

#### DQL执行顺序
 
FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY -> LIMIT

### DCL

#### 管理用户

查询用户：

```mysql
USER mysql;
SELECT * FROM user;
```

创建用户:
`CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`

修改用户密码：
`ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';`

删除用户：
`DROP USER '用户名'@'主机名';`

例子：

```mysql
-- 创建用户test，只能在当前主机localhost访问
create user 'test'@'localhost' identified by '123456';
-- 创建用户test，能在任意主机访问
create user 'test'@'%' identified by '123456';
create user 'test' identified by '123456';
-- 修改密码
alter user 'test'@'localhost' identified with mysql_native_password by '1234';
-- 删除用户
drop user 'test'@'localhost';
```

##### 注意事项

- 主机名可以使用 % 通配

#### 权限控制

常用权限：

| 权限                | 说明               |
| ------------------- | ------------------ |
| ALL, ALL PRIVILEGES | 所有权限           |
| SELECT              | 查询数据           |
| INSERT              | 插入数据           |
| UPDATE              | 修改数据           |
| DELETE              | 删除数据           |
| ALTER               | 修改表             |
| DROP                | 删除数据库/表/视图 |
| CREATE              | 创建数据库/表      |

更多权限请看[权限一览表](#权限一览表 "权限一览表")

查询权限：
`SHOW GRANTS FOR '用户名'@'主机名';`

授予权限：
`GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';`

撤销权限：
`REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';`

##### 注意事项

- 多个权限用逗号分隔
- 授权时，数据库名和表名可以用 * 进行通配，代表所有

## 函数

- 字符串函数
- 数值函数
- 日期函数
- 流程函数

### 字符串函数

常用函数：

| 函数                       | 功能                                                      |
| -------------------------- | --------------------------------------------------------- |
| CONCAT(s1, s2, ..., sn)    | 字符串拼接，将s1, s2, ..., sn拼接成一个字符串             |
| LOWER(str)                 | 将字符串全部转为小写                                      |
| UPPER(str)                 | 将字符串全部转为大写                                      |
| LPAD(str, n, pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度 |
| RPAD(str, n, pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度 |
| TRIM(str)                  | 去掉字符串头部和尾部的空格                                |
| SUBSTRING(str, start, len) | 返回从字符串str从start位置起的len个长度的字符串           |

使用示例：

```mysql
-- 拼接
SELECT CONCAT('Hello', 'World');
-- 小写
SELECT LOWER('Hello');
-- 大写
SELECT UPPER('Hello');
-- 左填充
SELECT LPAD('01', 5, '-');
-- 右填充
SELECT RPAD('01', 5, '-');
-- 去除空格
SELECT TRIM(' Hello World ');
-- 切片（起始索引为1）
SELECT SUBSTRING('Hello World', 1, 5);
```

### 数值函数

常见函数：

| 函数        | 功能                             |
| ----------- | -------------------------------- |
| CEIL(x)     | 向上取整                         |
| FLOOR(x)    | 向下取整                         |
| MOD(x, y)   | 返回x/y的模                      |
| RAND()      | 返回0~1内的随机数                |
| ROUND(x, y) | 求参数x的四舍五入值，保留y位小数 |

### 日期函数

常用函数：

| 函数                               | 功能                                              |
| ---------------------------------- | ------------------------------------------------- |
| CURDATE()                          | 返回当前日期                                      |
| CURTIME()                          | 返回当前时间                                      |
| NOW()                              | 返回当前日期和时间                                |
| YEAR(date)                         | 获取指定date的年份                                |
| MONTH(date)                        | 获取指定date的月份                                |
| DAY(date)                          | 获取指定date的日期                                |
| DATE_ADD(date, INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1, date2)             | 返回起始时间date1和结束时间date2之间的天数        |

例子：

```mysql
-- DATE_ADD
SELECT DATE_ADD(NOW(), INTERVAL 70 YEAR);
```

### 流程函数

常用函数：

| 函数                                                               | 功能                                                      |
| ------------------------------------------------------------------ | --------------------------------------------------------- |
| IF(value, t, f)                                                    | 如果value为true，则返回t，否则返回f                       |
| IFNULL(value1, value2)                                             | 如果value1不为空，返回value1，否则返回value2              |
| CASE WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END          | 如果val1为true，返回res1，... 否则返回default默认值       |
| CASE [ expr ] WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END | 如果expr的值等于val1，返回res1，... 否则返回default默认值 |

例子：

```mysql
select
	name,
	(case when age > 30 then '中年' else '青年' end)
from employee;
select
	name,
	(case workaddress when '北京市' then '一线城市' when '上海市' then '一线城市' else '二线城市' end) as '工作地址'
from employee;
```

## 约束

分类：

| 约束                    | 描述                                                     | 关键字      |
| ----------------------- | -------------------------------------------------------- | ----------- |
| 非空约束                | 限制该字段的数据不能为null                               | NOT NULL    |
| 唯一约束                | 保证该字段的所有数据都是唯一、不重复的                   | UNIQUE      |
| 主键约束                | 主键是一行数据的唯一标识，要求非空且唯一                 | PRIMARY KEY |
| 默认约束                | 保存数据时，如果未指定该字段的值，则采用默认值           | DEFAULT     |
| 检查约束（8.0.1版本后） | 保证字段值满足某一个条件                                 | CHECK       |
| 外键约束                | 用来让两张图的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

约束是作用于表中字段上的，可以再创建表/修改表的时候添加约束。

### 常用约束

| 约束条件 | 关键字         |
| -------- | -------------- |
| 主键     | PRIMARY KEY    |
| 自动增长 | AUTO_INCREMENT |
| 不为空   | NOT NULL       |
| 唯一     | UNIQUE         |
| 逻辑条件 | CHECK          |
| 默认值   | DEFAULT        |

例子：

```mysql
create table user(
	id int primary key auto_increment,
	name varchar(10) not null unique,
	age int check(age > 0 and age < 120),
	status char(1) default '1',
	gender char(1)
);
```

### 外键约束

添加外键：

```mysql
CREATE TABLE 表名(
	字段名 字段类型,
	...
	[CONSTRAINT] [外键名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)
);
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名);

-- 例子
alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id);
```

删除外键：
`ALTER TABLE 表名 DROP FOREIGN KEY 外键名;`

#### 删除/更新行为

| 行为        | 说明                                                                                                                  |
| ----------- | --------------------------------------------------------------------------------------------------------------------- |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与RESTRICT一致）                |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与NO ACTION一致）               |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则也删除/更新外键在子表中的记录                    |
| SET NULL    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（要求该外键允许为null） |
| SET DEFAULT | 父表有变更时，子表将外键设为一个默认值（Innodb不支持）                                                                |

更改删除/更新行为：
`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名(主表字段名) ON UPDATE 行为 ON DELETE 行为;`

## 多表查询

### 多表关系

- 一对多（多对一）
- 多对多
- 一对一

#### 一对多

案例：部门与员工
关系：一个部门对应多个员工，一个员工对应一个部门
实现：在多的一方建立外键，指向一的一方的主键

#### 多对多

案例：学生与课程
关系：一个学生可以选多门课程，一门课程也可以供多个学生选修
实现：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

#### 一对一

案例：用户与用户详情
关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
实现：在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的（UNIQUE）

### 查询

合并查询（笛卡尔积，会展示所有组合结果）：
`select * from employee, dept;`

> 笛卡尔积：两个集合A集合和B集合的所有组合情况（在多表查询时，需要消除无效的笛卡尔积）

消除无效笛卡尔积：
`select * from employee, dept where employee.dept = dept.id;`

### 内连接查询

内连接查询的是两张表交集的部分

隐式内连接：
`SELECT 字段列表 FROM 表1, 表2 WHERE 条件 ...;`

显式内连接：
`SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ...;`

显式性能比隐式高

例子：

```mysql
-- 查询员工姓名，及关联的部门的名称
-- 隐式
select e.name, d.name from emp as e, dept as d where e.dept_id = d.id;
-- 显式
select e.name, d.name from emp e inner join dept d on e.dept_id=d.id;
```

### 外连接查询

左外连接：
查询左表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ...;`
相当于查询表1的所有数据，包含表1和表2交集部分数据

右外连接：
查询右表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ...;`

例子：

```mysql
-- 左
select e.*, d.name from employee as e left outer join dept as d on e.dept = d.id;
select d.name, e.* from dept d left outer join emp e on e.dept = d.id;  -- 这条语句与下面的语句效果一样
-- 右
select d.name, e.* from employee as e right outer join dept as d on e.dept = d.id;
```

左连接可以查询到没有dept的employee，右连接可以查询到没有employee的dept

### 自连接查询

当前表与自身的连接查询，自连接必须使用表别名

语法：
`SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ...;`

自连接查询，可以是内连接查询，也可以是外连接查询

例子：

```mysql
-- 查询员工及其所属领导的名字
select a.name'员工', b.name'领导' from emp a, emp b where a.managerid = b.id;
-- 没有领导的也查询出来
select a.name'员工', b.name'领导' from emp a left join emp b on a.managerid=b.id;
```

### 联合查询 union, union all

把多次查询的结果合并，形成一个新的查询集

语法：

```mysql
SELECT 字段列表 FROM 表A ...
UNION [ALL]
SELECT 字段列表 FROM 表B ...
```
案例：
```mysql
--查询表中小于salary10000和表中age大于50的字段
select * from emp where salary<10000
union all
select * from emp where age>50
--去掉重复显示的字段
select * from emp where salary<10000
union
select * from emp where age>50
```

#### 注意事项

- UNION ALL 会有重复结果，UNION 不会
- 联合查询比使用or效率高，不会使索引失效
- 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致

### 子查询

SQL语句中嵌套SELECT语句，称谓嵌套查询，又称子查询。
`SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2);`
**子查询外部的语句可以是 INSERT / UPDATE / DELETE / SELECT 的任何一个**

根据子查询结果可以分为：

- 标量子查询（子查询结果为单个值）
- 列子查询（子查询结果为一列）
- 行子查询（子查询结果为一行）
- 表子查询（子查询结果为多行多列）

根据子查询位置可分为：

- WHERE 之后
- FROM 之后
- SELECT 之后

#### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等）。
常用操作符：- < > > >= < <=

例子：

```mysql
-- 查询销售部所有员工
select id from dept where name = '销售部';
-- 根据销售部部门ID，查询员工信息
select * from employee where dept = 4;
-- 合并（子查询）
select * from employee where dept = (select id from dept where name = '销售部');

-- 查询xxx入职之后的员工信息
select * from employee where entrydate > (select entrydate from employee where name = 'xxx');
```

#### 列子查询

返回的结果是一列（可以是多行）。

常用操作符：

| 操作符 | 描述                                   |
| ------ | -------------------------------------- |
| IN     | 在指定的集合范围内，多选一             |
| NOT IN | 不在指定的集合范围内                   |
| ANY    | 子查询返回列表中，有任意一个满足即可   |
| SOME   | 与ANY等同，使用SOME的地方都可以使用ANY |
| ALL    | 子查询返回列表的所有值都必须满足       |

例子：

```mysql
-- 查询销售部和市场部的所有员工信息
select * from emp where dept in (select id from dept where name = '销售部' or name = '市场部');
-- 查询比财务部所有人工资都高的员工信息
select * from emp where salary > all(select salary from emp where dept_id = (select id from dept where name = '财务部'));
-- 查询比研发部任意一人工资高的员工信息
select * from emp where salary > any (select salary from emp where dept_id = (select id from dept where name = '研发部'));
```

#### 行子查询

返回的结果是一行（可以是多列）。
常用操作符：=, <, >, IN, NOT IN

例子：

```mysql
-- 查询与xxx的薪资及直属领导相同的员工信息
select * from emp where (salary, managerid) = (12500, 1);
select * from emp where (salary, managerid) = (select salary, managerid from emp where name = 'xxx');
```

#### 表子查询

返回的结果是多行多列
常用操作符：IN

例子：

```mysql
-- 查询与xxx1，xxx2的职位和薪资相同的员工
select * from emp where (job, salary) in (select job, salary from emp where name = 'xxx1' or name = 'xxx2');
-- 查询入职日期是2006-01-01之后的员工，及其部门信息
select e.*, d.* from (select * from emp where entrydate > '2006-01-01') as e left join dept as d on e.dept_id = d.id;
```

## 事务

事务是一组操作的集合，事务会把所有操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

基本操作：

```mysql
-- 1. 查询张三账户余额
select * from account where name = '张三';
-- 2. 将张三账户余额-1000
update account set money = money - 1000 where name = '张三';
-- 此语句出错后张三钱减少但是李四钱没有增加
模拟sql语句错误
-- 3. 将李四账户余额+1000
update account set money = money + 1000 where name = '李四';

-- 查看事务提交方式
SELECT @@AUTOCOMMIT;
-- 设置事务提交方式，1为自动提交，0为手动提交，该设置只对当前会话有效
SET @@AUTOCOMMIT = 0;
-- 提交事务
COMMIT;
-- 回滚事务
ROLLBACK;

-- 设置手动提交后上面代码改为：
select * from account where name = '张三';
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';
commit;
```

操作方式二：

开启事务：
`START TRANSACTION 或 BEGIN TRANSACTION;`
提交事务：
`COMMIT;`
回滚事务：
`ROLLBACK;`

操作实例：

```mysql
start transaction;
select * from account where name = '张三';
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';
commit;
```

### 四大特性ACID

- 原子性(Atomicity)：事务是不可分割的最小操作但愿，要么全部成功，要么全部失败
- 一致性(Consistency)：事务完成时，必须使所有数据都保持一致状态
- 隔离性(Isolation)：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性(Durability)：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的

### 并发事务

| 问题       | 描述                                                                                   |
| ---------- | -------------------------------------------------------------------------------------- |
| 脏读       | 一个事务读到另一个事务还没提交的数据                                                   |
| 不可重复读 | 一个事务先后读取同一条记录，但两次读取的数据不同                                       |
| 幻读       | 一个事务按照条件查询数据时，没有对应的数据行，但是再插入数据时，又发现这行数据已经存在 |

> 这三个问题的详细演示：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=55cd 

并发事务隔离级别：

| 隔离级别              | 脏读 | 不可重复读 | 幻读 |
| --------------------- | ---- | ---------- | ---- |
| Read uncommitted      | √    | √          | √    |
| Read committed        | ×    | √          | √    |
| Repeatable Read(默认) | ×    | ×          | √    |
| Serializable          | ×    | ×          | ×    |

- √表示在当前隔离级别下该问题会出现
- Serializable 性能最低；Read uncommitted 性能最高，数据安全性最差

查看事务隔离级别：
`SELECT @@TRANSACTION_ISOLATION;`
设置事务隔离级别：
`SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE };`
SESSION 是会话级别，表示只针对当前会话有效，GLOBAL 表示对所有会话有效

# 进阶篇

## 存储引擎

MySQL体系结构：

![结构图](https://dhc.pythonanywhere.com/media/editor/MySQL体系结构_20220315034329549927.png "结构图")
![层级描述](https://dhc.pythonanywhere.com/media/editor/MySQL体系结构层级含义_20220315034359342837.png "层级描述")

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是基于表而不是基于库的，所以存储引擎也可以被称为表引擎。
默认存储引擎是InnoDB。

相关操作：

```mysql
-- 查询建表语句
show create table account;
-- 建表时指定存储引擎
CREATE TABLE 表名(
	...
) ENGINE=INNODB;
-- 查看当前数据库支持的存储引擎
show engines;
```

### InnoDB

InnoDB 是一种兼顾高可靠性和高性能的通用存储引擎，在 MySQL 5.5 之后，InnoDB 是默认的 MySQL 引擎。

特点：

- DML 操作遵循 ACID 模型，支持**事务**
- **行级锁**，提高并发访问性能
- 支持**外键**约束，保证数据的完整性和正确性
文件：

- xxx.ibd: xxx代表表名，InnoDB 引擎的每张表都会对应这样一个表空间文件，存储该表的表结构（frm、sdi）、数据和索引。

参数：innodb_file_per_table，决定多张表共享一个表空间还是每张表对应一个表空间

知识点：

查看 Mysql 变量：
`show variables like 'innodb_file_per_table';`

从idb文件提取表结构数据：
（在cmd运行）
`ibd2sdi xxx.ibd`

InnoDB 逻辑存储结构：
![InnoDB逻辑存储结构](./picture/%E9%80%BB%E8%BE%91%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84_20220316030616590001.png "InnoDB逻辑存储结构")

### MyISAM

MyISAM 是 MySQL 早期的默认存储引擎。

特点：

- 不支持事务，不支持外键
- 支持表锁，不支持行锁
- 访问速度快

文件：

- xxx.sdi: 存储表结构信息
- xxx.MYD: 存储数据
- xxx.MYI: 存储索引

### Memory

Memory 引擎的表数据是存储在内存中的，受硬件问题、断电问题的影响，只能将这些表作为临时表或缓存使用。

特点：

- 存放在内存中，速度快
- hash索引（默认）

文件：

- xxx.sdi: 存储表结构信息

### 存储引擎特点

| 特点         | InnoDB              | MyISAM | Memory |
| ------------ | ------------------- | ------ | ------ |
| 存储限制     | 64TB                | 有     | 有     |
| 事务安全     | 支持                | -      | -      |
| 锁机制       | 行锁                | 表锁   | 表锁   |
| B+tree索引   | 支持                | 支持   | 支持   |
| Hash索引     | -                   | -      | 支持   |
| 全文索引     | 支持（5.6版本之后） | 支持   | -      |
| 空间使用     | 高                  | 低     | N/A    |
| 内存使用     | 高                  | 低     | 中等   |
| 批量插入速度 | 低                  | 高     | 高     |
| 支持外键     | 支持                | -      | -      |

### 存储引擎的选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

- InnoDB: 如果应用对事物的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，则 InnoDB 是比较合适的选择
- MyISAM: 如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不高，那这个存储引擎是非常合适的。
- Memory: 将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。Memory 的缺陷是对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性

电商中的足迹和评论适合使用 MyISAM 引擎，缓存适合使用 Memory 引擎。

## 索引

索引是帮助 MySQL **高效获取数据**的**数据结构（有序）**。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查询算法，这种数据结构就是索引。

优缺点：

优点：

- 提高数据检索效率，降低数据库的IO成本
- 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗

缺点：

- 索引列也是要占用空间的
- 索引大大提高了查询效率，但降低了更新的速度，比如 INSERT、UPDATE、DELETE

### 索引结构

| 索引结构            | 描述                                                                             |
| ------------------- | -------------------------------------------------------------------------------- |
| B+Tree              | 最常见的索引类型，大部分引擎都支持B+树索引                                       |
| Hash                | 底层数据结构是用哈希表实现，只有精确匹配索引列的查询才有效，不支持范围查询       |
| R-Tree(空间索引)    | 空间索引是 MyISAM 引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |
| Full-Text(全文索引) | 是一种通过建立倒排索引，快速匹配文档的方式，类似于 Lucene, Solr, ES              |

| 索引       | InnoDB        | MyISAM | Memory |
| ---------- | ------------- | ------ | ------ |
| B+Tree索引 | 支持          | 支持   | 支持   |
| Hash索引   | 不支持        | 不支持 | 支持   |
| R-Tree索引 | 不支持        | 支持   | 不支持 |
| Full-text  | 5.6版本后支持 | 支持   | 不支持 |

#### B-Tree

![二叉树](./picture/%E4%BA%8C%E5%8F%89%E6%A0%91_20220316153214227108.png "二叉树")

二叉树的缺点可以用红黑树来解决：
![红黑树](./picture/%E7%BA%A2%E9%BB%91%E6%A0%91_20220316163142686602.png "红黑树")
**红黑树**也存在大数据量情况下，**层级较深**，**检索速度慢**的问题。

为了解决上述问题，可以使用 B-Tree 结构。
B-Tree (多路平衡查找树) 以一棵最大度数（max-degree，指一个节点的子节点个数）为5（5阶）的 b-tree 为例（每个节点最多存储4个key，5个指针）

![B-Tree结构](./picture/B-Tree%E7%BB%93%E6%9E%84_20220316163813441163.png "B-Tree结构")

> B-Tree 的数据插入过程动画参照：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=68
演示地址：https://www.cs.usfca.edu/~galles/visualization/BTree.html

#### B+Tree

结构图：

![B+Tree结构图](./picture/B%2BTree%E7%BB%93%E6%9E%84%E5%9B%BE_20220316170700591277.png "B+Tree结构图")

> 演示地址：https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html

与 B-Tree 的区别：

- 所有的数据都会出现在叶子节点
- 叶子节点形成一个单向链表

MySQL 索引数据结构对经典的 B+Tree 进行了优化。在原 B+Tree 的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的 B+Tree，提高区间访问的性能。

![MySQL B+Tree 结构图](./picture/%E7%BB%93%E6%9E%84%E5%9B%BE_20220316171730865611.png "MySQL B+Tree 结构图")

#### Hash

哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中。
如果两个（或多个）键值，映射到一个相同的槽位上，他们就产生了hash冲突（也称为hash碰撞），可以通过链表来解决。

![Hash索引原理图](./picture/Hash%E7%B4%A2%E5%BC%95%E5%8E%9F%E7%90%86%E5%9B%BE_20220317143226150679.png "Hash索引原理图")

特点：

- Hash索引只能用于对等比较（=、in），不支持范围查询（betwwn、>、<、...）
- 无法利用索引完成排序操作
- 查询效率高，通常只需要一次检索就可以了，效率通常要高于 B+Tree 索引

存储引擎支持：

- Memory
- InnoDB: 具有自适应hash功能，hash索引是存储引擎根据 B+Tree 索引在指定条件下自动构建的

#### 面试题

1. 为什么 InnoDB 存储引擎选择使用 B+Tree 索引结构？

- 相对于二叉树，层级更少，搜索效率高
- 对于 B-Tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针也跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低
- 相对于 Hash 索引，B+Tree 支持范围匹配及排序操作

### 索引分类

| 分类     | 含义                                                 | 特点                     | 关键字   |
| -------- | ---------------------------------------------------- | ------------------------ | -------- |
| 主键索引 | 针对于表中主键创建的索引                             | 默认自动创建，只能有一个 | PRIMARY  |
| 唯一索引 | 避免同一个表中某数据列中的值重复                     | 可以有多个               | UNIQUE   |
| 常规索引 | 快速定位特定数据                                     | 可以有多个               |          |
| 全文索引 | 全文索引查找的是文本中的关键词，而不是比较索引中的值 | 可以有多个               | FULLTEXT |

在 InnoDB 存储引擎中，根据索引的存储形式，又可以分为以下两种：

| 分类                      | 含义                                                       | 特点                 |
| ------------------------- | ---------------------------------------------------------- | -------------------- |
| 聚集索引(Clustered Index) | 将数据存储与索引放一块，索引结构的叶子节点保存了行数据     | 必须有，而且只有一个 |
| 二级索引(Secondary Index) | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键 | 可以存在多个         |

演示图：

![大致原理](./picture/%E5%8E%9F%E7%90%86%E5%9B%BE_20220318194454880073.png "大致原理")
![演示图](./picture/%E6%BC%94%E7%A4%BA%E5%9B%BE_20220319215403721066.png "演示图")

聚集索引选取规则：

- 如果存在主键，主键索引就是聚集索引
- 如果不存在主键，将使用第一个唯一(UNIQUE)索引作为聚集索引
- 如果表没有主键或没有合适的唯一索引，则 InnoDB 会自动生成一个 rowid 作为隐藏的聚集索引

#### 思考题

1\. 以下 SQL 语句，哪个执行效率高？为什么？

```mysql
select * from user where id = 10;
select * from user where name = 'Arm';
-- 备注：id为主键，name字段创建的有索引
```

答：第一条语句，因为第二条需要回表查询，相当于两个步骤。

2\. InnoDB 主键索引的 B+Tree 高度为多少？

答：假设一行数据大小为1k，一页中可以存储16行这样的数据。InnoDB 的指针占用6个字节的空间，主键假设为bigint，占用字节数为8.
可得公式：`n * 8 + (n + 1) * 6 = 16 * 1024`，其中 8 表示 bigint 占用的字节数，n 表示当前节点存储的key的数量，(n + 1) 表示指针数量（比key多一个）。算出n约为1170。

如果树的高度为2，那么他能存储的数据量大概为：`1171 * 16 = 18736`；
如果树的高度为3，那么他能存储的数据量大概为：`1171 * 1171 * 16 = 21939856`。

另外，如果有成千上万的数据，那么就要考虑分表，涉及运维篇知识。

### 语法

创建索引：
`CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name (index_col_name, ...);`

- 如果不加 CREATE 后面不加索引类型参数，则创建的是常规索引
- 一个索引只关联一个字段称之为单列索引
- 一个索引关联多个字段称之为关联索引

查看索引：
`SHOW INDEX FROM table_name;`

删除索引：
`DROP INDEX index_name ON table_name;`

案例：

```mysql
-- name字段为姓名字段，该字段的值可能会重复，为该字段创建索引
create index idx_user_name on tb_user(name);
-- phone手机号字段的值非空，且唯一，为该字段创建唯一索引
create unique index idx_user_phone on tb_user (phone);
-- 为profession, age, status创建联合索引
create index idx_user_pro_age_stat on tb_user(profession, age, status);
-- 为email建立合适的索引来提升查询效率
create index idx_user_email on tb_user(email);

-- 删除索引
drop index idx_user_email on tb_user;
```

## 性能分析

### 查看执行频次

查看当前数据库的 INSERT, UPDATE, DELETE, SELECT 访问频次：
`SHOW GLOBAL STATUS LIKE 'Com_______';` 或者 `SHOW SESSION STATUS LIKE 'Com_______';`
例：`show global status like 'Com_______'`

### 慢查询日志

慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有SQL语句的日志。
MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：
	# 开启慢查询日志开关
	slow_query_log=1
	# 设置慢查询日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
	long_query_time=2
更改后记得重启MySQL服务，日志文件位置：/var/lib/mysql/localhost-slow.log
`systemctl restart msqld`
查看慢查询日志开关状态：
`show variables like 'slow_query_log';`

### profile

show profile 能在做SQL优化时帮我们了解时间都耗费在哪里。通过 have_profiling 参数，能看到当前 MySQL 是否支持 profile 操作：
`SELECT @@have_profiling;`
查看是否开启profiling开关：
`SELECT @@profiling`
profiling 默认关闭，可以通过set语句在session/global级别开启 profiling：
`SET profiling = 1;`
查看所有语句的耗时：
`show profiles;`
查看指定query_id的SQL语句各个阶段的耗时：
`show profile for query query_id;`
查看指定query_id的SQL语句CPU的使用情况
`show profile cpu for query query_id;`

### explain

EXPLAIN 或者 DESC 命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中表如何连接和连接的顺序。
语法：
- 直接在select语句之前加上关键字 explain / desc
- EXPLAIN SELECT 字段列表 FROM 表名 HWERE 条件;

EXPLAIN 各字段含义：

- id：select 查询的序列号，表示查询中执行 select 子句或者操作表的顺序（id相同，执行顺序从上到下；id不同，值越大越先执行）
- select_type：表示 SELECT 的类型，常见取值有 SIMPLE（简单表，即不适用表连接或者子查询）、PRIMARY（主查询，即外层的查询）、UNION（UNION中的第二个或者后面的查询语句）、SUBQUERY（SELECT/WHERE之后包含了子查询）等
- type：表示连接类型，性能由好到差的连接类型为 NULL、system、const、eq_ref、ref、range、index、all
- possible_key：可能应用在这张表上的索引，一个或多个
- Key：实际使用的索引，如果为 NULL，则没有使用索引
- Key_len：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好
- rows：MySQL认为必须要执行的行数，在InnoDB引擎的表中，是一个估计值，可能并不总是准确的
- filtered：表示返回结果的行数占需读取行数的百分比，filtered的值越大越好

### 使用规则

#### 最左前缀法则

如果索引关联了多列（联合索引），要遵守最左前缀法则，最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。
如果跳跃某一列，索引将部分失效（后面的字段索引失效）。

联合索引中，出现范围查询（<, >），范围查询右侧的列索引失效。可以用>=或者<=来规避索引失效问题。

#### 索引失效情况

1. 在索引列上进行运算操作，索引将失效。如：`explain select * from tb_user where substring(phone, 10, 2) = '15';`
2. 字符串类型字段使用时，不加引号，索引将失效。如：`explain select * from tb_user where phone = 17799990015;`，此处phone的值没有加引号
3. 模糊查询中，如果仅仅是尾部模糊匹配，索引不会是失效；如果是头部模糊匹配，索引失效。如：`explain select * from tb_user where profession like '%工程';`，前后都有 % 也会失效。
4. 用 or 分割开的条件，如果 or 其中一个条件的列没有索引，那么涉及的索引都不会被用到。
5. **数据分部影响**如果 MySQL 评估使用索引比全表更慢，则不使用索引。

#### SQL 提示

是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

例如，使用索引：
`explain select * from tb_user use index(idx_user_pro) where profession="软件工程";`
不使用哪个索引：
`explain select * from tb_user ignore index(idx_user_pro) where profession="软件工程";`
必须使用哪个索引：
`explain select * from tb_user force index(idx_user_pro) where profession="软件工程";`

use 是建议，实际使用哪个索引 MySQL 还会自己权衡运行速度去更改，force就是无论如何都强制使用该索引。

#### 覆盖索引&回表查询

尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能找到），减少 select *。

explain 中 extra 字段含义：
`using index condition`：查找使用了索引，但是需要回表查询数据
`using where; using index;`：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询

如果在聚集索引中直接能找到对应的行，则直接返回行数据，只需要一次查询，哪怕是select \*；如果在辅助索引中找聚集索引，如`select id, name from xxx where name='xxx';`，也只需要通过辅助索引(name)查找到对应的id，返回name和name索引对应的id即可，只需要一次查询；如果是通过辅助索引查找其他字段，则需要回表查询，如`select id, name, gender from xxx where name='xxx';`

所以尽量不要用`select *`，容易出现回表查询，降低效率，除非有联合索引包含了所有字段

面试题：一张表，有四个字段（id, username, password, status），由于数据量大，需要对以下SQL语句进行优化，该如何进行才是最优方案：
`select id, username, password from tb_user where username='itcast';`

解：给username和password字段建立联合索引，则不需要回表查询，直接覆盖索引

#### 前缀索引

当字段类型为字符串（varchar, text等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO，影响查询效率，此时可以只降字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

语法：`create index idx_xxxx on table_name(columnn(n));`
前缀长度：可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高，唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。
求选择性公式：
```mysql
select count(distinct email) / count(*) from tb_user;
select count(distinct substring(email, 1, 5)) / count(*) from tb_user;
```

show index 里面的sub_part可以看到接取的长度

#### 单列索引&联合索引

单列索引：即一个索引只包含单个列
联合索引：即一个索引包含了多个列
在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。

单列索引情况：
`explain select id, phone, name from tb_user where phone = '17799990010' and name = '韩信';`
这句只会用到phone索引字段

##### 注意事项

- 多条件联合查询时，MySQL优化器会评估哪个字段的索引效率更高，会选择该索引完成本次查询

### 设计原则

1. 针对于数据量较大，且查询比较频繁的表建立索引
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串类型的字段，字段长度较长，可以针对于字段的特点，建立前缀索引
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价就越大，会影响增删改的效率
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询

## SQL 优化

### 插入数据

普通插入：

1. 采用批量插入（一次插入的数据不建议超过1000条）
2. 手动提交事务
3. 主键顺序插入

大批量插入：
如果一次性需要插入大批量数据，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令插入。

```mysql
# 客户端连接服务端时，加上参数 --local-infile（这一行在bash/cmd界面输入）
mysql --local-infile -u root -p
# 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;
select @@local_infile;
# 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table 'tb_user' fields terminated by ',' lines terminated by '\n';
```

### 主键优化

数据组织方式：在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为**索引组织表**（Index organized table, IOT）

页分裂：页可以为空，也可以填充一般，也可以填充100%，每个页包含了2-N行数据（如果一行数据过大，会行溢出），根据主键排列。

页合并：当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用。当页中删除的记录到达 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前后）看看是否可以将这两个页合并以优化空间使用。

MERGE_THRESHOLD：合并页的阈值，可以自己设置，在创建表或创建索引时指定

> 文字说明不够清晰明了，具体可以看视频里的PPT演示过程：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=90

主键设计原则：

- 满足业务需求的情况下，尽量降低主键的长度
- 插入数据时，尽量选择顺序插入，选择使用 AUTO_INCREMENT 自增主键
- 尽量不要使用 UUID 做主键或者是其他的自然主键，如身份证号
- 业务操作时，避免对主键的修改

### order by优化

1. Using filesort：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区 sort buffer 中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序
2. Using index：通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外排序，操作效率高

如果order by字段全部使用升序排序或者降序排序，则都会走索引，但是如果一个字段升序排序，另一个字段降序排序，则不会走索引，explain的extra信息显示的是`Using index, Using filesort`，如果要优化掉Using filesort，则需要另外再创建一个索引，如：`create index idx_user_age_phone_ad on tb_user(age asc, phone desc);`，此时使用`select id, age, phone from tb_user order by age asc, phone desc;`会全部走索引

总结：

- 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
- 尽量使用覆盖索引
- 多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）
- 如果不可避免出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 sort_buffer_size（默认256k）

### group by优化

- 在分组操作时，可以通过索引来提高效率
- 分组操作时，索引的使用也是满足最左前缀法则的

如索引为`idx_user_pro_age_stat`，则句式可以是`select ... where profession order by age`，这样也符合最左前缀法则

### limit优化

常见的问题如`limit 2000000, 10`，此时需要 MySQL 排序前2000000条记录，但仅仅返回2000000 - 2000010的记录，其他记录丢弃，查询排序的代价非常大。
优化方案：一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化

例如：

```mysql
-- 此语句耗时很长
select * from tb_sku limit 9000000, 10;
-- 通过覆盖索引加快速度，直接通过主键索引进行排序及查询
select id from tb_sku order by id limit 9000000, 10;
-- 下面的语句是错误的，因为 MySQL 不支持 in 里面使用 limit
-- select * from tb_sku where id in (select id from tb_sku order by id limit 9000000, 10);
-- 通过连表查询即可实现第一句的效果，并且能达到第二句的速度
select * from tb_sku as s, (select id from tb_sku order by id limit 9000000, 10) as a where s.id = a.id;
```

### count优化

MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(\*) 的时候会直接返回这个数，效率很高（前提是不适用where）；
InnoDB 在执行 count(\*) 时，需要把数据一行一行地从引擎里面读出来，然后累计计数。
优化方案：自己计数，如创建key-value表存储在内存或硬盘，或者是用redis

count的几种用法：

- 如果count函数的参数（count里面写的那个字段）不是NULL（字段值不为NULL），累计值就加一，最后返回累计值
- 用法：count(\*)、count(主键)、count(字段)、count(1)
- count(主键)跟count(\*)一样，因为主键不能为空；count(字段)只计算字段值不为NULL的行；count(1)引擎会为每行添加一个1，然后就count这个1，返回结果也跟count(\*)一样；count(null)返回0

各种用法的性能：

- count(主键)：InnoDB引擎会遍历整张表，把每行的主键id值都取出来，返回给服务层，服务层拿到主键后，直接按行进行累加（主键不可能为空）
- count(字段)：没有not null约束的话，InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为null，不为null，计数累加；有not null约束的话，InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，直接按行进行累加
- count(1)：InnoDB 引擎遍历整张表，但不取值。服务层对于返回的每一层，放一个数字 1 进去，直接按行进行累加
- count(\*)：InnoDB 引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加

按效率排序：count(字段) < count(主键) < count(1) < count(\*)，所以尽量使用 count(\*)

### update优化（避免行锁升级为表锁）

InnoDB 的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级为表锁。

如以下两条语句：
`update student set no = '123' where id = 1;`，这句由于id有主键索引，所以只会锁这一行；
`update student set no = '123' where name = 'test';`，这句由于name没有索引，所以会把整张表都锁住进行数据更新，解决方法是给name字段添加索引

## 视图/存储过程/触发器

### 视图

视图(View)是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。
通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。

#### 语法

- 视图基本语法
```mysql
1. 创建:
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH[CASCADED|LOCAL]CHECK OPTION]

2. 查询:
查看创建视图语句：SHOW CREATE VIEW 视图名称;
查看视图数据：SELECT * FROM 视图名称 ...... ;

3. 修改:
方式一：CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH
[ CASCADED | LOCAL ] CHECK OPTION ]
方式二：ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED |
LOCAL ] CHECK OPTION ]

4. 删除:
DROP VIEW [IF EXISTS] 视图名称 [,视图名称] ...
```

- 视图案例演示
```mysql
-- 创建视图
create or replace view stu_v_1 as select id,name from student where id <= 10;
-- 查询视图
show create view stu_v_1;
select * from stu_v_1;
select * from stu_v_1 where id < 3;
-- 修改视图
create or replace view stu_v_1 as select id,name,no from student where id <= 10;
alter view stu_v_1 as select id,name from student where id <= 10;
-- 删除视图
drop view if exists stu_v_1;
```
上述我们演示了，视图应该如何创建、查询、修改、删除，那么我们能不能通过视图来插入、更新数据
呢？ 接下来，做一个测试。

```mysql
create or replace view stu_v_1 as select id,name from student where id <= 10 ;
select * from stu_v_1;

insert into stu_v_1 values(6,'Tom');
insert into stu_v_1 values(17,'Tom22');
```
执行上述的SQL，我们会发现，id为6和17的数据都是可以成功插入的。 但是我们执行查询，查询出
来的数据，却没有id为17的记录。
因为我们在创建视图的时候，指定的条件为 id<=10, id为17的数据，是不符合条件的，所以没有查
询出来，但是这条数据确实是已经成功的插入到了基表中。
如果我们定义视图时，如果指定了条件，然后我们在插入、修改、删除数据时，是否可以做到必须满足
条件才能操作，否则不能够操作呢？ 答案是可以的，这就需要借助于视图的检查选项了。


#### 检查选项
当使用**WITH CHECK OPTION**子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插
入，更新，删除，以使其符合视图的定义。 MySQL允许基于另一个视图创建视图，它还会检查依赖视
图中的规则以保持一致性。为了确定检查的范围，mysql提供了两个选项： CASCADED 和 LOCAL
，默认值为 CASCADED 。

1. CASCADED
级联。
比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 cascaded，但是v1视图
创建时未指定检查选项。 则在执行检查时，不仅会检查v2，还会级联检查v2的关联视图v1

2. LOCAL
本地。
比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 local ，但是v1视图创
建时未指定检查选项。 则在执行检查时，知会检查v2，不会检查v2的关联视图v1。

#### 视图的更新

要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一
项，则该视图不可更新：
A. 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等）
B. DISTINCT
C. GROUP BY
D. HAVING
E. UNION 或者 UNION ALL
示例演示:
```mysql
 create view stu_v_count as select count(*) from student;
```
上述的视图中，就只有一个单行单列的数据，如果我们对这个视图进行更新或插入的，将会报错。

```mysql
insert into stu_v_count values(10);
```
#### 视图作用
1. 简单
视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视
图，从而使得用户不必为以后的操作每次指定全部的条件。
2. 安全
数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据
```mysql
 create view stu_v_count as select count(*) from student;

 insert into stu_v_count values(10);
```
3. 数据独立
视图可帮助用户屏蔽真实表结构变化带来的影响。

#### 案例
1. 为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽手机号和邮箱两个字段。
```mysql
create view tb_user_view as select id,name,profession,age,gender,status,createtime from tb_user;

select * from tb_user_view;
```
2. 查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操
作，定义一个视图。

```mysql
create view tb_stu_course_view as select s.name student_name , s.no student_no ,
c.name course_name from student s, student_course sc , course c where s.id =
sc.studentid and sc.courseid = c.id;

select * from tb_stu_course_view;
```
### 存储过程

#### 介绍

存储过程是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程可以简化应用开发
人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。
存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。
![演示图](./picture/%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B.png "演示图")
特点:
- 封装，复用 -----------------------> 可以把某一业务SQL封装在存储过程中，需要用到
的时候直接调用即可。
- 可以接收参数，也可以返回数据 --------> 再存储过程中，可以传递参数，也可以接收返回
值。
- 减少网络交互，效率提升 -------------> 如果涉及到多条SQL，每执行一次都是一次网络传
输。 而如果封装在存储过程中，我们只需要网络交互一次可能就可以了。
#### 语法
```mysql
1. 创建
CREATE PROCEDURE 存储过程名称 ([ 参数列表 ])
BEGIN
-- SQL语句
END ;

2. 调用
CALL 名称 ([ 参数 ]);

3. 查看
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'xxx'; -- 查询指
定数据库的存储过程及状态信息
SHOW CREATE PROCEDURE 存储过程名称 ; -- 查询某个存储过程的定义

4. 删除
DROP PROCEDURE [ IF EXISTS ] 存储过程名称 ；

```
**注意:**
在命令行中，执行创建存储过程的SQL时，需要通过关键字 delimiter 指定SQL语句的
结束符

演示示例:
```mysql
-- 存储过程基本语法
-- 创建
create procedure p1()
begin
select count(*) from student;
end;
-- 调用
call p1();
-- 查看
select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'itcast';
show create procedure p1;
-- 删除
drop procedure if exists p1;

```
#### 变量
在MySQL中变量分为三种类型: 系统变量、用户定义变量、局部变量。

##### 系统变量
系统变量 是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量（GLOBAL）、会话变量（SESSION）。

```mysql
1. 查看系统变量
SHOW [ SESSION | GLOBAL ] VARIABLES ; -- 查看所有系统变量
SHOW [ SESSION | GLOBAL ] VARIABLES LIKE '......'; -- 可以通过LIKE模糊匹配方
式查找变量
SELECT @@[SESSION | GLOBAL] 系统变量名; -- 查看指定变量的值

2.设置系统变量
SET [ SESSION | GLOBAL ] 系统变量名 = 值 ;
SET @@[SESSION | GLOBAL]系统变量名 = 值 ;

注意:
如果没有指定SESSION/GLOBAL，默认是SESSION，会话变量。
A. 全局变量(GLOBAL): 全局变量针对于所有的会话。
B. 会话变量(SESSION): 会话变量针对于单个会话，在另外一个会话窗口就不生效了。

```
演示示例:
```mysql
-- 查看系统变量
show session variables ;
show session variables like 'auto%';
show global variables like 'auto%';
select @@global.autocommit;
select @@session.autocommit;
-- 设置系统变量
set session autocommit = 1;
insert into course(id, name) VALUES (6, 'ES');
set global autocommit = 0;
select @@global.autocommit;
```

##### 用户定义变量
用户定义变量 是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用 "@变量
名" 使用就可以。其作用域为当前连接。
```mysql
1. 赋值
方式一:
SET @var_name = expr [, @var_name = expr] ... ;
SET @var_name := expr [, @var_name := expr] ... ;
赋值时，可以使用 = ，也可以使用 := 。
方式二:
SELECT @var_name := expr [, @var_name := expr] ... ;
SELECT 字段名 INTO @var_name FROM 表名;

2. 使用
SELECT @var_name ;

注意: 用户定义的变量无需对其进行声明或初始化，只不过获取到的值为NULL。
```
演示示例:

```mysql
-- 赋值
set @myname = 'itcast';
set @myage := 10;
set @mygender := '男',@myhobby := 'java';
select @mycolor := 'red';
select count(*) into @mycount from tb_user;
-- 使用
select @myname,@myage,@mygender,@myhobby;
select @mycolor , @mycount;
select @abc;
```

##### 局部变量
局部变量 是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的
局部变量和输入参数，局部变量的范围是在其内声明的BEGIN ... END块。

1. 声明
```mysql
1 DECLARE 变量名 变量类型 [DEFAULT ... ] ;
```
2. 赋值
```mysql
SET 变量名 = 值 ;
SET 变量名 := 值 ;
SELECT 字段名 INTO 变量名 FROM 表名 ... ;
```

演示示例:
```mysql
-- 声明局部变量 - declare
-- 赋值
create procedure p2()
begin
declare stu_count int default 0;
select count(*) into stu_count from student;
select stu_count;
end;
call p2();
```
#### if
if 用于做条件判断，具体的语法结构为：
```mysql
IF 条件1 THEN
.....
ELSEIF 条件2 THEN -- 可选
.....
ELSE -- 可选
.....
END IF;

```
在if条件判断的结构中，ELSE IF 结构可以有多个，也可以没有。 ELSE结构可以有，也可以没有。

案例:
根据定义的分数score变量，判定当前分数对应的分数等级。
- score >= 85分，等级为优秀。
- score >= 60分 且 score < 85分，等级为及格。
- score < 60分，等级为不及格。
```mysql
create procedure p3()
begin
declare score int default 58;
declare result varchar(10);
if score >= 85 then
set result := '优秀';
elseif score >= 60 then
set result := '及格';
else
set result := '不及格';
end if;
select result;
end;
call p3();

```
上述的需求我们虽然已经实现了，但是也存在一些问题，比如：score 分数我们是在存储过程中定义死的，而且最终计算出来的分数等级，我们也仅仅是最终查询展示出来而已。
那么我们能不能，把score分数动态的传递进来，计算出来的分数等级是否可以作为返回值返回呢？
我们可以通过 参数 来解决上述的问题。

#### 参数
参数的类型，主要分为以下三种：IN、OUT、INOUT。 具体的含义如下：
|类型 |含义|备注|
|------|------|------|
|IN |该类参数作为输入，也就是需要调用时传入值|默认|
|OUT |该类参数作为输出，也就是该参数可以作为返回值|
INOUT|既可以作为输入参数，也可以作为输出参数|
用法：
```mysql
CREATE PROCEDURE 存储过程名称 ([ IN/OUT/INOUT 参数名 参数类型 ])
BEGIN
-- SQL语句
END ;

```

- 案例一
根据传入参数score，判定当前分数对应的分数等级，并返回。
- score >= 85分，等级为优秀。
- score >= 60分 且 score < 85分，等级为及格。
- score < 60分，等级为不及格。
```mysql
create procedure p4(in score int, out result varchar(10))
begin
	if score >= 85 then
	set result := '优秀';
	elseif score >= 60 then
	set result := '及格';
	else
	set result := '不及格';
	end if;
end;
-- 定义用户变量 @result来接收返回的数据, 用户变量可以不用声明
call p4(18, @result);
select @result;

```
- 案例二
将传入的200分制的分数，进行换算，换算成百分制，然后返回。
``` mysql
create procedure p5(inout score double)
begin
	set score := score * 0.5;
end;
set @score = 198;
call p5(@score);
select @score;
```

#### case
case结构及作用，和我们在基础篇中所讲解的流程控制函数很类似。有两种语法格式：
语法1：
```mysql
-- 含义： 当case_value的值为 when_value1时，执行statement_list1，当值为 when_value2时，
执行statement_list2， 否则就执行 statement_list
CASE case_value
WHEN when_value1 THEN statement_list1
[ WHEN when_value2 THEN statement_list2] ...
[ ELSE statement_list ]
END CASE;

```
语法2：
```mysql
-- 含义： 当条件search_condition1成立时，执行statement_list1，当条件search_condition2成
立时，执行statement_list2， 否则就执行 statement_list
CASE
WHEN search_condition1 THEN statement_list1
[WHEN search_condition2 THEN statement_list2] ...
[ELSE statement_list]
END CASE;

```
 案例:
 根据传入的月份，判定月份所属的季节（要求采用case结构）。
- 1-3月份，为第一季度
- 4-6月份，为第二季度
- 7-9月份，为第三季度
- 10-12月份，为第四季度
 ```mysql
create procedure p6(in month int)
begin
	declare result varchar(10);
	case
	when month >= 1 and month <= 3 then
	set result := '第一季度';
	when month >= 4 and month <= 6 then
	set result := '第二季度';
	when month >= 7 and month <= 9 then
	set result := '第三季度';
	when month >= 10 and month <= 12 then
	set result := '第四季度';
	else
	set result := '非法参数';
	end case ;
	select concat('您输入的月份为: ',month, ', 所属的季度为: ',result);
end;
call p6(16);

 ```
 > 注意：如果判定条件有多个，多个条件之间，可以使用 and 或 or 进行连接。
 #### while
 while 循环是有条件的循环控制语句。满足条件后，再执行循环体中的SQL语句。
 具体语法为：
 ```mysql
 -- 先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑
WHILE 条件 DO
SQL逻辑...
END WHILE;

 ```
案例:
- 计算从1累加到n的值，n为传入的参数值。
```mysql
-- A. 定义局部变量, 记录累加之后的值;
-- B. 每循环一次, 就会对n进行减1 , 如果n减到0, 则退出循环
CREATE PROCEDURE p7 ( IN n INT ) BEGIN
	DECLARE
		total INT DEFAULT 0;
	WHILE
			n > 0 DO
			
			SET total := total + n;
		
		SET n := n - 1;
		
	END WHILE;
	SELECT
		total;
	
END;
CALL p7 ( 100 );
```
#### repeat
repeat是有条件的循环控制语句, 当满足until声明的条件的时候，则退出循环 。
具体语法为：
```mysql
-- 先执行一次逻辑，然后判定UNTIL条件是否满足，如果满足，则退出。如果不满足，则继续下一次循环
REPEAT
SQL逻辑...
UNTIL 条件
END REPEAT;
```
案例:
- 计算从1累加到n的值，n为传入的参数值。(使用repeat实现)
```mysql
-- A. 定义局部变量, 记录累加之后的值;
-- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环
CREATE PROCEDURE p8 ( IN n INT ) BEGIN
	DECLARE
		total INT DEFAULT 0;
	REPEAT
			
			SET total := total + n;
		
		SET n := n - 1;
		UNTIL n <= 0 
	END REPEAT;
	SELECT
		total;
	
END;
CALL p8 ( 10 );
CALL p8 ( 100 );
```

#### loop
LOOP 实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用其来实现简单的死循环。
LOOP可以配合一下两个语句使用：
- LEAVE ：配合循环使用，退出循环。
- ITERATE：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。
```mysql
[begin_label:] LOOP
SQL逻辑...
END LOOP [end_label];
```
```mysql
LEAVE label; -- 退出指定标记的循环体
ITERATE label; -- 直接进入下一次循环
```
上述语法中出现的 begin_label，end_label，label 指的都是我们所自定义的标记。
案例一:
计算从1累加到n的值，n为传入的参数值
```mysql
-- A. 定义局部变量, 记录累加之后的值;
-- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环 ----> leave xx
CREATE PROCEDURE p9 ( IN n INT ) BEGIN
	DECLARE
		total INT DEFAULT 0;
	sum :
	LOOP
		IF
			n <= 0 THEN
				LEAVE sum;
			
		END IF;
		
		SET total := total + n;
		
		SET n := n - 1;
		
	END LOOP sum;
	SELECT
		total;
	
END;
CALL p9 ( 100 );
```
案例二:
计算从1到n之间的偶数累加的值，n为传入的参数值。
```mysql
-- A. 定义局部变量, 记录累加之后的值;
-- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环 ----> leave xx
-- C. 如果当次累加的数据是奇数, 则直接进入下一次循环. --------> iterate xx
CREATE PROCEDURE p10 ( IN n INT ) BEGIN
	DECLARE
		total INT DEFAULT 0;
	sum :
	LOOP
		IF
			n <= 0 THEN
				LEAVE sum;
			
		END IF;
		IF
			n % 2 = 1 THEN
				
				SET n := n - 1;
			ITERATE sum;
			
		END IF;
		
		SET total := total + n;
		
		SET n := n - 1;
		
	END LOOP sum;
	SELECT
		total;
	
END;
CALL p10 ( 100 );

```
#### 游标
游标（CURSOR）是用来存储查询结果集的数据类型 , 在存储过程和函数中可以使用游标对结果集进
行循环的处理。游标的使用包括游标的声明、OPEN、FETCH 和 CLOSE，其语法分别如下。
A. 声明游标
```mysql
DECLARE 游标名称 CURSOR FOR 查询语句 ;
```
B. 打开游标
```mysql
OPEN 游标名称 ;
```
C. 获取游标记录
```mysql
FETCH 游标名称 INTO 变量 [, 变量 ] ;
```
D. 关闭游标
```mysql
CLOSE 游标名称 ;
```
 案例:
 根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名
（name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表
(id,name,profession)中。
```mysql

-- 逻辑:
-- A. 声明游标, 存储查询结果集
-- B. 准备: 创建表结构
-- C. 开启游标
-- D. 获取游标中的记录
-- E. 插入数据到新表中
-- F. 关闭游标
CREATE PROCEDURE p11 ( IN uage INT ) BEGIN
	DECLARE
		uname VARCHAR ( 100 );
	DECLARE
		upro VARCHAR ( 100 );
	DECLARE
		u_cursor CURSOR FOR SELECT NAME
		,
		profession 
	FROM
		tb_user 
	WHERE
		age <= uage;
	DROP TABLE
	IF
		EXISTS tb_user_pro;
	CREATE TABLE
	IF
		NOT EXISTS tb_user_pro ( id INT PRIMARY KEY auto_increment, NAME VARCHAR ( 100 ), profession VARCHAR ( 100 ) );
	OPEN u_cursor;
	WHILE
		TRUE DO
			FETCH u_cursor INTO uname,
			upro;
		INSERT INTO tb_user_pro
		VALUES
			( NULL, uname, upro );
		
	END WHILE;
CLOSE u_cursor;
END;
```
>上述的存储过程，最终我们在调用的过程中，会报错，之所以报错是因为上面的while循环中，并没有
退出条件。当游标的数据集获取完毕之后，再次获取数据，就会报错，从而终止了程序的执行。
但是此时，tb_user_pro表结构及其数据都已经插入成功了。
要想解决这个问题，就需要通过MySQL中提供的 条件处理程序 Handler 来解决。

#### 条件处理程序
条件处理程序（Handler）可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。具体
语法为：
```mysql
DECLARE handler_action HANDLER FOR condition_value [, condition_value]
... statement ;

handler_action 的取值：
	CONTINUE: 继续执行当前程序
	EXIT: 终止执行当前程序
condition_value 的取值：

	SQLSTATE sqlstate_value: 状态码，如 02000
	SQLWARNING: 所有以01开头的SQLSTATE代码的简写
	NOT FOUND: 所有以02开头的SQLSTATE代码的简写
	SQLEXCEPTION: 所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写
```
 案例:
 根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名
（name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表
(id,name,profession)中。
A. 通过SQLSTATE指定具体的状态码

```mysql
-- 逻辑:
-- A. 声明游标, 存储查询结果集
-- B. 准备: 创建表结构
-- C. 开启游标
-- D. 获取游标中的记录
-- E. 插入数据到新表中
-- F. 关闭游标
CREATE PROCEDURE p11 ( IN uage INT ) BEGIN
	DECLARE
		uname VARCHAR ( 100 );
	DECLARE
		upro VARCHAR ( 100 );
	DECLARE
		u_cursor CURSOR FOR SELECT NAME
		,
		profession 
	FROM
		tb_user 
	WHERE
		age <= uage;
		-- 声明条件处理程序 ： 当SQL语句执行抛出的状态码为02000时，将关闭游标u_cursor，并退出
	DECLARE
		EXIT HANDLER FOR SQLSTATE '02000' CLOSE u_cursor;
	DROP TABLE
	IF
		EXISTS tb_user_pro;
	CREATE TABLE
	IF
		NOT EXISTS tb_user_pro ( id INT PRIMARY KEY auto_increment, NAME VARCHAR ( 100 ), profession VARCHAR ( 100 ) );
	OPEN u_cursor;
	WHILE
		TRUE DO
			FETCH u_cursor INTO uname,
			upro;
		INSERT INTO tb_user_pro
		VALUES
			( NULL, uname, upro );
		
	END WHILE;
	CLOSE u_cursor;
	
END;
CALL p11 ( 30 );

```
B. 通过SQLSTATE的代码简写方式 NOT FOUND
02 开头的状态码，代码简写为 NOT FOUND
```mysql
CREATE PROCEDURE p12 ( IN uage INT ) BEGIN
	DECLARE
		uname VARCHAR ( 100 );
	DECLARE
		upro VARCHAR ( 100 );
	DECLARE
		u_cursor CURSOR FOR SELECT NAME
		,
		profession 
	FROM
		tb_user 
	WHERE
		age <= uage;-- 声明条件处理程序 ： 当SQL语句执行抛出的状态码为02开头时，将关闭游标u_cursor，并退出
	DECLARE
		EXIT HANDLER FOR NOT found CLOSE u_cursor;
	DROP TABLE
	IF
		EXISTS tb_user_pro;
	CREATE TABLE
	IF
		NOT EXISTS tb_user_pro ( id INT PRIMARY KEY auto_increment, NAME VARCHAR ( 100 ), profession VARCHAR ( 100 ) );
	OPEN u_cursor;
	WHILE
		TRUE DO
			FETCH u_cursor INTO uname,
			upro;
		INSERT INTO tb_user_pro
		VALUES
			( NULL, uname, upro );
		
	END WHILE;
	CLOSE u_cursor;
	
END;
CALL p12 ( 30 );
```
具体的错误状态码，可以参考官方文档：
https://dev.mysql.com/doc/refman/8.0/en/declare-handler.html
https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html


### 存储函数
存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的。具体语法如下
```mysql
CREATE FUNCTION 存储函数名称 ([ 参数列表 ])
RETURNS type [characteristic ...]
BEGIN
	-- SQL语句
	RETURN ...;
END ;
```
characteristic说明：
- DETERMINISTIC：相同的输入参数总是产生相同的结果
- NO SQL ：不包含 SQL 语句。
- READS SQL DATA：包含读取数据的语句，但不包含写入数据的语句

 案例:
 计算从1累加到n的值，n为传入的参数值。
```mysql
create function fun1(n int)
returns int deterministic
begin
	declare total int default 0;
	while n>0 do
	set total := total + n;
	set n := n - 1;
	end while;
	return total;
end;
select fun1(50);

```
在mysql8.0版本中binlog默认是开启的，一旦开启了，mysql就要求在定义存储过程时，需要指定
characteristic特性。

### 触发器
触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触
发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性
, 日志记录 , 数据校验等操作 。
使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还
只支持行级触发，不支持语句级触发。
|触发器类型|NEW 和 OLD|
|------|------|
|INSERT 型触发器|NEW 表示将要或者已经新增的数据|
UPDATE 型触发器|OLD 表示修改之前的数据 , NEW 表示将要或已经修改后的数据|
|DELETE 型触发器 |OLD 表示将要或者已经删除的数据|

语法：
1. 创建
```mysql
CREATE TRIGGER trigger_name

BEFORE/AFTER INSERT/UPDATE/DELETE

ON tbl_name FOR EACH ROW -- 行级触发器
BEGIN
	trigger_stmt ;
END;
```
2. 查看
```mysql
SHOW TRIGGERS ;
```
3. 删除
```mysql
DROP TRIGGER [schema_name.]trigger_name ; -- 如果没有指定 schema_name，默认为当前数
据库 。
```
案例:
通过触发器记录 tb_user 表的数据变更日志，将变更日志插入到日志表user_logs中, 包含增加,
修改 , 删除 ;
表结构准备:
```mysql
-- 准备工作 : 日志表 user_logs
create table user_logs(
	id int(11) not null auto_increment,
	operation varchar(20) not null comment '操作类型, insert/update/delete',
	operate_time datetime not null comment '操作时间',
	operate_id int(11) not null comment '操作的ID',
	operate_params varchar(500) comment '操作参数',
	primary key(`id`)
)engine=innodb default charset=utf8;
```
A. 插入数据触发器
```mysql
create trigger tb_user_insert_trigger
	after insert on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params)
VALUES
	(null, 'insert', now(), new.id, concat('插入的数据内容为:
id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ',
profession=', NEW.profession));
end;

```
测试:
```mysql
-- 查看
show triggers ;

-- 插入数据到tb_user
insert into tb_user(id, name, phone, email, profession, age, gender, status,
createtime) VALUES (26,'三皇子','18809091212','erhuangzi@163.com','软件工
程',23,'1','1',now());

```
测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。
B. 修改数据触发器
```mysql
create trigger tb_user_update_trigger
	after update on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params)
	VALUES
	(null, 'update', now(), new.id,
	concat('更新之前的数据: id=',old.id,',name=',old.name, ', phone=',
	old.phone, ', email=', old.email, ', profession=', old.profession,
	' | 更新之后的数据: id=',new.id,',name=',new.name, ', phone=',
	NEW.phone, ', email=', NEW.email, ', profession=', NEW.profession));
end;
```
测试:
```mysql
-- 查看

show triggers ;
-- 更新
update tb_user set profession = '会计' where id = 23;
update tb_user set profession = '会计' where id <= 5;
```
测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。

C. 删除数据触发器
```mysql
create trigger tb_user_delete_trigger
	after delete on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params)
	VALUES
	(null, 'delete', now(), old.id,
	concat('删除之前的数据: id=',old.id,',name=',old.name, ', phone=',
	old.phone, ', email=', old.email, ', profession=', old.profession));
end;
```
测试:
```mysql
-- 查看
show triggers ;
-- 删除数据
delete from tb_user where id = 26;
```
测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。

##  锁
锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算资源（CPU、
RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有
效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个
角度来说，锁对数据库而言显得尤其重要，也更加复杂。
MySQL中的锁，按照锁的粒度分，分为以下三类：
- 全局锁：锁定数据库中的所有表。
- 表级锁：每次操作锁住整张表。
- 行级锁：每次操作锁住对应的行数据。

### 全局锁
全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语
句，已经更新操作的事务提交语句都将被阻塞。

其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整
性。
A. 我们一起先来分析一下不加全局锁，可能存在的问题。
假设在数据库中存在这样三张表: tb_stock 库存表，tb_order 订单表，tb_orderlog 订单日
志表。

![演示图](./picture/%E5%85%A8%E5%B1%80%E9%94%81.png "全局锁演示图")

- 在进行数据备份时，先备份了tb_stock库存表。
- 然后接下来，在业务系统中，执行了下单操作，扣减库存，生成订单（更新tb_stock表，插入
tb_order表）。
- 然后再执行备份 tb_order表的逻辑。
- 业务中执行插入订单日志操作。
- 最后，又备份了tb_orderlog表。

B. 再来分析一下加了全局锁后的情况
![演示图](./picture/%E5%85%A8%E5%B1%80%E9%94%81%E5%8E%9F%E7%90%86.png "全局锁演示图")
对数据库进行进行逻辑备份之前，先对整个数据库加上全局锁，一旦加了全局锁之后，其他的DDL、
DML全部都处于阻塞状态，但是可以执行DQL语句，也就是处于只读状态，而数据备份就是查询操作。
那么数据在进行逻辑备份的过程中，数据库中的数据就是不会发生变化的，这样就保证了数据的一致性
和完整性。
语法：
1. 加全局锁
```mysql
flush tables with read lock ;
```
2. 数据备份
```mysql
mysqldump -uroot –p1234 itcast > itcast.sql
```
数据备份的相关指令, 在后面MySQL管理章节, 还会详细讲解.

3. 释放锁
```mysql
unlock tables ;
```

特点:
数据库中加全局锁，是一个比较重的操作，存在以下问题：
- 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。
- 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导
致主从延迟。

在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致
性数据备份。

```mysql
mysqldump --single-transaction -uroot –p123456 itcast > itcast.sql
```

### 表级锁
表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、
InnoDB、BDB等存储引擎中。

对于表级锁，主要分为以下三类：
- 表锁
- 元数据锁（meta data lock，MDL）
- 意向锁

#### 表锁:
- 对于表锁，分为两类：
- 表共享读锁（read lock）
- 表独占写锁（write lock）
语法：
- 加锁：lock tables 表名... read/write。
- 释放锁：unlock tables / 客户端断开连接 。

特点:
A. 读锁

![演示图](./picture/%E8%AF%BB%E9%94%81.png "读锁")

左侧为客户端一，对指定表加了读锁，不会影响右侧客户端二的读，但是会阻塞右侧客户端的写。
测试:
![演示图](./picture/%E8%AF%BB%E9%94%81%E6%B5%8B%E8%AF%95.png "读锁测试")

B. 写锁
![演示图](./picture/%E5%86%99%E9%94%81.png "写锁")

左侧为客户端一，对指定表加了写锁，会阻塞右侧客户端的读和写。
测试:
![演示图](./picture/%E5%86%99%E9%94%81%E6%B5%8B%E8%AF%95.png "写锁测试")

> 结论: 读锁不会阻塞其他客户端的读，但是会阻塞写。写锁既会阻塞其他客户端的读，又会阻塞
其他客户端的写。

#### 元数据锁
meta data lock , 元数据锁，简写MDL。
MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维
护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。为了避免DML与
DDL冲突，**保证读写的正确性。**
这里的元数据，大家可以简单理解为就是一张表的表结构。 也就是说，某一张表涉及到未提交的事务
时，是不能够修改这张表的表结构的。
在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享)；当对表结构进行变
更操作的时候，加MDL写锁(排他)。
常见的SQL操作时，所添加的元数据锁：
|对应SQL|锁类型|说明|
| ------| ------| ------|
|lock tables xxx read /write|SHARED_READ_ONLY /<br>SHARED_NO_READ_WRITE|
|select 、select ...<br>lock in share mode|SHARED_READ|与SHARED_READ、<br>SHARED_WRITE兼容，与<br>EXCLUSIVE互斥|
|insert 、update、<br>delete、select ... for<br>update|SHARED_WRITE|与SHARED_READ、<br>SHARED_WRITE兼容，与<br>EXCLUSIVE互斥|
|alter table ... |EXCLUSIVE|与其他的MDL都互斥


演示：
当执行SELECT、INSERT、UPDATE、DELETE等语句时，添加的是元数据共享锁（SHARED_READ /
SHARED_WRITE），之间是兼容的
![演示图](./picture/%E5%85%83%E7%B4%A0%E9%94%811.png "元素共享锁")
当执行SELECT语句时，添加的是元数据共享锁（SHARED_READ），会阻塞元数据排他锁
（EXCLUSIVE），之间是互斥的。

![演示图](./picture/%E5%85%83%E7%B4%A0%E9%94%812.png "元素共享锁")
我们可以通过下面的SQL，来查看数据库中的元数据锁的情况：
```mysql
select object_type,object_schema,object_name,lock_type,lock_duration from
performance_schema.metadata_locks ;
```

我们在操作过程中，可以通过上述的SQL语句，来查看元数据锁的加锁情况。
![演示图](./picture/%E5%85%83%E7%B4%A0%E9%94%813.png "元素共享锁")
#### 意向锁

为了避免DML在执行时，加的行锁与表锁的冲突，在InnoDB中引入了意向锁，使得表锁不用检查每行
数据是否加锁，使用意向锁来减少表锁的检查。

假如没有意向锁，客户端一对表加了行锁后，客户端二如何给表加表锁呢，来通过示意图简单分析一
下：
首先客户端一，开启一个事务，然后执行DML操作，在执行DML语句时，会对涉及到的行加行锁。

![演示图](./picture/%E6%84%8F%E5%90%91%E9%94%81%E7%A4%BA%E6%84%8F%E5%9B%BE.png "意向锁示意图")
当客户端二，想对这张表加表锁时，会检查当前表是否有对应的行锁，如果没有，则添加表锁，此时就
会从第一行数据，检查到最后一行数据，效率较低
![演示图](./picture/%E6%84%8F%E5%90%91%E9%94%81%E7%A4%BA%E6%84%8F%E5%9B%BE2.png "意向锁示意图")
有了意向锁之后 :
客户端一，在执行DML操作时，会对涉及的行加行锁，同时也会对该表加上意向锁。

![演示图](./picture/%E5%85%83%E7%B4%A0%E9%94%813.png "意向锁示意图")

而其他客户端，在对这张表加表锁的时候，会根据该表上所加的意向锁来判定是否可以成功加表锁，而
不用逐行判断行锁情况了。

![演示图](./picture/%E6%84%8F%E5%90%91%E9%94%81%E7%A4%BA%E6%84%8F%E5%9B%BE4.png "意向锁示意图")

分类：
- 意向共享锁(IS): 由语句select ... lock in share mode添加 。 与 表锁共享锁(read)兼容，与表锁排他锁(write)互斥。
- 意向排他锁(IX): 由insert、update、delete、select...for update添加 。与表锁共享锁(read)及排他锁(write)都互斥，意向锁之间不会互斥。
> 一旦事务提交了，意向共享锁、意向排他锁，都会自动释放。

可以通过以下SQL，查看意向锁及行锁的加锁情况：

```mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from
performance_schema.data_locks;
```
演示：
![演示图](./picture/%E6%84%8F%E5%90%91%E9%94%81%E7%A4%BA%E6%84%8F%E5%9B%BE5.png "意向锁示意图")
![演示图](./picture/%E6%84%8F%E5%90%91%E9%94%81%E7%A4%BA%E6%84%8F%E5%9B%BE6.png "意向锁示意图")
### 行级锁
- 行锁（Record Lock）：锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。
![演示图](./picture/%E8%A1%8C%E9%94%81.png "行锁")

- 间隙锁（Gap Lock）：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持。
![演示图](./picture/%E9%97%B4%E9%9A%99%E9%94%81.png "间隙锁")

- 临键锁（Next-Key Lock）：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持。
![演示图](./picture/%E4%B8%B4%E6%97%B6%E9%94%81.png "临建锁")

#### 行锁
InnoDB实现了以下两种类型的行锁：
- 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。
- 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他
锁。
两种行锁的兼容情况如下:

![演示图](./picture/%E5%85%BC%E5%AE%B9%E6%83%85%E5%86%B5.png "兼容情况")

常见的SQL语句，在执行时，所加的行锁如下：
|SQL|行锁类型|说明|
| ------| ------| ------|
|INSERT ...|排他锁|自动加锁|
|UPDATE ... |排他锁|自动加锁|
|DELETE ...|排他锁|自动加锁|
|SELECT（正常）|不加任何锁|
|SELECT ... LOCK IN SHARE MODE|共享锁|需要手动在SELECT之后加LOCK IN HARE MODE|
|SELECT ... FOR UPDATE|排他锁|需要手动在SELECT之后加FOR UPDATE|

2. 演示
默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜
索和索引扫描，以防止幻读。
- 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。
- InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记
录加锁，此时 就会升级为表锁。
可以通过以下SQL，查看意向锁及行锁的加锁情况：
```mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from
performance_schema.data_locks;
```
**示例演示**
数据准备:
```mysql
CREATE TABLE `stu` (
	`id` int NOT NULL PRIMARY KEY AUTO_INCREMENT,
	`name` varchar(255) DEFAULT NULL,
	`age` int NOT NULL
) ENGINE = InnoDB CHARACTER SET = utf8mb4;
INSERT INTO `stu` VALUES (1, 'tom', 1);
INSERT INTO `stu` VALUES (3, 'cat', 3);
INSERT INTO `stu` VALUES (8, 'rose', 8);
INSERT INTO `stu` VALUES (11, 'jetty', 11);
INSERT INTO `stu` VALUES (19, 'lily', 19);
INSERT INTO `stu` VALUES (25, 'luci', 25);
```
演示行锁的时候，我们就通过上面这张表来演示一下。
A. 普通的select语句，执行时，不会加锁。
B. select...lock in share mode，加共享锁，共享锁与共享锁之间兼容
共享锁与排他锁之间互斥。
C. 排它锁与排他锁之间互斥
D. 无索引行锁升级为表锁

### 间隙锁&临键锁
默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜索和索引扫描，以防止幻读。
- 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。
- 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-key
lock 退化为间隙锁。
- 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

>注意：间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会
阻止另一个事务在同一间隙上采用间隙锁。

**示例演示**
A. 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。
B. 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-key
lock 退化为间隙锁。
介绍分析一下：
我们知道InnoDB的B+树索引，叶子节点是有序的双向链表。 假如，我们要根据这个二级索引查询值
为18的数据，并加上共享锁，我们是只锁定18这一行就可以了吗？ 并不是，因为是非唯一索引，这个
结构中可能有多个18的存在，所以，在加锁时会继续往后找，找到一个不满足条件的值（当前案例中也
就是29）。此时会对18加临键锁，并对29之前的间隙加锁。
![演示图](./picture/%E9%97%B4%E9%9A%99%E9%94%81%26%E4%B8%B4%E9%94%AE%E9%94%81.png "间隙锁&临键锁")

C. 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。
查询的条件为id>=19，并添加共享锁。 此时我们可以根据数据库表中现有的数据，将数据分为三个部
分：
[19]
(19,25]
(25,+∞]
所以数据库数据在加锁是，就是将19加了行锁，25的临键锁（包含25及25之前的间隙），正无穷的临
键锁(正无穷及之前的间隙)。

## InnoDB引擎

逻辑存储结构
InnoDB的逻辑存储结构如下图所示:
![演示图](./picture/%E9%80%BB%E8%BE%91%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84.png  "逻辑存储结构")

1. 表空间
表空间是InnoDB存储引擎逻辑结构的最高层， 如果用户启用了参数 innodb_file_per_table(在
8.0版本中默认开启) ，则每张表都会有一个表空间（xxx.ibd），一个mysql实例可以对应多个表空
间，用于存储记录、索引等数据。
2. 段
段，分为数据段（Leaf node segment）、索引段（Non-leaf node segment）、回滚段
（Rollback segment），InnoDB是索引组织表，数据段就是B+树的叶子节点， 索引段即为B+树的
非叶子节点。段用来管理多个Extent（区）。
3. 区
区，表空间的单元结构，每个区的大小为1M。 默认情况下， InnoDB存储引擎页大小为16K， 即一
个区中一共有64个连续的页。
4. 页
页，是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性，
InnoDB 存储引擎每次从磁盘申请 4-5 个区。
5. 行
行，InnoDB 存储引擎数据是按行进行存放的。
在行中，默认有两个隐藏字段：
- Trx_id：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。
- Roll_pointer：每次对某条引记录进行改动时，都会把旧的版本写入到undo日志中，然后这个
隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息。

### 架构
MySQL5.5 版本开始，默认使用InnoDB存储引擎，它擅长事务处理，具有崩溃恢复特性，在日常开发
中使用非常广泛。下面是InnoDB架构图，左侧为内存结构，右侧为磁盘结构。

![演示图](./picture/InnoDB%E6%9E%B6%E6%9E%84%E5%9B%BE.png "InnoDB架构图")

#### 内存结构
![演示图](./picture/%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84.png "内存结构")
在左侧的内存结构中，主要分为这么四大块儿： Buffer Pool、Change Buffer、Adaptive
Hash Index、Log Buffer。 接下来介绍一下这四个部分。
1. Buffer Pool
InnoDB存储引擎基于磁盘文件存储，访问物理硬盘和在内存中进行访问，速度相差很大，为了尽可能
弥补这两者之间的I/O效率的差值，就需要把经常使用的数据加载到缓冲池中，避免每次访问都进行磁
盘I/O。
在InnoDB的缓冲池中不仅缓存了索引页和数据页，还包含了undo页、插入缓存、自适应哈希索引以及
InnoDB的锁信息等等。
缓冲池 Buffer Pool，是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增
删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），然后再以一定频
率刷新到磁盘，从而减少磁盘IO，加快处理速度。
缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型：
- free page：空闲page，未被使用。
- clean page：被使用page，数据没有被修改过。
- dirty page：脏页，被使用page，数据被修改过，也中数据与磁盘的数据产生了不一致。
在专用服务器上，通常将多达80％的物理内存分配给缓冲池 。参数设置： show variables
like 'innodb_buffer_pool_size';

2. Change Buffer
Change Buffer，更改缓冲区（针对于非唯一二级索引页），在执行DML语句时，如果这些数据Page
没有在Buffer Pool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区 Change Buffer
中，在未来数据被读取时，再将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。

二级索引的结构图：
![演示图](./picture/%E4%BA%8C%E7%BA%A7%E7%B4%A2%E5%BC%95%E7%BB%93%E6%9E%84.png "二级索引的结构图")

与聚集索引不同，二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新
可能会影响索引树中不相邻的二级索引页，如果每一次都操作磁盘，会造成大量的磁盘IO。有了
ChangeBuffer之后，我们可以在缓冲池中进行合并处理，减少磁盘IO。

3. Adaptive Hash Index
自适应hash索引，用于优化对Buffer Pool数据的查询。MySQL的innoDB引擎中虽然没有直接支持
hash索引，但是给我们提供了一个功能就是这个自适应hash索引。因为前面我们讲到过，hash索引在
进行等值匹配时，一般性能是要高于B+树的，因为hash索引一般只需要一次IO即可，而B+树，可能需
要几次匹配，所以hash索引的效率要高，但是hash索引又不适合做范围查询、模糊匹配等。
InnoDB存储引擎会监控对表上各索引页的查询，如果观察到在特定的条件下hash索引可以提升速度，
则建立hash索引，称之为自适应hash索引。
自适应哈希索引，无需人工干预，是系统根据情况自动完成。
参数： adaptive_hash_inde

4. Log Buffer
Log Buffer：日志缓冲区，用来保存要写入到磁盘中的log日志数据（redo log 、undo log），
默认大小为 16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新、插入或删除许多行的事
务，增加日志缓冲区的大小可以节省磁盘 I/O。
参数:
innodb_log_buffer_size：缓冲区大小
innodb_flush_log_at_trx_commit：日志刷新到磁盘时机，取值主要包含以下三个:
	1: 日志在每次事务提交时写入并刷新到磁盘，默认值。
	0: 每秒将日志写入并刷新到磁盘一次。
	2: 日志在每次事务提交后写入，并每秒刷新到磁盘一次。

#### 磁盘结构
接下来，再来看看InnoDB体系结构的右边部分，也就是磁盘结构：

![演示图](./picture/%E7%A3%81%E7%9B%98%E7%BB%93%E6%9E%84.png  "磁盘结构")

1. System Tablespace
系统表空间是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或通用表空间中创建
的，它也可能包含表和索引数据。(在MySQL5.x版本中还包含InnoDB数据字典、undolog等)
参数：innodb_data_file_path

系统表空间，默认的文件名叫 ibdata1。
2. File-Per-Table Tablespaces
如果开启了innodb_file_per_table开关 ，则每个表的文件表空间包含单个InnoDB表的数据和索
引 ，并存储在文件系统上的单个数据文件中。
开关参数：innodb_file_per_table ，该参数默认开启
那也就是说，我们没创建一个表，都会产生一个表空间文件
3. General Tablespaces
通用表空间，需要通过 CREATE TABLESPACE 语法创建通用表空间，在创建表时，可以指定该表空
间。
A. 创建表空间
```mysql
 CREATE TABLESPACE ts_name ADD DATAFILE 'file_name' ENGINE = engine_name;
```
B. 创建表时指定表空间
```mysql
CREATE TABLE xxx ... TABLESPACE ts_name;
```
4. Undo Tablespaces
撤销表空间，MySQL实例在初始化时会自动创建两个默认的undo表空间（初始大小16M），用于存储
undo log日志。
5. Temporary Tablespaces
InnoDB 使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据。
6. Doublewrite Buffer Files
1 CREATE TABLESPACE ts_name ADD DATAFILE 'file_name' ENGINE = engine_name;
1 CREATE TABLE xxx ... TABLESPACE ts_name;
双写缓冲区，innoDB引擎将数据页从Buffer Pool刷新到磁盘前，先将数据页写入双写缓冲区文件
中，便于系统异常时恢复数据。

7. Redo Log
重做日志，是用来实现事务的持久性。该日志文件由两部分组成：重做日志缓冲（redo log
buffer）以及重做日志文件（redo log）,前者是在内存中，后者在磁盘中。当事务提交之后会把所
有修改信息都会存到该日志中, 用于在刷新脏页到磁盘时,发生错误时, 进行数据恢复使用。
以循环方式写入重做日志文件，涉及两个文件：
![演示图](./picture/%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6.png  "日志文件")

前面我们介绍了InnoDB的内存结构，以及磁盘结构，那么内存中我们所更新的数据，又是如何到磁盘
中的呢？ 此时，就涉及到一组后台线程，接下来，就来介绍一些InnoDB中涉及到的后台线程。
![演示图](./picture/%E5%90%8E%E5%8F%B0%E7%BA%BF%E7%A8%8B.png "后台线程")

#### 后台线程
![演示图](./picture/%E5%90%8E%E5%8F%B0%E7%BA%BF%E7%A8%8B%E7%BC%93%E5%86%B2.png "后台线程缓冲")

在InnoDB的后台线程中，分为4类，分别是：Master Thread 、IO Thread、Purge Thread、
Page Cleaner Thread。

1. Master Thread
核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中, 保持数据的一致性，
还包括脏页的刷新、合并插入缓存、undo页的回收 。
2. IO Thread
在InnoDB存储引擎中大量使用了AIO来处理IO请求, 这样可以极大地提高数据库的性能，而IO
Thread主要负责这些IO请求的回调。

|线程类型|默认个数|职责|
|------| ------| ------|
|Read thread|4|负责读操作|
|Write thread|4|负责写操作|
|Log thread|1|负责将日志缓冲区刷新到磁盘|
Insert buffer thread|1|负责将写缓冲区内容刷新到磁盘|

我们可以通过以下的这条指令，查看到InnoDB的状态信息，其中就包含IO Thread信息。

```mysql
show engine innodb status \G;
```
3. Purge Thread
主要用于回收事务已经提交了的undo log，在事务提交之后，undo log可能不用了，就用它来回
收。
4. Page Cleaner Thread
协助 Master Thread 刷新脏页到磁盘的线程，它可以减轻 Master Thread 的工作压力，减少阻
塞。

### 事务原理

#### 事务基础

1. 事务
事务 是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系
统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。
2. 特性
- 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
- 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。
- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环
境下运行。
- 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。
那实际上，我们研究事务的原理，就是研究MySQL的InnoDB引擎是如何保证事务的这四大特性的。

而对于这四大特性，实际上分为两个部分。 其中的原子性、一致性、持久化，实际上是由InnoDB中的
两份日志来保证的，一份是redo log日志，一份是undo log日志。 而持久性是通过数据库的锁，
加上MVCC来保证的。

![演示图](./picture/%E4%BA%8B%E5%8A%A1%E5%8E%9F%E7%90%86.png "事务原理")
主要就是来研究一下redolog，undolog以及MVCC。

#### redo log
重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。
该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log
file）,前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中, 用
于在刷新脏页到磁盘,发生错误时, 进行数据恢复使用。

我们知道，在InnoDB引擎中的内存结构中，主要的内存区域就是缓冲池，在缓冲池中缓存了很多的数
据页。 当我们在一个事务中，执行多个增删改的操作时，InnoDB引擎会先操作缓冲池中的数据，如果
缓冲区没有对应的数据，会通过后台线程将磁盘中的数据加载出来，存放在缓冲区中，然后将缓冲池中
的数据修改，修改后的数据页我们称为脏页。 而脏页则会在一定的时机，通过后台线程刷新到磁盘
中，从而保证缓冲区与磁盘的数据一致。 而缓冲区的脏页数据并不是实时刷新的，而是一段时间之后
将缓冲区的数据刷新到磁盘中，假如刷新到磁盘的过程出错了，而提示给用户事务提交成功，而数据却
没有持久化下来，这就出现问题了，没有保证事务的持久性。
![演示图](./picture/%E4%BA%8B%E5%8A%A1%E5%8E%9F%E7%90%862.png "事务原理2")

那么，如何解决上述的问题呢？ 在InnoDB中提供了一份日志 redo log，接下来我们再来分析一
下，通过redolog如何解决这个问题。

![演示图](./picture/%E4%BA%8B%E5%8A%A1%E5%8E%9F%E7%90%863.png "事务原理3")

有了redolog之后，当对缓冲区的数据进行增删改之后，会首先将操作的数据页的变化，记录在redo
log buffer中。在事务提交时，会将redo log buffer中的数据刷新到redo log磁盘文件中。
过一段时间之后，如果刷新缓冲区的脏页到磁盘时，发生错误，此时就可以借助于redo log进行数据
恢复，这样就保证了事务的持久性。 而如果脏页成功刷新到磁盘 或 或者涉及到的数据已经落盘，此
时redolog就没有作用了，就可以删除了，所以存在的两个redolog文件是循环写的。

因为在业务操作中，我们操作数据一般都是随机读写磁盘的，而不是顺序读写磁盘。 而redo log在
往磁盘文件中写入数据，由于是日志文件，所以都是顺序写的。顺序写的效率，要远大于随机写。 这
种先写日志的方式，称之为 WAL（Write-Ahead Logging）。

#### undo log
回滚日志，用于记录数据被修改前的信息 , 作用包含两个 : 提供回滚(保证事务的原子性) 和
MVCC(多版本并发控制) 。

undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo
log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的
update记录。当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。

Undo log销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些
日志可能还用于MVCC。

Undo log存储：undo log采用段的方式进行管理和记录，存放在前面介绍的 rollback segment
回滚段中，内部包含1024个undo log segment

### MVCC

 #### 基本概念
 1. 当前读
读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加
锁。对于我们日常的操作，如：select ... lock in share mode(共享锁)，select ...
for update、update、insert、delete(排他锁)都是一种当前读。

2. 快照读
简单的select（不加锁）就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，
不加锁，是非阻塞读。
- Read Committed：每次select，都生成一个快照读。
- Repeatable Read：开启事务后第一个select语句才是快照读的地方。
- Serializable：快照读会退化为当前读。

3. MVCC
全称 Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本，
使得读写操作没有冲突，快照读为MySQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需
要依赖于数据库记录中的三个隐式字段、undo log日志、readView。
接下来，我们再来介绍一下InnoDB引擎的表中涉及到的隐藏字段 、undolog 以及 readview，从
而来介绍一下MVCC的原理。

