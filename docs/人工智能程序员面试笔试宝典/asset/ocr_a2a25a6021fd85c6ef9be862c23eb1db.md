课程信息表C(CNO, CNAME, CTEACHER)，其属性分别表示课程号、课程名称和任课老师姓名。

1）把SC表中每门课程的平均成绩插入另外一个已经存在的表 SC_C(CNO, CNAME, AVG_GRADE)中，其中AVG_GRADE表示的是每门课程的平均成绩。

```
INSERT INTO SC_C(CNO,CNAME,AVG_GRADE)
SELECT SC.CNO, C.CNAME, AVG(SCGRADE) FROM SC, C WHERE SC.CNO = C.CNO GROUP BY SC.CNO ,C.CNAME
```

2）给出两种从S表中删除数据的方法。

① 使用delete语句删除，这种方法删除后的数据是可以恢复的。

② 使用truncate语句删除，这种方法删除后的数据是无法恢复的。

```
delete from S
truncate table S
```

3）从SC表中把何昊老师的女学生选课记录删除。

```
DELETE FROM SC WHERE CNO=(SELECT CNO FROM C WHERE C.CTEACHER ='何昊') AND SNO IN (SELECT SNO FROM S WHERE SEX='女')
```

4）列出有两门以上（含两门）不及格课程（成绩小于60）的学生姓名及其平均成绩。

```
SELECT S.SNO,S.SNAME,AVG_SC GRADE=AVG(SC.SC GRADE)
FROM S,SC,(
SELECT SNO FROM SC WHERE SC GRADE<60 GROUP BY SNO
HAVING COUNT(DISTINCT CNO)>=2)A WHERE S.SNO=A.SNO AND SC.SNO=A.SNO
GROUP BY S.SNO,S.SNAME
```

5）列出既学过“1”号课程，又学过“2”号课程的所有学生姓名。