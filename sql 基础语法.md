#### 创建表
结构
```sql
create table 表名(
列名 数据类型 约束条件 ,
列名 数据类型 约束条件 ,
);

数据类型：
数字：
int
float
decimal(10,2) 
字符：
varchar(n)
日期：
date
time

约束条件
PRIMARY KEY 主键
FORIGEN KEY 外键
NOT NULL 非空
UNIQUE  唯一
CHECK（条件）

```


### 表查询
```sql
select 查询列1，列2   

destinct（去重复）


from 表名
order by desc(降序)asc（升序）
where 条件
```