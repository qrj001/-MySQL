# MySQL学习笔记

## 登录MySQL数据库服务器
```mysql
mysql -uroot -p12345612
```

## 退出MySQL数据库服务器
```mysql
exit;
```

## 基本语法-增删改查

```mysql
-- 显示所有数据库
SHOW DATABASES;

-- 创建数据库
CREATE DATABASE test;

--选中数据库
USE test;

-- 显示某个数据库中的所有表
SHOW TABLES;

-- 创建数据表
CREATE TABLE pet (
    name VARCHAR(20),
    owner VARCHAR(20),
    species VARCHAR(20),
    sex CHAR(1),
    birth DATE,
    death DATE
);

-- 查看数据表结构
DESCRIBE pet;
DESC pet;

-- 查询表
SELECT * from pet;

-- 插入数据
INSERT INTO pet VALUES ('puffball', 'Diane', 'hamster', 'f', '1990-03-30', NULL);

-- 修改数据
UPDATE pet SET name = 'squirrel' where owner = 'Diane';

-- 删除数据
DELETE FROM pet where name = 'squirrel';

-- 删除表
DROP TABLE myorder;
```

## SQL数据类型
### 数据类型大致分为三类：字符串， 数值， 时期/时间
```
--- 字符串 （按字符串大小选择类型）
纯英文和数字，用char/varchar,英文字符占一个字节，中文字符占两个字节.
含有中文字符，用nchar/nvarchar,中英文字符都占取两个字节。

char(size)	固定长度的字符串,最多 8,000 个字符。输入的字符小于size时，后面补空值。当输入的字符大于size时，截取超出字符。
varchar(size)	可变长度的字符串，存储大小为输入数据的字节的实际长度，最多 8,000 个字符。
nchar(n)	固定长度的 Unicode 数据。最多 4,000 个字符。
nvarchar(n)	可变长度的 Unicode 数据。最多 4,000 个字符。

--- 数值 （按照数值大小选择类型）
INT(size)	size规定最大位数。
FLOAT(size,d)	带有浮动小数点的小数字。size规定最大位数。d规定小数点右侧的最大位数。
DOUBLE(size,d)	带有浮动小数点的大数字。size规定最大位数。d规定小数点右侧的最大位数。

--- 日期/时间（按格式选择类型）
DATE() 日期格式：YYYY-MM-DD
DATETIME()	日期和时间的组合。格式：YYYY-MM-DD HH:MM:SS

```


## 建表约束

### 主键约束 

#### 主键约束 -  PRIMARY KEY - 字段不重复且不为空，保证表内所有数据的唯一性。
```mysql 
CREATE TABLE user (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);
# id 为主键，id不能重复且不为NULL

或
CREATE TABLE user (
    id INT ,
    name VARCHAR(20)
    PRIMARY KEY（id）
);
```
####  联合主键 - PRIMARY KEY（列1，列2，...）
####  联合主键中的每列的值都不能为空，并且加起来不重复。可有多个列
```mysql
CREATE TABLE user4 (
    id INT,
    name VARCHAR(20),
    password VARCHAR(20),
    PRIMARY KEY(id, name)
);

+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int         | NO   | PRI | NULL    |       |
| name     | varchar(20) | NO   | PRI | NULL    |       |
| password | varchar(20) | NO   |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
```
#### 自增约束 - AUTO_INCREMENT
#### 自增约束的主键由系统自动递增生成。必须是主键才能自增

```mysql
CREATE TABLE user (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
);
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int         | NO   | PRI | NULL    | auto_increment |
| name  | varchar(20) | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+

insert into user(name) values('zs');
insert into user(name) values('ab');
insert into user(name) values('ab');
+----+------+
| id | name |
+----+------+
|  1 | zs   |
|  2 | ab   |
|  3 | ab   |
+----+------+
```
#### 添加主键约束 - ALTER ... MODIFY/ADD
```mysql
-- 如果忘记设置主键：
ALTER TABLE user MODIFY id INT PRIMARY KEY;
ALTER TABLE user ADD PRIMARY KEY(id,name);
```
#### 删除主键 - ALTER TABLE ...DROP PRIMARY KEY
```mysql
ALTER TABLE user DROP PRIMARY KEY;
```

### 唯一主键 - UNIQUE - 不能重复（可有NULL，但只能有一个NULL）

```mysql
CREATE TABLE user (
    id INT,
    name VARCHAR(20),
    UNIQUE(name)
); #name不能重复，但能有且仅有1个NULL

UNIQUE(name，id) #两个键的组合在一起不重复
```
#### 添加唯一主键 - ALTER TABLE ... ADD/MODIFY 
```mysql
ALTER TABLE user ADD UNIQUE(name);
ALTER TABLE user MODIFY name VARCHAR(20) UNIQUE;
```
#### 删除唯一主键 - ALTER TABLE ... DROP INDEX ...
```mysql
ALTER TABLE user DROP INDEX name;
```

### 非空约束 - NOT NULL - 约束某个字段不能为空

```mysql
CREATE TABLE user (
    id INT,
    name VARCHAR(20) NOT NULL
);
```
#### 添加/删除非空约束
```mysql
ALTER TABLE user MODIFY name VARCHAR(20) NOT NULL;

ALTER TABLE user MODIFY name VARCHAR(20);
```

### 默认约束 - DEFAULT - 当插入某字段值的时候，如果没有传值，使用默认值

```mysql
CREATE TABLE user(
    id INT,
    name VARCHAR(20),
    age INT DEFAULT 10
);
```

#### 删除默认约束
```mysql
ALTER TABLE user MODIFY age INT;
```

### 外键约束 - FOREIGN KEY

#### 副表中的FOREIGH KEY 指向主表中的PRIMARY KEY，在添加/删除数据时有限制：
1. 主表中没有的数据值，副表不可以使用；
2. 主表中的记录被副表引用时，主表不可以被删除。

```mysql
-- 班级表（主表）
CREATE TABLE classes (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

-- 学生表 （副表）
CREATE TABLE students (
    id INT PRIMARY KEY,
    name VARCHAR(20),
    class_id INT, # 与classes 中的 id 字段相关联
    FOREIGN KEY(class_id) REFERENCES classes(id) #class_id 的值必须来自于 classes表中的 id 字段值
);

```

## 数据库的三大设计范式

### 1NF

数据表中的所有字段都是不可分割的原子值。eg. 地址-“中国｜广东省｜深圳市｜南山区｜粤海大道｜100号｜”可拆分。

范式设计得越详细，便于某些实际操作（eg.统计），但并非都有好处，需要对实际项目的开发情况进行设定。

### 2NF

在满足第一范式的前提下，其 他列都必须完全依赖于主键列。如果出现部分依赖，只可能发生在联合主键的情况下：

```mysql
-- 订单表
CREATE TABLE myorder (
    product_id INT,
    customer_id INT,
    product_name VARCHAR(20),
    customer_name VARCHAR(20),
    PRIMARY KEY (product_id, customer_id)
);
```
问题：`product_name` 只依赖于 `product_id` ，`customer_name` 只依赖于 `customer_id` 。也就是说，除主键外的其他列，只依赖于主键的部分字段，不满足第二范式。

解决方法：拆表

```mysql
CREATE TABLE myorder (
    order_id INT PRIMARY KEY,
    product_id INT,
    customer_id INT
);

CREATE TABLE product (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

CREATE TABLE customer (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

```
拆分之后，`myorder` 表中的 `product_id` 和 `customer_id` 完全依赖于 `order_id` 主键，而 `product` 和 `customer` 表中的其他字段又完全依赖于主键。满足了第二范式的设计！

### 3NF

在满足第二范式的前提下，除了主键列之外，其他列之间不能有传递依赖关系。

```mysql
CREATE TABLE myorder (
    order_id INT PRIMARY KEY,
    product_id INT,
    customer_id INT,
    customer_phone VARCHAR(15)
);
```

问题：表中的 `customer_phone` 依赖于非主键列`customer_id` ，不满足第三范式。

解决方法：拆表

```mysql
CREATE TABLE myorder (
    order_id INT PRIMARY KEY,
    product_id INT,
    customer_id INT
);

CREATE TABLE customer (
    id INT PRIMARY KEY,
    name VARCHAR(20),
    phone VARCHAR(15)
);
```

修改后就不存在其他列之间的传递依赖关系，其他列都只依赖于主键列，满足了第三范式的设计！

## 查询练习

```mysql
准备数据

-- 创建数据库
CREATE DATABASE select_test;
-- 切换数据库
USE select_test;

-- 创建学生表
CREATE TABLE student (
    no VARCHAR(20) PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    sex VARCHAR(10) NOT NULL,
    birthday DATE, -- 生日
    class VARCHAR(20) -- 所在班级
);

-- 创建教师表
CREATE TABLE teacher (
    no VARCHAR(20) PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    sex VARCHAR(10) NOT NULL,
    birthday DATE,
    profession VARCHAR(20) NOT NULL, -- 职称
    department VARCHAR(20) NOT NULL -- 部门
);

-- 创建课程表
CREATE TABLE course (
    no VARCHAR(20) PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    t_no VARCHAR(20) NOT NULL, # 教师编号
    FOREIGN KEY(t_no) REFERENCES teacher(no) #tno 来自于 teacher 表中的 no 字段值
);

-- 成绩表
CREATE TABLE score (
    s_no VARCHAR(20) NOT NULL, -- 学生编号
    c_no VARCHAR(20) NOT NULL, -- 课程号
    degree DECIMAL,	-- 成绩
    FOREIGN KEY(s_no) REFERENCES student(no),	
    FOREIGN KEY(c_no) REFERENCES course(no),
    PRIMARY KEY(s_no, c_no)
);

-- 查看所有表
SHOW TABLES;

-- 添加学生表数据
INSERT INTO student VALUES('101', '曾华', '男', '1977-09-01', '95033');
INSERT INTO student VALUES('102', '匡明', '男', '1975-10-02', '95031');
INSERT INTO student VALUES('103', '王丽', '女', '1976-01-23', '95033');
INSERT INTO student VALUES('104', '李军', '男', '1976-02-20', '95033');
INSERT INTO student VALUES('105', '王芳', '女', '1975-02-10', '95031');
INSERT INTO student VALUES('106', '陆军', '男', '1974-06-03', '95031');
INSERT INTO student VALUES('107', '王尼玛', '男', '1976-02-20', '95033');
INSERT INTO student VALUES('108', '张全蛋', '男', '1975-02-10', '95031');
INSERT INTO student VALUES('109', '赵铁柱', '男', '1974-06-03', '95031');

-- 添加教师表数据
INSERT INTO teacher VALUES('804', '李诚', '男', '1958-12-02', '副教授', '计算机系');
INSERT INTO teacher VALUES('856', '张旭', '男', '1969-03-12', '讲师', '电子工程系');
INSERT INTO teacher VALUES('825', '王萍', '女', '1972-05-05', '助教', '计算机系');
INSERT INTO teacher VALUES('831', '刘冰', '女', '1977-08-14', '助教', '电子工程系');

-- 添加课程表数据
INSERT INTO course VALUES('3-105', '计算机导论', '825');
INSERT INTO course VALUES('3-245', '操作系统', '804');
INSERT INTO course VALUES('6-166', '数字电路', '856');
INSERT INTO course VALUES('9-888', '高等数学', '831');

-- 添加添加成绩表数据
INSERT INTO score VALUES('103', '3-105', '92');
INSERT INTO score VALUES('103', '3-245', '86');
INSERT INTO score VALUES('103', '6-166', '85');
INSERT INTO score VALUES('105', '3-105', '88');
INSERT INTO score VALUES('105', '3-245', '75');
INSERT INTO score VALUES('105', '6-166', '79');
INSERT INTO score VALUES('109', '3-105', '76');
INSERT INTO score VALUES('109', '3-245', '68');
INSERT INTO score VALUES('109', '6-166', '81');

-- 查看表结构
SELECT * FROM course;
SELECT * FROM score;
SELECT * FROM student;
SELECT * FROM teacher;
```

### 增删改查练习

1. 查询 score 表中degree在60-80之间的所有行
   -- BETWEEN xx AND xx: 查询区间
```mysql
SELECT * FROM score WHERE degree BETWEEN 60 AND 80;
```
2.  查询 score 表中degree为 85, 86 或 88 的行
    -- IN: 表示“或者”关系的查询
```mysql
SELECT * FROM score WHERE degree IN (85, 86, 88);
```
3.  查询 "95031" 班的学生人数
    -- COUNT: 统计。 若count(列1)，不算NULL值。所以一般用count(*)。
```mysql
SELECT COUNT(*) FROM student WHERE class = '95031';
```
4. 查询 score 表中的最高分的学生学号和课程编号。
  
```
子查询: (SELECT MAX(degree) FROM score) = 算出最高分
SELECT s_no, c_no FROM score WHERE degree = (SELECT MAX(degree) FROM score);
```

```mysql
排序查询: order by ... limit r,n = 排序后从第r行开始，查询n条数据  = limit n offset r
SELECT s_no, c_no, degree FROM score ORDER BY degree DESC LIMIT 0, 1;
```

5.查询 `student` 表中每个学生的姓名和年龄。

```mysql
-- 年龄 = 当前年份-出生年份 = YEAR(NOW()) - YEAR(birthday)
SELECT name, YEAR(NOW()) - YEAR(birthday) as age FROM student;
+-----------+------+
| name      | age  |
+-----------+------+
| 曾华      |   42 |
| 匡明      |   44 |
| 王丽      |   43 |
| 李军      |   43 |
| 王芳      |   44 |
| 陆军      |   45 |
| 王尼玛    |   43 |
| 张全蛋    |   44 |
| 赵铁柱    |   45 |
| 张飞      |   45 |
+-----------+------+
```


### 分组查询练习
- GROUP BY语句用来与聚合函数(aggregate functions such as COUNT, SUM, AVG, MIN, or MAX.)联合使用来得到一个或多个列的结果集。
- HAVING语句通常与GROUP BY语句联合使用，用来过滤由GROUP BY语句返回的记录集。HAVING语句的存在弥补了WHERE关键字不能与聚合函数联合使用的不足。

1.查询每门课的平均成绩。

```mysql
SELECT c_no, AVG(degree) FROM score GROUP BY c_no;
```

2.查询 `score` 表中至少有 2 名学生选修，并以 3 开头的课程的平均分数。

```mysql
-- 查询出至少有 2 名学生选修的课程
-- HAVING: 表示持有
HAVING COUNT(c_no) >= 2

-- 查询以 3 开头的课程
-- LIKE 表示模糊查询，"%" 是一个通配符，匹配 "3" 后面的任意字符。
-- NOT LIKE 模糊查询取反
AND c_no LIKE '3%';

-- 后面加上一个 COUNT(*)，表示将每个分组的个数也查询出来。
SELECT c_no, AVG(degree), COUNT(*) FROM score GROUP BY c_no
HAVING COUNT(c_no) >= 2 AND c_no LIKE '3%';
+-------+-------------+----------+
| c_no  | AVG(degree) | COUNT(*) |
+-------+-------------+----------+
| 3-105 |     85.3333 |        3 |
| 3-245 |     76.3333 |        3 |
+-------+-------------+----------+
```

### 多表查询

1. 查询所有学生的 `name`，以及该学生在 `score` 表中对应的 `c_no` 和 `degree` 

```mysql
name -> student
c_no,degree -> score
通过primary key 关联两张表

SELECT name, c_no, degree FROM student, score 
WHERE student.no = score.s_no;
```

2. 查询所有学生的 `name` 、课程名称 ( `course` 表中的 `name` ) 和成绩 ( `score` 表中的 `degree` ) 列。

```mysql
三表关联方法：用and，分别关联2张表

score: s_no, c_no, degree 
course: no, name
student: no,name,sex,birthday,class

-- 由于字段名存在重复，使用 "表名.字段名 as 别名" 代替。
SELECT student.name as s_name, course.name as c_name, degree 
FROM student, score, course
WHERE student.no = score.s_no
AND score.c_no = course.no;
```

### 子查询

1. 查询 `95031` 班学生每门课程的平均成绩。

```mysql
-- IN (..): 将筛选出的学生号当做 s_no 的条件查询
SELECT s_no, c_no, degree FROM score
WHERE s_no IN (SELECT no FROM student WHERE class = '95031');
+------+-------+--------+
| s_no | c_no  | degree |
+------+-------+--------+
| 105  | 3-105 |     88 |
| 105  | 3-245 |     75 |
| 105  | 6-166 |     79 |
| 109  | 3-105 |     76 |
| 109  | 3-245 |     68 |
| 109  | 6-166 |     81 |
+------+-------+--------+

这时只要将 `c_no` 分组一下就能得出 `95031` 班学生每门课的平均成绩：

SELECT c_no, AVG(degree) FROM score
WHERE s_no IN (SELECT no FROM student WHERE class = '95031')
GROUP BY c_no;
+-------+-------------+
| c_no  | AVG(degree) |
+-------+-------------+
| 3-105 |     82.0000 |
| 3-245 |     71.5000 |
| 6-166 |     80.0000 |
+-------+-------------+
```

2. 查询在 `3-105` 课程中，比 `109` 号同学分数高的同学。

```mysql
首先找到 3-105，109号同学的分数。
SELECT degree FROM score WHERE s_no = '109' AND c_no = '3-105'

再筛选出课堂号为 `3-105` ，在找出所有成绩高于 `109` 号同学的的行。
SELECT * FROM score 
WHERE c_no = '3-105'
AND degree > (SELECT degree FROM score WHERE s_no = '109' AND c_no = '3-105');
```

3. 查询所有和 `101` 、`108` 号学生同年出生的 `no` 、`name` 、`birthday` 列。

```mysql
-- YEAR(..): 取出日期中的年份
首先找到101，108号同学出生的年份
SELECT YEAR(birthday) FROM student WHERE no IN (101, 108)
找到对应的同学
SELECT * FROM student
WHERE YEAR(birthday) IN (SELECT YEAR(birthday) FROM student WHERE no IN (101, 108));
```

4. 查询 `'张旭'` 教师任课的学生成绩表。

```mysql
首先找到教师编号：
SELECT NO FROM teacher WHERE NAME = '张旭'

通过 `sourse` 表找到该教师课程号：
select * from course where t_no = (select no from teacher where name = '张旭');

通过筛选出的课程号查询成绩表：
SELECT * FROM score WHERE c_no = (
    SELECT no FROM course WHERE t_no = ( 
        SELECT no FROM teacher WHERE NAME = '张旭' 
    )
);
```

5. 查询某课程多于5个同学的教师姓名。

```mysql
插入新数据：
INSERT INTO score VALUES('101', '3-105', '90');
INSERT INTO score VALUES('102', '3-105', '91');
INSERT INTO score VALUES('104', '3-105', '89');

首先查询各门课的学生人数：
select c_no, count(*) from score group by c_no;
+-------+----------+
| c_no  | count(*) |
+-------+----------+
| 3-105 |        6 |
| 3-245 |        3 |
| 6-166 |        3 |
+-------+----------+

选出超过5人的课程号：
select c_no from score group by c_no having count(*)>5;
+-------+
| c_no  |
+-------+
| 3-105 |
+-------+

选出（超过5人的课程号）对应的t_no
select t_no from course where no = (select c_no from score group by c_no having count(*)>5);
+------+
| t_no |
+------+
| 825  |
+------+

选出（超过5人的课程号）对应的t_no 对应的姓名
select name from teacher where no = (select t_no from course where no = (select c_no from score group by c_no having count(*)>5));
+--------+
| name   |
+--------+
| 王萍   |
+--------+
```

### 交并集的使用

1. 排除同一职称下 `计算机系` 与 `电子工程系` 的教师。
+-----+--------+-----+------------+------------+-----------------+
| no  | name   | sex | birthday   | profession | department      |
+-----+--------+-----+------------+------------+-----------------+
| 804 | 李诚   | 男  | 1958-12-02 | 副教授     | 计算机系        |
| 825 | 王萍   | 女  | 1972-05-05 | 助教       | 计算机系        |
| 831 | 刘冰   | 女  | 1977-08-14 | 助教       | 电子工程系      |
| 856 | 张旭   | 男  | 1969-03-12 | 讲师       | 电子工程系      |
+-----+--------+-----+------------+------------+-----------------+
结果输出 （李诚，张旭）

```mysql

SELECT * FROM teacher WHERE department = '计算机系' AND profession NOT IN (
    SELECT profession FROM teacher WHERE department = '电子工程系'
)
UNION
SELECT * FROM teacher WHERE department = '电子工程系' AND profession NOT IN (
    SELECT profession FROM teacher WHERE department = '计算机系'
);
```


### 复制表的数据作为条件查询

1. 查询某课程成绩比该课程平均成绩低的 `score` 表

```mysql
-- 查询平均分
SELECT c_no, AVG(degree) FROM score GROUP BY c_no;
+-------+-------------+
| c_no  | AVG(degree) |
+-------+-------------+
| 3-105 |     87.6667 |
| 3-245 |     76.3333 |
| 6-166 |     81.6667 |
+-------+-------------+


-- 将表 b 作用于表 a 中查询数据
-- score a (b): 将表声明为 a (b)，
-- 如此就能用 a.c_no = b.c_no 作为条件执行查询了。
SELECT * FROM score a WHERE degree < (
    (SELECT AVG(degree) FROM score b WHERE a.c_no = b.c_no)
);
+------+-------+--------+
| s_no | c_no  | degree |
+------+-------+--------+
| 105  | 3-245 |     75 |
| 105  | 6-166 |     79 |
| 109  | 3-105 |     76 |
| 109  | 3-245 |     68 |
| 109  | 6-166 |     81 |
+------+-------+--------+
```


### 按等级查询

建立一个 `grade` 表代表学生的成绩等级，并插入数据：

```mysql
CREATE TABLE grade (
    low INT(3),
    upp INT(3),
    grade char(1)
);

INSERT INTO grade VALUES (90, 100, 'A');
INSERT INTO grade VALUES (80, 89, 'B');
INSERT INTO grade VALUES (70, 79, 'C');
INSERT INTO grade VALUES (60, 69, 'D');
INSERT INTO grade VALUES (0, 59, 'E');

SELECT * FROM grade;
+------+------+-------+
| low  | upp  | grade |
+------+------+-------+
|   90 |  100 | A     |
|   80 |   89 | B     |
|   70 |   79 | C     |
|   60 |   69 | D     |
|    0 |   59 | E     |
+------+------+-------+
```

**查询所有学生的 `s_no` 、`c_no` 和 `grade` 列。**

思路是，使用区间 ( `BETWEEN` ) 查询，判断学生的成绩 ( `degree` )  在 `grade` 表的 `low` 和 `upp` 之间。

```mysql
SELECT s_no, c_no, grade FROM score, grade 
WHERE degree BETWEEN low AND upp;
+------+-------+-------+
| s_no | c_no  | grade |
+------+-------+-------+
| 101  | 3-105 | A     |
| 102  | 3-105 | A     |
| 103  | 3-105 | A     |
| 103  | 3-245 | B     |
| 103  | 6-166 | B     |
| 104  | 3-105 | B     |
| 105  | 3-105 | B     |
| 105  | 3-245 | C     |
| 105  | 6-166 | C     |
| 109  | 3-105 | C     |
| 109  | 3-245 | D     |
| 109  | 6-166 | B     |
+------+-------+-------+
```

### 连接查询

准备用于测试连接查询的数据：

```mysql
CREATE DATABASE testJoin;
USE testJoin;

CREATE TABLE person (
    id INT,
    name VARCHAR(20),
    cardId INT
);

CREATE TABLE card (
    id INT,
    name VARCHAR(20)
);

INSERT INTO card VALUES (1, '饭卡'), (2, '建行卡'), (3, '农行卡'), (4, '工商卡'), (5, '邮政卡');
SELECT * FROM card;
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | 饭卡      |
|    2 | 建行卡    |
|    3 | 农行卡    |
|    4 | 工商卡    |
|    5 | 邮政卡    |
+------+-----------+

INSERT INTO person VALUES (1, '张三', 1), (2, '李四', 3), (3, '王五', 6);
SELECT * FROM person;
+------+--------+--------+
| id   | name   | cardId |
+------+--------+--------+
|    1 | 张三   |      1 |
|    2 | 李四   |      3 |
|    3 | 王五   |      6 |
+------+--------+--------+
```

分析两张表发现，`person` 表并没有为 `cardId` 字段设置一个在 `card` 表中对应的 `id` 外键。如果设置了的话，`person` 中 `cardId` 字段值为 `6` 的行就插不进去，因为该 `cardId` 值在 `card` 表中并没有。

#### 内连接 INNER JOIN = JOIN
要查询这两张表中有关系的数据，可以使用 `INNER JOIN` ( 内连接 ) 将它们连接在一起。

```mysql
SELECT * FROM person INNER JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
+------+--------+--------+------+-----------+
```

####  LEFT JOIN

完整显示左边的表，右边的表如果符合条件就显示，不符合则补 `NULL` 。

```mysql
SELECT * FROM person LEFT JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
|    3 | 王五   |      6 | NULL | NULL      |
+------+--------+--------+------+-----------+
```

#### 右外链接 RIGHT JOIN

完整显示右边的表，左边的表如果符合条件就显示，不符合则补 `NULL` 。

```mysql
SELECT * FROM person RIGHT JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
| NULL | NULL   |   NULL |    2 | 建行卡    |
| NULL | NULL   |   NULL |    4 | 工商卡    |
| NULL | NULL   |   NULL |    5 | 邮政卡    |
+------+--------+--------+------+-----------+
```

#### 全外链接 FULL JOIN

完整显示两张表的全部数据。

```mysql
-- MySQL 不支持这种语法的全外连接
-- SELECT * FROM person FULL JOIN card on person.cardId = card.id;
-- ERROR 1054 (42S22): Unknown column 'person.cardId' in 'on clause'

-- MySQL全连接语法，使用 UNION 将LEFT JOIN结果表 和 RIGHT JOIN结果表合并在一起。
SELECT * FROM person LEFT JOIN card on person.cardId = card.id
UNION
SELECT * FROM person RIGHT JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
|    3 | 王五   |      6 | NULL | NULL      |
| NULL | NULL   |   NULL |    2 | 建行卡    |
| NULL | NULL   |   NULL |    4 | 工商卡    |
| NULL | NULL   |   NULL |    5 | 邮政卡    |
+------+--------+--------+------+-----------+
```

## 事务

在 MySQL 中，事务是一个最小的不可分割的工作单元。事务能够保证一个业务的完整性。

比如我们的银行转账（a给b转100）：

```mysql
-- a -> -100
UPDATE user set money = money - 100 WHERE name = 'a';

-- b -> +100
UPDATE user set money = money + 100 WHERE name = 'b';
```

在实际项目中，假设只有一条 SQL 语句执行成功，而另外一条执行失败了，就会出现数据前后不一致。

### 如何控制事务 - COMMIT / ROLLBACK
事务可能会要求多条SQL语句同时执行成功或者同时失败。

1. 自动提交
- 查看自动提交状态：`SELECT @@AUTOCOMMIT` ；
- 设置自动提交状态：`SET AUTOCOMMIT = 0` ;
- 事物默认自动提交，效果立即体现，不能回滚。

2. 手动提交
- `@@AUTOCOMMIT = 0` 时，使用 `COMMIT` 命令提交事务。

3. 事务回滚
- `@@AUTOCOMMIT = 0` 时，使用 `ROLLBACK` 命令回滚事务。

4. 事物开启
- SET AUTOCOMMIT = 0
- BEGIN
- START TRANSACTION

5. 事物提交
- COMMIT

```mysql
-- 查询事务的自动提交状态

CREATE DATABASE bank;
USE bank;

CREATE TABLE user (
    id INT PRIMARY KEY,
    name VARCHAR(20),
    money INT
);

SELECT @@AUTOCOMMIT;
+--------------+
| @@AUTOCOMMIT |
+--------------+
|            1 |
+--------------+

INSERT INTO user VALUES (1, 'a', 1000);

+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+

-- 回滚到最后一次提交
ROLLBACK;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+
```
可以看到，在执行插入语句后数据立刻生效，原因是 MySQL 中的事务自动提交到了数据库。回滚的意思是，撤销执行过的所有 SQL 语句，使其回滚到最后一次提交数据时的状态。

```mysql
-- 关闭自动提交，数据的变化是在一张虚拟的临时数据表中展示
-- 发生变化的数据并没有真正插入到数据表中。
SET AUTOCOMMIT = 0;
+--------------+
| @@AUTOCOMMIT |
+--------------+
|            0 |
+--------------+

INSERT INTO user VALUES (2, 'b', 1000);
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+

-- 数据表中的真实数据其实还是：
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+


ROLLBACK;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+
```

使用 `COMMIT`手动提交数据 : 

```mysql
SET AUTOCOMMIT = 0;
INSERT INTO user VALUES (2, 'b', 1000);
COMMIT;

ROLLBACK;（回滚无效）
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+
```

### 手动开启事务 - BEGIN / START TRANSACTION

事务的默认提交被开启 ( `@@AUTOCOMMIT = 1` ) 后，此时就不能使用事务回滚了。但是我们还可以手动开启一个事务处理事件，使其可以发生回滚：

```mysql
-- 使用 BEGIN 或者 START TRANSACTION 手动开启一个事务，无自动提交

BEGIN;
UPDATE user set money = money - 100 WHERE name = 'a';
UPDATE user set money = money + 100 WHERE name = 'b';
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   900 |
|  2 | b    |  1100 |
+----+------+-------+

ROLLBACK;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+

- 提交
COMMIT;
```

### 事务的 ACID 特征与使用
A （Atomic）原子性: 事务是最小的单位，不可以再分割;
C （Consistent）一致性: 要求同一事务中的 SQL 语句，必须保证同时成功或者失败;
I （Isolated）隔离: 事务1 和 事务2 之间是具有隔离性的;
D （Durability) 持久性: 事务一旦结束 ( `COMMIT` ) ，就不可以再返回了 ( `ROLLBACK` ).

#### 查看当前数据库的默认隔离级别：
```mysql
-- GLOBAL表示系统级别，不加表示会话级别。
SELECT @@GLOBAL.TRANSACTION_ISOLATION;
SELECT @@TRANSACTION_ISOLATION;
+--------------------------------+
| @@GLOBAL.TRANSACTION_ISOLATION |
+--------------------------------+
| REPEATABLE-READ                | -- MySQL的默认隔离级别，可以重复读。
+--------------------------------+
```

#### 修改隔离级别：
```mysql
-- 设置系统隔离级别，LEVEL 后面表示要设置的隔离级别
SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
+--------------------------------+
| @@GLOBAL.TRANSACTION_ISOLATION |
+--------------------------------+
| READ-UNCOMMITTED               |
+--------------------------------+
```
#### 事务的隔离性

1. **READ UNCOMMITTED 读取未提交 ** 

   如果有多个事务，那么任意事务都可以看见其他事务的**未提交数据**。又名脏读，在实际开发中不允许出现.

2. **READ COMMITTED 读取已提交 **

   只能读取到其他事务**已经提交的数据**。在读取同一个表的数据时，可能会前后不一致，因为一个事务在操作数据时，其他事务的操作干扰了这个事务的数据。

3. **REPEATABLE READ 可被重复读 **

   如果有多个连接都开启了事务，那么事务之间不能共享数据记录，否则只能共享已提交的记录。存在幻读问题。

4. **SERIALIZABLE 串行化 **

   所有的事务都会按照**固定顺序执行**，执行完一个事务后再继续执行下一个事务的**写入操作**。

隔离级别越高，性能越差
mysql 默认隔离级别是 REPEATABLE READ

##### 幻读

将隔离级别设置为 **REPEATABLE READ ( 可被重复读取 )** :

```mysql
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- 小张 - 成都
START TRANSACTION;

-- 小王 - 北京
START TRANSACTION;

-- 小张 - 成都
INSERT INTO user VALUES (6, 'd', 1000);
COMMIT;

-- 小王 - 北京
INSERT INTO user VALUES (6, 'd', 1000);
ERROR 1062 (23000): Duplicate entry '6' for key 'PRIMARY'
```

当前事务开启后，没提交之前，查询不到，提交后可以被查询到。但是，在提交之前其他事务被开启了，那么在这条事务线上，就不会查询到当前有操作事务的连接。相当于开辟出一条单独的线程。

无论小张是否 `COMMIT` ，在小王都不会查询到小张的事务记录，而是只会查询到自己所处事务的记录，当小王插入同一条数据，会报错。
事物a 和 事物b 同时操作一张表，a 提交的数据，b查不到，称为幻读。
