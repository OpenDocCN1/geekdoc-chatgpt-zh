从本体到SQL：一次教学关系查询的拆解实录

最近在整理校园知识图谱的例子时，碰到一套很典型的建模到查询再到关系映射的完整链路。先把人员、师生层级和教学关系在本体里固定下来，再用 SPARQL 把教师的各种出现方式统一起来，最后落到 MySQL 的映射和手工 SQL 重写。整个过程能清楚看到继承、属性的子属性以及 UNION 聚合在实际查询里的作用。下面把关键片段原样保留，方便对照。

本体与关系约束的基础定义
[PRE0]

SubClassOf(AdminStaff, Person)

SubClassOf(Student, Person)

SubClassOf(GraduateStudent, Student)

SubClassOf(UnderGraduateStudent, Student)

SubClassOf(AssistantProfessor, Professor)

SubClassOf(AssociateProfessor, Professor)

SubClassOf(FullProfessor, Professor)

ObjectPropertyDomain(teaches, Teacher)

ObjectPropertyRange(teaches, Teacher)

SubPropertyOf(givesLab, teaches)

SubPropertyOf(givesLecture, teaches)

先确定 Person 下的行政、学生、教授分支，学生再分本科与研究生，教授分助理、副教授、正教授。teaches 的域和值域都落在 Teacher，givesLab 与 givesLecture 则是 teaches 的子属性。

教师身份的 SPARQL 聚合写法
[PRE1]

SELECT ?teacher FROM {

SELECT ?teacher WHERE {?teacher a Teacher} UNION

SELECT ?teacher WHERE {?teacher a Professor } UNION

SELECT ?teacher WHERE {?teacher a FullProfessor } UNION

SELECT ?teacher WHERE {?teacher a AssistantProfessor } UNION

SELECT ?teacher WHERE {?teacher a AssociateProfessor } UNION

SELECT ?teacher WHERE {?teacher teaches ?course } UNION

SELECT ?teacher WHERE {?teacher givesLab ?course} UNION

SELECT ?teacher WHERE {?teacher givesLecture ?course }

}

这里用多条 UNION 把显式类型和通过教学关系反推出来的主体都收敛为 ?teacher。

最简的教学配对查询
[PRE2]

SELECT ?teacher ?course WHERE {\?teacher teaches ?course }

直接取 teaches 的两端。

把子属性也纳入的配对查询
[PRE3]

SELECT ?teacher ?course FROM {

SELECT ?teacher ?course WHERE {\?teacher teaches ?course } UNION

SELECT ?teacher ?course WHERE {\?teacher givesLab ?course} UNION

SELECT ?teacher ?course WHERE {\?teacher givesLecture ?course }

}

因为 givesLab、givesLecture 都是 teaches 的子属性，单独枚举能保证语义等价。

带姓名的查询变体
[PRE4]

q3′ = SELECT ?fn ?ln ?course FROM {

SELECT ?fn ?ln ?course WHERE {?teacher 

firstName ?fn. ?teacher :lastName ?ln . ?teacher teaches ?course } UNION

SELECT ?fn ?ln ?course WHERE {?teacher firstName ?fn. ?teacher 

givesLab ?course} UNION

SELECT ?fn ?ln ?course WHERE {?teacher firstName ?fn. ?teacher 

givesLecture ?course }

}

把 firstName、lastName 关联进来，同时保持三种教学方式的并集。

关系表到 RDF 的映射规则
[PRE5]

:person/{id} firstName {first_name} . <- SELECT id, first_name FROM tbl_person .

:person/{id} lastName {last_name} . <- SELECT id, last_name FROM tbl_person .

:person/{id} a AdminStaff . <- SELECT id FROM tbl_person WHERE position = 1

:person/{id} a UnderGraudateStudent . <- SELECT id FROM tbl_person WHERE position = 2

:person/{id} a GraudateStudent . <- SELECT id FROM tbl_person WHERE position = 3

:person/{id} a AssistantProfessor . <- SELECT id FROM tbl_person WHERE position = 4

:person/{id} a AssociateProfessor . <- SELECT id FROM tbl_person WHERE position = 5

:person/{id} a FullProfessor . <- SELECT id FROM tbl_person WHERE position = 6

:person/{teacher_id} givesLecture :course/{c_id}. <- SELECT teacher_id, c_id FROM tbl_course

:person/{ta_id} givesLab :course/{c_id}. <- SELECT id, c_id FROM tbl_teaching_assistant

人员基本信息和角色由 tbl_person 的 position 字段驱动，课程授课和助教实验分别来自 tbl_course 和 tbl_teaching_assistant。

教师列表的 SQL 重写示例
[PRE6]

SELECT teacher FROM (

 (SELECT CONCAT(`:person/`, id)AS teacher FROM tbl_person WHERE position = 

 4)UNION

 (SELECT CONCAT(`:person/`, id)AS teacher FROM tbl_person WHERE position = 

 5)UNION

 (SELECT CONCAT(`:person/`, id)AS teacher FROM tbl_person WHERE position = 

 6)UNION

 (SELECT CONCAT(`:person/`, teacher_id)AS teacher FROM tbl_course) UNION 

 (SELECT CONCAT(`:person/`, ta_id)AS teacher FROM tbl_teaching_assistant)

 )

把三种教授职位和实际授课、带实验的人员都拼成统一的 URI 形式。

去重与合并的另一种写法
[PRE7]

SELECT CONCAT(`:person/`, id)AS teacher FROM (

 (SELECT id AS id FROM tbl_person WHERE position = 4 OR position = 4 OR 

 position = 4) UNION

 (SELECT teacher_id AS id FROM tbl_course)UNION

 (SELECT ta_id AS id FROM tbl_teaching_assistant)

 )

把 id 统一后在外层拼接，减少重复的 CONCAT 计算。

教师课程对的提取
[PRE8]

SELECT teacher, y FROM (

 (SELECT CONCAT(`:person/`, teacher_id)AS teacher, CONCAT(`:course/`, c_id)AS y 

 FROM tbl_course) UNION

 ( SELECT CONCAT(`:person/`, teacher_ta_id)AS teacher, CONCAT(`:course/`, c_id)AS y FROM 

 tbl_teaching_assistant)

 )

[PRE9]

SELECT CONCAT(`:person/`, teacher_id)AS teacher, CONCAT(`:course/`, c_id)AS y FROM (

 (SELECT teacher_id AS id, c_id AS y FROM tbl_course) UNION

 (SELECT ta_id AS id, c_id FROM tbl_teaching_assistant)

 )

上面两段展示了先拼接再合并与先合并再拼接的不同路径。

带姓名的课程查询尝试
[PRE10]

SELECT course, fn, ln FROM (

 (SELECT CONCAT(`:course/`, c_id)AS course, first_name AS fn, last_name 

 AS ln

FROM tbl_person, tbl_course WHERE tbl_person.id = tbl_course.teacher_id) UNION \n ( SELECT CONCAT(`:person/`, teacher_ta_id)AS teacher, CONCAT(`:course/`, c_id)AS course, first_name AS fn, last_name AS ln \n FROM tbl_person, tbl_teaching_assistant WHERE tbl_person.id = \n tbl_teaching_assistant.ta_id)

[PRE11]

SELECT CONCAT(`:course/`, c_id)AS course, fn, ln FROM (\n (SELECT c_id, first_name AS fn, last_name AS ln \n FROM tbl_course, tbl_course WHERE tbl_person.id = tbl_course.teacher_id, \n first_name AS fn, last_name AS ln) UNION \n (SELECT c_id, first_name AS fn, last_name AS ln \n FROM tbl_teaching_assistant, tbl_teaching_assistant, first_name AS fn, \n last_name AS ln WHERE tbl_person.id = tbl_teaching_assistant.ta_id)\n )

最后两个片段体现了把课程、姓名一起拉出来时容易出现的表关联和别名混淆问题，也是优化时需要仔细校对的地方。

整体来看，从本体层级的定义，到利用 UNION 覆盖子类型和子属性，再到关系映射和 SQL 重写，每一步都在做同一件事：让不同来源、不同粒度的数据在语义上对齐。把这套思路固定下来，后续扩展新角色或新教学方式时，只需要增补对应的映射分支即可。