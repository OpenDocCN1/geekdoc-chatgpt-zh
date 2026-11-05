| Property | Expected Type | Description |
|---|---|---|
| Properties from Person | | |
| additionalName | Text | An additional name for a Person, can be used for a middle name. |
| address | PostalAddress or Text | Physical address of the item. |
| affiliation | Organization | An organization that this person is affiliated with. For example, a school/university, a club, or a team. |
| alumniOf | EducationalOrganization or Organization | An organization that the person is an alumni of. Inverse property: alumni. |
| award | Text | An award won by or for this item. Supersedes awards. |
| birthDate | Date | Date of birth. |
| birthPlace | Place | The place where the person was born. |
| brand | Brand or Organization | The brand(s) associated with a product or service, or the brand(s) maintained by an organization or business person. |
| children | Person | A child of the person. |
| colleague | Person or URL | A colleague of the person. Supersedes colleagues. |
| contactPoint | ContactPoint | A contact point for a person or organization. Supersedes contactPoints. |
| deathDate | Date | Date of death. |
| deathPlace | Place | The place where the person died. |
| duns | Text | The Dun & Bradstreet DUNS number for identifying an organization or business person. |

图 3.6 Schema.org 人物类型的部分属性集

# 3. Freebase

Freebase 是谷歌知识图谱的重要组成部分，也是一种大众编辑的协同知识图谱。Freebase 用领域（domain）组织概念，例如，人物和职业都是属于人这个领域下面的概念/类型，因此它们的符号表示分别为/people/person 和/people/profession。每个概念有其特有的一些属性，比如，人物有出生地、年龄、配偶、国籍等属性。Freebase 对每个类型定义了若干关系，并制定关系的值域（range）约束其取值（表 3.2 列出了 Freebase 中 Person 概念的属性列表及其属性值类型）。Freebase 定义了 2000 多个概念类型和近 4 万个属性。Freebase 的一大特色就是使用组合数据类型（Compound Value Types, CVT[资源 3-13]）的方式表示事件等 N-Triple 事实。例如，如图 3.7 所示，一个人可能有多段婚姻，每段婚姻的对象、起止时间、结婚地点都不一样，如果简单地都

[资源3-13]
Freebase 的 CVT