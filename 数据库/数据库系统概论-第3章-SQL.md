# 第3章 关系数据库标准语言 SQL

## 3.1 SQL 概述

### 3.1.1 SQL 的产生与发展

- **1974年**：Boyce 和 Chamberlin 提出，最初名为 SEQUEL，在 IBM System R 上实现
- **1986年**：ANSI 批准为美国标准（SQL86）
- **1987年**：ISO 通过为国际标准
- 后续版本：SQL89 → SQL92 (SQL2) → SQL99 (SQL3) → SQL2003 → SQL2008 → SQL2011 → SQL2016
- SQL2016 已扩展到12个部分，引入 XML、Window 函数、TRUNCATE、时序数据、JSON 等

SQL 是一种**非过程化语言**：用户只需说明"要什么"，无须指明"怎么干"，存取路径选择和具体处理由 RDBMS 自动完成。

### 3.1.2 SQL 的特点

1. **功能综合且风格统一**：集 DDL、DML、DCL 于一体，可独立完成数据库生命周期全部活动
2. **数据操纵高度非过程化**
3. **面向集合的操作方式**
4. **以统一的语法结构提供多种使用方式**（独立语言 + 嵌入式语言）
5. **语言简洁，易学易用**

**完成核心功能的9个动词**：

| SQL 功能 | 动词 |
|----------|------|
| 数据定义 | CREATE, DROP, ALTER |
| 数据查询 | SELECT |
| 数据操纵 | INSERT, UPDATE, DELETE |
| 数据控制 | GRANT, REVOKE |

### 3.1.3 SQL 的基本概念

SQL 支持关系数据库三级模式结构：

| 层次 | SQL 对应 |
|------|----------|
| 外模式 | 视图（View）+ 部分基本表 |
| 模式 | 基本表（Base Table） |
| 内模式 | 存储文件（Stored File） |

- **基本表**：本身独立存在的表，一个关系对应一个基本表
- **视图**：从基本表或其他视图导出的虚表，只存定义不存数据

---

## 3.2 数据定义

### 3.2.1 模式的定义与删除

**定义模式**：
```sql
CREATE SCHEMA [<模式名>] AUTHORIZATION <用户名>;
```

> **例3.1** 为用户 WANG 定义"学生选课"模式 S-C-SC
> ```sql
> CREATE SCHEMA "S-C-SC" AUTHORIZATION WANG;
> ```

> **例3.3** 创建模式 Test，并在其中定义表 Tab1
> ```sql
> CREATE SCHEMA Test AUTHORIZATION ZHANG
> CREATE TABLE Tab1(Col1 SMALLINT, Col2 INT, Col3 CHAR(20),
>                     Col4 NUMERIC(10,3), Col5 DECIMAL(5,2));
> ```

**删除模式**：
```sql
DROP SCHEMA <模式名> <CASCADE|RESTRICT>;
```
- `CASCADE`：级联删除模式中所有数据库对象
- `RESTRICT`：仅当模式中无对象时才可删除

### 3.2.2 基本表的定义、删除与修改

**定义基本表**：
```sql
CREATE TABLE <表名> (
    <列名> <数据类型> [列级完整性约束],
    ...
    [, <表级完整性约束>]
);
```

> **例3.5** 建立学生表 Student
> ```sql
> CREATE TABLE Student
> (Sno CHAR(8) PRIMARY KEY,
>  Sname VARCHAR(20) UNIQUE,
>  Ssex CHAR(6),
>  Sbirthdate DATE,
>  Smajor VARCHAR(40)
> );
> ```

> **例3.6** 建立课程表 Course
> ```sql
> CREATE TABLE Course
> (Cno CHAR(5) PRIMARY KEY,
>  Cname VARCHAR(40) NOT NULL,
>  Ccredit SMALLINT,
>  Cpno CHAR(5),
>  FOREIGN KEY(Cpno) REFERENCES Course(Cno)
> );
> ```

> **例3.7** 建立学生选课表 SC
> ```sql
> CREATE TABLE SC
> (Sno CHAR(8),
>  Cno CHAR(5),
>  Grade SMALLINT,
>  Semester CHAR(5),
>  Teachingclass CHAR(8),
>  PRIMARY KEY(Sno, Cno),
>  FOREIGN KEY(Sno) REFERENCES Student(Sno),
>  FOREIGN KEY(Cno) REFERENCES Course(Cno)
> );
> ```

**常用数据类型**：

| 数据类型 | 含义 |
|----------|------|
| CHAR(n) | 定长字符串 |
| VARCHAR(n) | 变长字符串 |
| INT / INTEGER | 整数（4字节） |
| SMALLINT | 短整数（2字节） |
| BIGINT | 大整数（8字节） |
| NUMERIC(p,d) | 定点数 |
| FLOAT(n) | 浮点数 |
| DATE | 日期 YYYY-MM-DD |
| TIME | 时间 HH:MM:SS |
| TIMESTAMP | 时间戳 |
| BOOLEAN | 布尔值 |
| CLOB / BLOB | 字符串大对象 / 二进制大对象 |

**修改基本表**：
```sql
ALTER TABLE <表名>
    [ADD [COLUMN] <新列名> <数据类型> [完整性约束]]
    [DROP [COLUMN] <列名> [CASCADE|RESTRICT]]
    [DROP CONSTRAINT <完整性约束名>]
    [RENAME COLUMN <列名> TO <新列名>]
    [ALTER COLUMN <列名> TYPE <数据类型>];
```

> **例3.8** 增加邮箱地址列
> ```sql
> ALTER TABLE Student ADD Semail VARCHAR(30);
> ```

> **例3.9** 修改出生日期数据类型
> ```sql
> ALTER TABLE Student ALTER COLUMN Sbirthdate TYPE VARCHAR(20);
> ```

> **例3.10** 增加课程名唯一约束
> ```sql
> ALTER TABLE Course ADD UNIQUE(Cname);
> ```

**删除基本表**：
```sql
DROP TABLE <表名> [RESTRICT|CASCADE];
```

> **例3.11** 级联删除 Student 表
> ```sql
> DROP TABLE Student CASCADE;
> ```

### 3.2.3 索引的建立与删除

**建立索引**：
```sql
CREATE [UNIQUE] [CLUSTER] INDEX <索引名>
ON <表名>(<列名> [ASC|DESC], ...);
```

> **例3.13**
> ```sql
> CREATE UNIQUE INDEX Idx_StuSname ON Student(Sname);
> CREATE UNIQUE INDEX Idx_CouCname ON Course(Cname);
> CREATE UNIQUE INDEX Idx_SCCno ON SC(Sno ASC, Cno DESC);
> ```

**修改索引名**：
```sql
ALTER INDEX <旧索引名> RENAME TO <新索引名>;
```

**删除索引**：
```sql
DROP INDEX <索引名>;
```

**常见索引类型**：顺序表索引、B+树索引、哈希索引、位图索引。

### 3.2.4 数据字典

数据字典是 RDBMS 内部的一组系统表，记录所有定义信息（关系模式、视图、索引、完整性约束、权限、统计信息等），是 RDBMS 运行的重要依据。

---

## 3.3 数据查询

### SELECT 语句一般格式

```sql
SELECT [ALL|DISTINCT] <目标列表达式> [别名], ...
FROM <表名或视图名> [别名], ...
     [(SELECT ...) [AS] <别名>]
[WHERE <条件表达式>]
[GROUP BY <列名1> [HAVING <条件表达式>]]
[ORDER BY <列名2> [ASC|DESC]]
[LIMIT <行数1> [OFFSET <行数2>]];
```

### 3.3.1 单表查询

#### 1. 选择表中的若干列（投影）

> **例3.16** 查询全体学生的学号与姓名
> ```sql
> SELECT Sno, Sname FROM Student;
> ```

> **例3.17** 查询姓名、学号、主修专业
> ```sql
> SELECT Sname, Sno, Smajor FROM Student;
> ```

> **例3.18** 查询全部列
> ```sql
> SELECT * FROM Student;
> ```

> **例3.19** 查询姓名及年龄（带计算）
> ```sql
> SELECT Sname, (extract(year from current_date) - extract(year from Sbirthdate)) "年龄"
> FROM Student;
> ```

> **例3.20** 带字符串常量的查询
> ```sql
> SELECT Sname, 'Date of Birth:', Sbirthdate, Smajor FROM Student;
> ```

#### 2. 选择表中的若干元组

**① 消除重复行（DISTINCT）**

> **例3.21** 查询选修了课程的学生学号
> ```sql
> SELECT DISTINCT Sno FROM SC;
> ```

**② 比较大小**

> **例3.22** 查询计算机科学与技术专业的学生姓名
> ```sql
> SELECT Sname FROM Student WHERE Smajor = '计算机科学与技术';
> ```

> **例3.23** 查询2000年及以后出生的学生
> ```sql
> SELECT Sname, Ssex FROM Student
> WHERE extract(year from Sbirthdate) >= 2000;
> ```

> **例3.24** 查询不及格学生的学号
> ```sql
> SELECT DISTINCT Sno FROM SC WHERE Grade < 60;
> ```

**③ 确定范围（BETWEEN）**

> **例3.25** 查询年龄在20~23岁之间的学生
> ```sql
> SELECT Sname, Sbirthdate, Smajor FROM Student
> WHERE (extract(year from current_date) - extract(year from Sbirthdate)) BETWEEN 20 AND 23;
> ```

> **例3.26** 查询年龄不在20~23岁的学生
> ```sql
> ... WHERE ... NOT BETWEEN 20 AND 23;
> ```

**④ 确定集合（IN）**

> **例3.27** 查询计算机科学与技术和信息安全专业的学生
> ```sql
> SELECT Sname, Ssex FROM Student
> WHERE Smajor IN ('计算机科学与技术', '信息安全');
> ```

> **例3.28** 非上述专业
> ```sql
> WHERE Smajor NOT IN ('计算机科学与技术', '信息安全');
> ```

**⑤ 字符匹配（LIKE）**

通配符：`%` 任意长度字符串，`_` 单个字符。

> **例3.29** 查询学号20180003
> ```sql
> SELECT * FROM Student WHERE Sno LIKE '20180003';
> -- 等价于: WHERE Sno = '20180003';
> ```

> **例3.30** 查询所有姓刘的学生
> ```sql
> SELECT Sname, Sno, Ssex FROM Student WHERE Sname LIKE '刘%';
> ```

> **例3.31** 查询2018级学生
> ```sql
> SELECT Sno, Sname FROM Student WHERE Sno LIKE '2018%';
> ```

> **例3.32** 查询课程号81开头、最后一位是6
> ```sql
> SELECT Cname, Cno FROM Course WHERE Cno LIKE '81_6';
> ```

> **例3.33** 不姓刘
> ```sql
> WHERE Sname NOT LIKE '刘%';
> ```

> **例3.34** 用 ESCAPE 转义
> ```sql
> SELECT Cno, Ccredit FROM Course
> WHERE Cname LIKE 'DB\_Design' ESCAPE '\';
> ```

**⑥ 涉及空值的查询**

> **例3.36** 查询缺考学生（无成绩）
> ```sql
> SELECT Sno, Cno FROM SC WHERE Grade IS NULL;
> ```

> **例3.37** 有成绩的学生
> ```sql
> SELECT Sno, Cno FROM SC WHERE Grade IS NOT NULL;
> ```
> 注意：`IS NULL` 不能用 `= NULL` 代替。

**⑦ 多条件查询（AND / OR）**

> **例3.38** 计算机科学与技术专业、2000年以后出生
> ```sql
> SELECT Sno, Sname, Ssex FROM Student
> WHERE Smajor = '计算机科学与技术'
>   AND extract(year from Sbirthdate) >= 2000;
> ```

#### 3. ORDER BY 子句

> **例3.39** 查询81003课程成绩，按降序
> ```sql
> SELECT Sno, Grade FROM SC WHERE Cno = '81003'
> ORDER BY Grade DESC;
> ```

> **例3.40** 先按课程号升序，同课程按成绩降序
> ```sql
> SELECT * FROM SC ORDER BY Cno ASC, Grade DESC;
> ```

#### 4. 聚集函数

| 函数 | 功能 |
|------|------|
| COUNT(*) | 统计元组个数 |
| COUNT([DISTINCT] <列名>) | 统计一列中值的个数 |
| SUM([DISTINCT] <列名>) | 计算一列值的总和（数值型） |
| AVG([DISTINCT] <列名>) | 计算一列值的平均值（数值型） |
| MAX([DISTINCT] <列名>) | 求最大值 |
| MIN([DISTINCT] <列名>) | 求最小值 |

> **例3.41** 查询学生总人数
> ```sql
> SELECT COUNT(*) FROM Student;
> ```

> **例3.42** 查询选修了课程的学生人数
> ```sql
> SELECT COUNT(DISTINCT Sno) FROM SC;
> ```

> **例3.43** 计算81001课程的平均成绩
> ```sql
> SELECT AVG(Grade) FROM SC WHERE Cno = '81001';
> ```

> **例3.44** 查询81001课程的最高分
> ```sql
> SELECT MAX(Grade) FROM SC WHERE Cno = '81001';
> ```

> **例3.45** 查询学生20180003选修课程的总学分
> ```sql
> SELECT SUM(Ccredit) FROM SC, Course
> WHERE Sno = '20180003' AND SC.Cno = Course.Cno;
> ```

#### 5. GROUP BY 子句

> **例3.46** 求各课程号及选修人数
> ```sql
> SELECT Cno, COUNT(Sno) FROM SC GROUP BY Cno;
> ```

> **例3.47** 查询20192学期选修超过10门课的学生
> ```sql
> SELECT Sno FROM SC
> WHERE Semester = '20192'
> GROUP BY Sno
> HAVING COUNT(*) > 10;
> ```

**WHERE vs HAVING**：WHERE 作用于基本表/视图（元组级筛选），HAVING 作用于分组后的组。

> **例3.48** 查询平均成绩 ≥ 90 的学生学号和平均成绩
> ```sql
> SELECT Sno, AVG(Grade) FROM SC
> GROUP BY Sno
> HAVING AVG(Grade) >= 90;
> ```

#### 6. LIMIT 子句

> **例3.49** 数据库系统概论课程成绩前10名的学生
> ```sql
> SELECT Sno FROM SC, Course
> WHERE Course.Cname = '数据库系统概论' AND SC.Cno = Course.Cno
> ORDER BY Grade DESC
> LIMIT 10;
> ```

> **例3.50** 平均成绩排名第3~7名
> ```sql
> SELECT Sno, AVG(Grade) FROM SC
> GROUP BY Sno
> ORDER BY AVG(Grade) DESC
> LIMIT 5 OFFSET 2;
> ```

### 3.3.2 连接查询

#### 1. 等值与非等值连接查询

> **例3.51** 查询每个学生及其选修课程情况
> ```sql
> SELECT Student.*, SC.* FROM Student, SC
> WHERE Student.Sno = SC.Sno;
> ```

#### 2. 自然连接查询

> **例3.52** 去掉重复列 Sno
> ```sql
> SELECT Student.Sno, Sname, Ssex, Sbirthdate, Smajor, Cno, Grade
> FROM Student, SC WHERE Student.Sno = SC.Sno;
> ```

#### 3. 复合条件连接查询

> **例3.53** 选修81002且成绩>90的学生的学号和姓名
> ```sql
> SELECT Student.Sno, Sname FROM Student, SC
> WHERE Student.Sno = SC.Sno
>   AND SC.Cno = '81002' AND SC.Grade > 90;
> ```

#### 4. 自身连接查询

> **例3.54** 查询每门课的间接先修课（先修课的先修课）
> ```sql
> SELECT FIRST.Cno, SECOND.Cpno
> FROM Course FIRST, Course SECOND
> WHERE FIRST.Cpno = SECOND.Cno AND SECOND.Cpno IS NOT NULL;
> ```

#### 5. 外连接查询

> **例3.55** 左外连接，保留没有选课的学生
> ```sql
> SELECT Student.Sno, Sname, Ssex, Sbirthdate, Smajor, Cno, Grade
> FROM Student LEFT OUTER JOIN SC ON (Student.Sno = SC.Sno);
> ```

外连接类型：左外连接（保留左边关系悬浮元组）、右外连接、全外连接。

#### 6. 多表连接查询

> **例3.56** 查询每个学生的学号、姓名、选修课程名及成绩
> ```sql
> SELECT Student.Sno, Sname, Cname, Grade
> FROM Student, SC, Course
> WHERE Student.Sno = SC.Sno AND SC.Cno = Course.Cno;
> ```

### 3.3.3 嵌套查询

#### 1. 带有 IN 谓词的子查询（不相关子查询）

> **例3.57** 查询与"刘晨"同专业的学生
> ```sql
> SELECT Sno, Sname, Smajor FROM Student
> WHERE Smajor IN (SELECT Smajor FROM Student WHERE Sname = '刘晨');
> ```
>
> 等价自身连接写法：
> ```sql
> SELECT S1.Sno, S1.Sname, S1.Smajor
> FROM Student S1, Student S2
> WHERE S1.Smajor = S2.Smajor AND S2.Sname = '刘晨';
> ```

> **例3.58** 查询选修"信息系统概论"的学生学号和姓名（三层嵌套）
> ```sql
> SELECT Sno, Sname FROM Student
> WHERE Sno IN
>     (SELECT Sno FROM SC
>      WHERE Cno IN
>          (SELECT Cno FROM Course
>           WHERE Cname = '信息系统概论'));
> ```

#### 2. 带有比较运算符的子查询

当内层查询返回单个值时，可用 `=`、`>`、`<` 等替代 IN。

> **例3.57（解法三）**：
> ```sql
> SELECT Sno, Sname, Smajor FROM Student
> WHERE Smajor = (SELECT Smajor FROM Student WHERE Sname = '刘晨');
> ```

**相关子查询**（子查询条件依赖于父查询）：

> **例3.59** 找出每个学生超过自己平均成绩的课程号
> ```sql
> SELECT Sno, Cno FROM SC x
> WHERE Grade >= (SELECT AVG(Grade) FROM SC y WHERE y.Sno = x.Sno);
> ```

#### 3. 带有 ANY (SOME) 或 ALL 谓词的子查询

| 谓词 | 等价的聚集函数 |
|------|---------------|
| `>ANY` | `>MIN` |
| `<ANY` | `<MAX` |
| `>ALL` | `>MAX` |
| `<ALL` | `<MIN` |
| `=ANY` | `IN` |
| `<>ALL` | `NOT IN` |

> **例3.60** 非计算机专业中，比计算机专业任意一个年龄小的学生
> ```sql
> SELECT Sname, Sbirthdate, Smajor FROM Student
> WHERE Sbirthdate >ANY (SELECT Sbirthdate FROM Student
>                         WHERE Smajor = '计算机科学与技术')
>   AND Smajor <> '计算机科学与技术';
> ```
> 等价于用 `>MIN` 实现。

> **例3.61** 非计算机专业中，比计算机专业所有学生年龄都小的
> ```sql
> WHERE Sbirthdate >ALL (SELECT Sbirthdate FROM Student
>                         WHERE Smajor = '计算机科学与技术')
> -- 等价于: WHERE Sbirthdate > (SELECT MAX(Sbirthdate) ...)
> ```

#### 4. 带有 EXISTS 谓词的子查询

- `EXISTS`：内层查询结果非空返回 True
- `NOT EXISTS`：内层查询结果为空返回 True

> **例3.63** 查询没有选修81001课程的学生姓名
> ```sql
> SELECT Sname FROM Student
> WHERE NOT EXISTS
>     (SELECT * FROM SC
>      WHERE Sno = Student.Sno AND Cno = '81001');
> ```

> **例3.64** 查询选修了全部课程的学生姓名（全称量词 → NOT EXISTS 转换）
> ```sql
> SELECT Sname FROM Student
> WHERE NOT EXISTS
>     (SELECT * FROM Course
>      WHERE NOT EXISTS
>          (SELECT * FROM SC
>           WHERE Sno = Student.Sno AND Cno = Course.Cno));
> ```
> 思路：没有一门课是他不选修的。即 $(\forall x)P \equiv \neg(\exists x(\neg P))$。

> **例3.65** 查询至少选修了20180002学生所选全部课程的学生学号
> ```sql
> SELECT DISTINCT Sno FROM Student
> WHERE NOT EXISTS
>     (SELECT * FROM SC SCX
>      WHERE SCX.Sno = '20180002' AND
>            NOT EXISTS
>            (SELECT * FROM SC SCY
>             WHERE SCY.Sno = Student.Sno AND SCY.Cno = SCX.Cno));
> ```
> 思路：蕴含逻辑 $p \rightarrow q \equiv \neg(p \land \neg q)$。

### 3.3.4 集合查询

- `UNION`：并（自动去重），`UNION ALL` 保留重复
- `INTERSECT`：交
- `EXCEPT`：差

> **例3.66** 计算机专业学生与年龄≤19岁学生的并集
> ```sql
> SELECT * FROM Student WHERE Smajor = '计算机科学与技术'
> UNION
> SELECT * FROM Student
> WHERE extract(year from current_date) - extract(year from Sbirthdate) <= 19;
> ```

> **例3.69** 既选修81001又选修81002的学生
> ```sql
> SELECT Sno FROM SC WHERE Cno = '81001'
> INTERSECT
> SELECT Sno FROM SC WHERE Cno = '81002';
> ```

> **例3.70** 计算机专业与年龄≤19岁学生的差集
> ```sql
> SELECT * FROM Student WHERE Smajor = '计算机科学与技术'
> EXCEPT
> SELECT * FROM Student
> WHERE extract(year from current_date) - extract(year from Sbirthdate) <= 19;
> ```

### 3.3.5 基于派生表的查询

子查询出现在 FROM 子句中，生成临时派生表。

> **例3.59（派生表写法）**
> ```sql
> SELECT Sno, Cno
> FROM SC, (SELECT Sno, AVG(Grade) FROM SC GROUP BY Sno)
>           AS Avg_SC(Avg_sno, Avg_grade)
> WHERE SC.Sno = Avg_SC.Avg_sno AND SC.Grade >= Avg_SC.Avg_grade;
> ```

---

## 3.4 数据更新

### 3.4.1 插入数据

**插入一个元组**：
```sql
INSERT INTO <表名> [(<属性列1>, ...)] VALUES (<常量1>, ...);
```

> **例3.71**
> ```sql
> INSERT INTO Student(Sno, Sname, Ssex, Smajor, Sbirthdate)
> VALUES('20180009', '陈冬', '男', '信息管理与信息系统', '2000-5-22');
> ```

> **例3.73** 插入选课记录（成绩默认为空）
> ```sql
> INSERT INTO SC(Sno, Cno, Semester, Teachingclass)
> VALUES('20180005', '81004', '20202', '81004-01');
> ```

**插入子查询结果**：
```sql
INSERT INTO <表名> [(<属性列1>, ...)] 子查询;
```

> **例3.74** 将各专业的平均年龄存入新表
> ```sql
> INSERT INTO Smajor_age(Smajor, Avg_age)
> SELECT Smajor, AVG(extract(year from current_date) - extract(year from Sbirthdate))
> FROM Student GROUP BY Smajor;
> ```

### 3.4.2 修改数据

```sql
UPDATE <表名>
SET <列名> = <表达式>, ...
[WHERE <条件>];
```

> **例3.75** 修改单个元组
> ```sql
> UPDATE Student SET Sbirthdate = '2001-3-18' WHERE Sno = '20180001';
> ```

> **例3.76** 修改多个元组
> ```sql
> UPDATE SC SET Grade = Grade - 5
> WHERE Semester = '20201' AND Cno = '81002';
> ```

> **例3.77** 带子查询的修改
> ```sql
> UPDATE SC SET Grade = 0
> WHERE Sno IN (SELECT Sno FROM Student WHERE Smajor = '计算机科学与技术');
> ```

### 3.4.3 删除数据

```sql
DELETE FROM <表名> [WHERE <条件>];
```

> **例3.78** 删除单个元组
> ```sql
> DELETE FROM Student WHERE Sno = '20180007';
> ```

> **例3.79** 删除所有选课记录
> ```sql
> DELETE FROM SC;
> ```

> **例3.80** 带子查询的删除
> ```sql
> DELETE FROM SC
> WHERE Sno IN (SELECT Sno FROM Student WHERE Smajor = '计算机科学与技术');
> ```

---

## 3.5 空值的处理

**空值（NULL）**：表示"不知道"、"不存在"或"无意义"的值。

**产生空值的情况**：
- 该属性有值但暂时不知道
- 该属性不应有值（如缺考）
- 不便于填写

**判断**：`IS NULL` / `IS NOT NULL`（不能用 `=` 或 `<>`）

**约束**：
- NOT NULL 属性不能取空值
- 主码属性不能取空值
- UNIQUE 约束列能否取空值？SQL标准未明确规定，各RDBMS实现不同

**运算规则**：
- 空值与任何值的**算术运算**结果为空值
- 空值与任何值的**比较运算**结果为 UNKNOWN（三值逻辑）

**三值逻辑真值表**：

| x AND y | T | U | F | | x OR y | T | U | F |
|---------|---|---|---|-------|---|---|---|---|
| T | T | U | F | T | T | T | T |
| U | U | U | F | U | T | U | U |
| F | F | F | F | F | T | U | F |

NOT T=F, NOT U=U, NOT F=T

> **例3.85** 选出81001号课不及格和缺考的学生
> ```sql
> SELECT Sno FROM SC WHERE Cno = '81001' AND (Grade < 60 OR Grade IS NULL);
> ```

---

## 3.6 视图

### 3.6.1 定义视图

**创建视图**：
```sql
CREATE VIEW <视图名> [(<列名>, ...)]
AS <子查询>
[WITH CHECK OPTION];
```

> **例3.86** 信息管理与信息系统专业学生视图
> ```sql
> CREATE VIEW IS_Student
> AS
> SELECT Sno, Sname, Ssex, Sbirthdate, Smajor FROM Student
> WHERE Smajor = '信息管理与信息系统';
> ```

> **例3.87** 带 WITH CHECK OPTION 的视图
> ```sql
> CREATE VIEW IS_Student AS ...
> WITH CHECK OPTION;
> ```
> 对视图增删改时自动检查 `Smajor = '信息管理与信息系统'`。

> **例3.88** 建立在多表上的视图
> ```sql
> CREATE VIEW IS_C1(Sno, Sname, Grade)
> AS
> SELECT Student.Sno, Sname, Grade
> FROM Student, SC
> WHERE Smajor = '信息管理与信息系统'
>   AND Student.Sno = SC.Sno AND SC.Cno = '81001';
> ```

**三种必须明确指定视图所有列名的情况**：
1. 目标列是聚集函数或列表达式
2. 多表连接时有同名列
3. 需要为列启用新名字

> **例3.90** 带表达式的视图（学生的学号、姓名、年龄）
> ```sql
> CREATE VIEW S_AGE(Sno, Sname, Sage)
> AS
> SELECT Sno, Sname,
>        extract(year from current_date) - extract(year from Sbirthdate)
> FROM Student;
> ```

> **例3.91** 分组视图（学生学号及平均成绩）
> ```sql
> CREATE VIEW S_GradeAVG(Sno, Gavg)
> AS
> SELECT Sno, AVG(Grade) FROM SC GROUP BY Sno;
> ```

**删除视图**：
```sql
DROP VIEW <视图名> [CASCADE];
```

> **例3.93**
> ```sql
> DROP VIEW IS_C1 CASCADE;  -- 级联删除 IS_C1 和 IS_C2
> ```

### 3.6.2 查询视图

**视图消解（View Resolution）**：将查询视图转换为对基本表的查询。

> **例3.94** 在IS_Student视图中查询年龄≤20的学生
> ```sql
> SELECT Sno, Sbirthdate FROM IS_Student
> WHERE (extract(year from current_date) - extract(year from Sbirthdate)) <= 20;
> ```
> 消解后：
> ```sql
> SELECT Sno, Sbirthdate FROM Student
> WHERE Smajor = '信息管理与信息系统'
>   AND (extract(year from current_date) - extract(year from Sbirthdate)) <= 20;
> ```

注意：行列子集视图查询通常能正确转换，非行列子集视图（如带聚集函数的）可能转换错误。

### 3.6.3 更新视图

更新视图最终转换为对基本表的更新。**行列子集视图一般是可更新的**。

> **例3.97** 修改视图
> ```sql
> UPDATE IS_Student SET Sname = '刘新奇' WHERE Sno = '20180005';
> ```
> 消解后：
> ```sql
> UPDATE Student SET Sname = '刘新奇'
> WHERE Sno = '20180005' AND Smajor = '信息管理与信息系统';
> ```

> **例3.99** 删除视图数据
> ```sql
> DELETE FROM IS_Student WHERE Sno = '20180207';
> ```

**不可更新的视图**：如例3.91的S_GradeAVG视图，平均成绩由多行计算而来，无法有意义地转换为对基本表的更新。

### 3.6.4 视图的作用

1. **安全保护**：对机密数据提供安全保护（不同用户定义不同视图）
2. **逻辑独立性**：重构数据库时，通过视图保持外模式不变
3. **简化用户操作**：隐藏复杂的表连接，用户只需查询虚表
4. **多种角度看待同一数据**：不同用户按各自需求定义视图

---

## 核心概念速记

| 概念 | 要点 |
|------|------|
| SQL 9个核心动词 | CREATE/DROP/ALTER, SELECT, INSERT/UPDATE/DELETE, GRANT/REVOKE |
| 选择列 | `SELECT` + 投影 |
| 选择行 | `WHERE` + 比较/BETWEEN/IN/LIKE/IS NULL/AND/OR |
| 排序 | `ORDER BY` ASC/DESC |
| 分组 | `GROUP BY` + `HAVING`（WHERE 作用于元组，HAVING 作用于组） |
| 限制行数 | `LIMIT n OFFSET m` |
| 连接条件 | `WHERE R.A = S.B`（等值/非等值/自然/自身/外连接/多表） |
| 嵌套查询 | IN / 比较运算符 / ANY-ALL / EXISTS-NOT EXISTS |
| 集合操作 | UNION / INTERSECT / EXCEPT |
| 派生表 | 子查询放在 FROM 子句中 |
| LIKE 通配符 | `%` 任意长度字符串，`_` 单个字符 |
| 空值 | IS NULL / IS NOT NULL，参与运算得 UNKNOWN |
| 视图类型 | 行列子集视图（可更新）、带表达式视图、分组视图、多表视图 |
| WITH CHECK OPTION | 通过视图更新时自动检查视图定义条件 |
| 视图消解 | 将对视图的查询转换为对基本表的查询 |
