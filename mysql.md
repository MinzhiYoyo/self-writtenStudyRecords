## 基本命令

### 查询

#### 1、数据库基本操作

```mysql
# 打开数据库
mysql -u root -p  # 回车
mufeng # 密码

# 查询有哪些数据库
show databases; 
# 查询有哪些表
show tables;
### 创建表
create database mydb1;  //创建mydb1数据库，mydb1存在就会报错，下面mydb4存在只会w
create database mydb2 CHARACTER SET gbk; //以字符集为gbk来创键mydb2
create database mydb3 CHARACTER SET utf8; //以字符集为utf8来创建mydb3
create database if not exists mydb4; //如果mydb4不存在则创建mydb4，推荐使用
### 删除表
drop database mydb1;
drop database if exists mydb1; #推荐使用这种方式，原因同创建一样
### 返回当前所在表
select database(); 
### 查看数据库创建时的信息
SHOW CREATE DATABASE mydb1;
### 修改数据库
ALTER DATABASE mydb1 CHARACTER SET gbk;

### 任何select语句都会返回一张虚拟的表
```

---



#### 2、进入数据库

```mysql
use databasename;

### 一些细节
###         ``这个可以规避关键字
```

---



#### 3、查询内容

```	mysql
#####  查询表tablename中的label列
select label from tableName;
##### 查询表tablename中的所有列，效率慢，因为*先转换成所有的列再查询
select * from tableName;

#####  查询表tablename中的label1，label2……列
select label1,label2... from tableName;

#####  将查询结果重命名
select label1 as 'label1的别名', label2 as "label2的别名,只是显示方便" fromtabelName;

##### distinct是将label中的去重，好像只支持单个列查询
select distinct label from tableName;

##### 升序和降序排序, ASC升序，DESC降序
##### 先根据label2来升序，如果label2中有相同元素，就根据label1来降序
select label1, label2 from tableName order by label2 ASC;
select label1, label2 from tableName order by label2 ASC, label1 DESC;

##### 条件查询, 有and表示与，or表示或
##### 有 = >= <= > < != <>， !=,<>都表示不等于区别如下
##### !=: 
##### <>: 
select label1, label2 from tabelName where label1>100 and label1<1000 ;
select label1, label2 from tabelName where label1<=100 or label1>=1000 or label2=12;
##### not: 下面两种结果是不一样的
##### 第一个是，先对后面去整体然后再取反
##### 第二个是，先对not label1>100 去整体再和label1 < 1000 取与
select label1, label2 from tabelName where not (label1>100 and label1<1000) ;
select label1, label2 from tabelName where not label1>100 and label1<1000 ;
##### label1区间[100,1000]，且100<=1000
##### 如果1000和100互换位置，那么不会报错且没有任何结果，逻辑错误
select label1, label2 from tabelName where label1 between 100 and 1000 ;
##### IS NULL 和 IS NOT NULL，查询label1为NULL且 label2不为NULL的
select label1, label2 from tabelName where label1 IS NULL and label2 IS NOT NULL ;
##### 枚举查询,查询label1为100或者200或者300，IN的效率比较低下
select label1, label2 from tabelName where label1 IN (100,200,300);
##### 模糊查询 '_'代表单个任意字符 '%'代表0-N个的任意字符,不仅是字符串,数字也行
#####   注意：不是匹配 _ 或者 % 前面的，而是单独代表任意字符
#####   特殊字符查询请用转义字符\，如'\%'
select label1, label2 from tabelName where label1 like 'd__' or label2 like 'd%';
#### 分支结构
CASE
	WHEN 条件1 THEN 结果1
	WHEN 条件2 THEN 结果2
	ELSE 结果
END
#### 用法，注意from的位置 as还是取别名，同上
SELECT SALARY, EMPLOYEE_ID, FIRST_NAME, COMMISSION_PCT,
CASE
	WHEN EMPLOYEE_ID > 100 AND EMPLOYEE_ID < 140 THEN 'A'
	WHEN EMPLOYEE_ID = 140 THEN 'b'
	ELSE 'c'
END AS '等级'  
FROM t_employees;  



##### 时间查询，都是一些函数
select[时间函数([参数列表])];
##### 时间函数
SYSDATE()   # --->  当前系统时间（日，月，年，时，分，秒）
CURDATE()   # --->  获取到当前日期
CURTIME()   # --->  获取当前时间
WEEK(DATE)   # --->  获取指定日期为一年中的第几天
YEAR(DATE)   # --->  获取指定日期的年份
HOUR(TIME)   # --->  获取指定时间的小时
MINUTE(TIME)   # --->  获取指定时间的分钟
DATEDIFF(DATE1,DATE2)   # --->  获取DATE1 和 DATE2之间的天数
ADDDATE(DATE,N)   # --->  计算DATE加上N天后的日期
#### 举例
select SYSDATE() AS '日期 时间', CURDATE() AS '时间';
select WEEK(SYSDATE()) AS '', HOUR('12:16:23') AS '小时';

##### 字符串查询,字符串的下标从1开始，不是从0开始，中文好像也算一个
select 字符串函数名([函数列表]);
CONCAT(str1,str2,str3...)   # --->  将多个字符串拼接，数字也可以拼接哦
INSERT(str,pos.len,newStr)   # --->  将str中指定pos位置开始冷长度的内容替换为newStr
LOWER(str)   # --->  将指定字符串转换为小写
UPPER(str)   # --->  将指定字符串转换为大写
SUSELECT department_id, AVG(SALARY) AS '薪资平均值'
FROM t_employees
GROUP BY department_id;BSTRING(str,num,len)   # --->  将str字符串指定num位置开始截取len个内容

##### 聚合函数，对数据统计
select 字符串函数名([函数列表]);
SUM()   # --->  返回和
AVG()   # --->  返回平均值
MAX()   # --->  返回最大值
MIN()   # --->  返回最小值
COUNT()   # --->  返回行数，会自动忽略NULL
###举例
SELECT SUM(EMPLOYEE_ID) FROM t_employees;

##### 分组查询
##### 解释：从表t_employees中查询department_id和 SALARY的平均值是department_id相同计算的
#### 先对department_id，分组，再对depatr2分组
SELECT department_id, AVG(SALARY) AS '薪资平均值'
FROM t_employees
GROUP BY department_id, depatr2;
##### 再过滤
SELECT department_id, AVG(SALARY) AS '薪资平均值'
FROM t_employees
GROUP BY department_id
HAVING department_id IN (60,70,80,90);
##### 限制查询,行的下标从0开始，下面中的0为下标，5为行数
select * from t_employees limit 0,5;

##### 子查询，先算括号里面的，然后将结果带给>出进行外面这一部分的查询
SELECT * FROM t_employees WHERE SALARY > ( SELECT salary FROM t_employees WHERE FIRST_NAME = 'Bruce' ) ;  #1行1列的
SELECT * FROM t_employees WHERE SALARY IN ( SELECT salary FROM t_employees WHERE FIRST_NAME = 'Bruce' ) ;  #多行1列的
##### 关键字 ALL ANY，感觉类似与最大值，最小值
SELECT * FROM t_employees WHERE SALARY > ALL(SELECT SALARY FROM T_EMPLOYEES WHERE DEPARTMENT_ID = 60); ## 高于所有的
SELECT * FROM t_employees WHERE SALARY > ANY(SELECT SALARY FROM T_EMPLOYEES WHERE DEPARTMENT_ID = 60); ## 高于部分
#### 多行多列的，注意是from在后面,temp只是临时表的名字，括号里面的就是临时表了，不能省略
SELECT * FROM (SELECT EMPLOYEE_ID, FIRST_NAME, SALARY FROM T_EMPLOYEES ORDER BY SALARY DESC) AS temp LIMIT 0,5;

#### 合并查询,列数必须相同，类型不一定相同 
SELECT * FROM 表名1 UNION SELECT * FROM 表名2;  ### 去除重复
SELECT * FROM 表名1 UNION ALL SELECT * FROM 表名2; ### 保留重复记录

### 内连接查询 INNER JOIN NO
SELECT * FROM T_EMPLOYEES  ## SQL 标准
INNER JOIN T_JOBS     ## 联合其他的表名
ON T_EMPLOYEES.`JOB_ID` = T_JOBS.`JOB_ID`;  # 条件
SELECT * FROM T_EMPLOYEES,T_JOBS WHERE T_EMPLOYEES.`JOB_ID` = T_JOBS.`JOB_ID`; # MySQL 标准，其他数据库是不兼容的

### 左外连接，右外连接查询: 关键字 内连接查询的INNER 编程 LEFT 或者 RIGHT
LEFT #左表为主表，有就返回结果，没有就用NULL填充
RIGHT #右表为主表，有就返回结果，没有就用NULL填充

```

```mysql
####          总结                #########
### 1、书写顺序
select 列名 FROM 表名 WHERE 条件 GROUP BY 分组 HAVING 过滤条件 ORDER BY 排序 LIMIT 起始行，结束行
### 2、执行顺序
FROM 			#指定数据来源表
where			#对查询数据做第一次过滤
GROUP BY		#分组
HAVING			#对分组后的数据做第二次过滤
SELECT			#查询各字段的值
ORDER BY		#排序
LIMIT			#限定查询结果
```



---



#### 4、增删改操作

```mysql
### 新增表数据，记住，是表的数据，不是新增表
### 列1，列2，列3……，表名都是已经存在了的，不然会报错，且只能由一个VALUES，不能创建多条
### 且列数不一定等于表名里的列数，空的自动补充NULL，（列1，列2……）这个括号这里都可以省略
INSERT INTO 表名(列1, 列2, 列3,……)VALUES(值1, 值2, 值3……);

### 修改语句，加条件是防止把整张表都修改了，只修改满足WHERE的条件的表
UPDATE 表名 SET 列1=新值1, 列2=新值2, ... WHERE 条件;
UPDATE 表名 SET money = money - 1000 WHERE id=1;
## SQL条件判断中没有 值a=NULL 这一写法，只有 值a IS NULL / A IS NOT NULL
## 这里赋值就可以 set 列1=NULL

### 删除条件
DELETE FROM 表名 WHERE 条件;

### 清空整张表
# 清空推荐使用这个，对表做删除操作，delete只对数据进行删除，这个会生成一张新表，按照原表结构
TRUNCATE TABLE 表名;
```

---



#### 5、数据类型

##### 1）数值类型

|     类型     |          大小(字节)           |                范围（有符号）                 |      范围（无符号）       |      用途      |
| :----------: | :---------------------------: | :-------------------------------------------: | :-----------------------: | :------------: |
|     INT      |               4               |          （-2147483648，2147483647）          |     （0，4294967295）     |    大整数值    |
|    DOUBLE    |               8               |           (-1.797E+308,-2.22E-308)            | (0,2.22E-308,11.797E+308) | 双精度浮点数值 |
| DOUBLE(M,D)  | 8，（M代表长度，D代表小数位） | 同上，受M,D的约束DOUBLE(5,2)-999.999,+999.999 |    同上，受M，D的约束     | 双精度浮点数值 |
| DECIMAL(M,D) |         DECIMAL(M,D)          |           依赖于M,D的值，M最大值65            |                           |     小数值     |

---



##### 2）日期类型

|     类型     | 大小 |                             范围                             |        格式         |           用途           |
| :----------: | :--: | :----------------------------------------------------------: | :-----------------: | :----------------------: |
|   **DATE**   |  3   |                    1000-01-01/9999-12-31                     |     YYYY-MM-DD      |          日期值          |
|     TIME     |  3   |                   '-838:59:59'/'838:59:59'                   |      HH:MM:SS       |     时间值或持续时间     |
|     YEAR     |  1   |                          1901/2155                           |        YYYY         |          年份值          |
| **DATETIME** |  8   |           1000-01-01 00:00:00/9999-12-31-23:59:59            | YYYY-MM-DD HH:MM:SS |     混合日期和时间值     |
|  TIMESTAMP   |  4   | 1970-01-01 00:00:00/2038结束时间是第2147483647秒，北京时间2038-1-19 11:14:07 |   YYYYMMDDHHMMSS    | 混合日期和时间值，时间戳 |

---



##### 3）字符串

|  类型   |  大小   |                用途                |
| :-----: | :-----: | :--------------------------------: |
|  CHAR   |  0-255  |    定长字符串插入（10）10个字符    |
| VARCHAR | 0-65535 | 变长字符拆给你varchar(10) 10个字符 |
|  BLOB   | 0-65535 |        二进制形式的文本数据        |
|  TEXT   | 0-65536 |             长文本数据             |

---



### 6、对表进行操作

```mysql
########  建表
CREATE TABLE 表名(
    列名1 数据类型 [约束]
    列名2 数据类型 [约束]
)[CHARSET=utf8];

#######  删表
DROP TABLE 表名;
```

---



### 7、数据约束

##### --->  实体完整性约束

```mysql
## 1、主键约束
### PRIMARY KEY 唯一，标识表中的一行数据，此列的值不可重复，且不能为NULL
CREATE TABLE 表名(
    列名1 数据类型 PRIMARY KEY,
    列名2 数据类型 [约束]
)[CHARSET=utf8];
### 那么在这张表中，列名1就不能出现重复的

## 2、唯一约束
### UNIQUE 唯一，标识表中的一行数据，不可重复，可以为NULL
CREATE TABLE 表名(
    列名1 数据类型 PRIMARY KEY,
    列名2 数据类型 UNIQUE
)[CHARSET=utf8];

## 3、自动增长列，只能配合主键约束，一般是用于数字类型的数据
####AUTO_INCREMENT 自动增长，给主键数值列添加自动增长，从1开始，每次加1.不能单独使用，和主键配合
#### 所以使用INSERT增加数据可以不必要为列名1手动写了
CREATE TABLE 表名(
    列名1 数据类型 PRIMARY KEY AUTO_INCREMENT, ##这里会从1开始，自动增长
    列名2 数据类型 UNIQUE
)[CHARSET=utf8];
```

##### --->域完整性约束

```mysql
## 1、NOT NULL 非空，此列必须有值
CREATE TABLE 表名(
    列名1 数据类型 PRIMARY KEY,
    列名2 数据类型 UNIQUE NOT NULL #那么列2就必须有值了
)[CHARSET=utf8];

## 2、default值 为列赋予默认值，当新增数据不指定值，书写DEFAULT，以指定的默认值进行填充
CREATE TABLE 表名(
    列名1 数据类型 DEFAULT 20, ##如果没有赋值，就会默认是20
    列名2 数据类型 [约束]
)[CHARSET=utf8];
INSERT INTO 表名(列名1,列名2)VALUES(DEFAULT,'LMZ'); # 因为列名1可以用DEFAULT赋值

## 3、引用完整性约束
#### CONSTRAINT 引用名 FOREIGN KEY（列名） REFERENCES 被引用表名(列名)
#### 详解：新建 列名 时，列名必须时 被引用表名 中 的列名已经存在的值
#### 引用名命名规则：fk_此表名_列名
```

##### --->综合练习

```mysql
CREATE TABLE Grade(  #创立年级表
	GradeID INT PRIMARY KEY AUTO_INCREMENT,
	GradeName VARCHAR(20) UNIQUE NOT NULL
)CHARSET=UTF8;
INSERT INTO GRADE(GRADENAME) VALUES('电科七班');
INSERT INTO GRADE(GRADENAME) VALUES('电科一班');

CREATE TABLE Student(
	Student_ID VARCHAR(20) PRIMARY KEY,
	Student_Name VARCHAR(50) NOT NULL,
	sex CHAR(1) DEFAULT '密',
	BornDate DATE NOT NULL,
	Phone VARCHAR(11),
	GradeId INT NOT NULL,
	CONSTRAINT fk_Student_GradeId FOREIGN KEY(GradeId) REFERENCES Grade(GradeID)
)CHARSET=UTF8;
DROP TABLE Student;
INSERT INTO Student(Student_ID,Student_Name,sex,BornDate,Phone,GradeId)
VALUES('B19020713','梁敏智',DEFAULT,'2001-11-03','18692860733',3);
SELECT * FROM Student;
```

---



###  8、事务的概念及应用

##### -->概念

```mysql
事务是将一条或者多条SQL语句封装在一起，所有SQL语句被成功执行，那么整个事务成功，有一条SQL语句执行失败，则事务失败。

显示提交：commit;
显示回滚：roolback;

##事务的特性：ACID --> 原子性、一致性、隔离性、持久性。

## 用法：举例，用转账来模拟，id为1的向id为2的转1000快钱
START TRANSACTION; #开启事务
UPDATE account SET money = money - 1000 WHERE id = 1; #id=1的money-1000
UPDATE account SET money = money + 1000 WHERE id = 2; #id=2的money+1000
COMMIT;   #如果没出错就提交，手动
ROLLBACK; #如果出错了，就能回滚，回到TRANSACTION之前状态，手动
```

---



### 9、用户管理

```mysql
# 创建用户
CREATE USER `zhangsan` IDENTIFIED BY '123'; #创建用户ZHANGSAN，密码为 123

# 授权
GRANT ALL ON 数据库.表 TO 用户名;
# 举例
GRANT ALL ON mydata.t1 TO `zhangsan`;

# 撤销权限
REVOKE ALL ON 数据库.表 FROM 用户名; # 而且需要用户重新连接才能生效

# 删除用户
DROP USER 用户名;
```

