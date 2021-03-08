数据库基础：

## 1.

int(1) 不是用来限制长度的

## 2.sql约束

  primary key----主键约束

  default 10 ----- 默认值约束

  unique(phone)---唯一约束

 foreign key----外键约束

```sql
CREATE DATABASE gradesystem;

use gradesystem

CREATE TABLE student(
    sid int NOT NULL AUTO_INCREMENT,
    sname varchar(20) NOT NULL,
    gender varchar(10) NOT NULL,
    PRIMARY KEY(sid)
    );

CREATE TABLE course(
    cid int NOT NULL AUTO_INCREMENT,
    cname varchar(20) NOT NULL,
    PRIMARY KEY(cid)
    );

CREATE TABLE mark(
    mid int NOT NULL AUTO_INCREMENT,
    sid int NOT NULL,
    cid int NOT NULL,
    score int NOT NULL,
    PRIMARY KEY(mid),
    FOREIGN KEY(sid) REFERENCES student(sid),
    FOREIGN KEY(cid) REFERENCES course(cid)
    );

INSERT INTO student VALUES(1,'Tom','male'),(2,'Jack','male'),(3,'Rose','female');

INSERT INTO course VALUES(1,'math'),(2,'physics'),(3,'chemistry');

INSERT INTO mark VALUES(1,1,1,80),(2,2,1,85),(3,3,1,90),(4,1,2,60),(5,2,2,90),(6,3,2,75),(7,1,3,95),(8,2,3,75),(9,3,3,85);
```

## 3.查询语句：

  in和not in筛选数据在不在某个范围

```sql
SELECT name,age,phone,in_dpt FROM employee WHERE in_dpt IN ('dpt3','dpt4');
```

  like用于模糊查询

  对结果进行排序

```sql
SELECT name,age,salary,phone FROM employee ORDER BY salary DESC;
--降序排列 升序为asc 默认查询为升序排列
```

  子查询

```sql
SELECT of_dpt,COUNT(proj_name) AS count_project FROM project GROUP BY of_dpt
HAVING of_dpt IN
(SELECT in_dpt FROM employee WHERE name='Tom');
```

  连接查询

```sql
SELECT id,name,people_num
FROM employee,department
WHERE employee.in_dpt = department.dpt_name
ORDER BY id;
```

## 4.删除和修改

  删除数据库

```sql
DROP DATABASE test_01;
```

  重命名表

```sql
RENAME TABLE 原名 TO 新名字;

ALTER TABLE 原名 RENAME 新名;

ALTER TABLE 原名 RENAME TO 新名;

RENAME TABLE table_1 TO table_2;
```

  删除表

```sql
DROP TABLE 表名字;

DROP TABLE table_2;
```

  增加列

```sql
ALTER TABLE 表名字 ADD COLUMN 列名字 数据类型 约束;
或：
ALTER TABLE 表名字 ADD 列名字 数据类型 约束;

ALTER TABLE employee ADD height INT(4) DEFAULT 170;
```

  修改值

```sql
UPDATE 表名字 SET 列1=值1,列2=值2 WHERE 条件;

UPDATE employee SET age=21,salary=3000 WHERE name='Tom';
```

  删除一条数据

```sql
DELETE FROM 表名字 WHERE 条件;

DELETE FROM employee WHERE name='Tom';
```

## 5.其他

​    索引

```java
ALTER TABLE 表名字 ADD INDEX 索引名 (列名);

CREATE INDEX 索引名 ON 表名字 (列名);

ALTER TABLE employee ADD INDEX idx_id (id);  #在employee表的id列上建立名为idx_id的索引

CREATE INDEX idx_name ON employee (name);   #在employee表的name列上建立名为idx_name的索引
```





  视图

```sql
CREATE VIEW 视图名(列a,列b,列c) AS SELECT 列1,列2,列3 FROM 表名字;

CREATE VIEW v_emp (v_name,v_age,v_phone) AS SELECT name,age,phone FROM employee;
```

  导入和导出

```sql
LOAD DATA INFILE '文件路径和文件名' INTO TABLE 表名字;

SELECT 列1，列2 INTO OUTFILE '文件路径和文件名' FROM 表名字;
SELECT * INTO OUTFILE '/var/lib/mysql-files/out.txt' FROM employee;
```

  备份

```sql
mysqldump -u root 数据库名>备份文件名;   #备份整个数据库

mysqldump -u root 数据库名 表名字>备份文件名;  #备份整个表

mysqldump -u root mysql_shiyan > bak.sql;
```

  恢复

```sql
1.
source /tmp/SQL6/MySQL-06.sql
2.
mysql -u root test < bak.sql
```

