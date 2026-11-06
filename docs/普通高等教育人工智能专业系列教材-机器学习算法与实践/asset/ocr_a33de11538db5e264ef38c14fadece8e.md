```
variable_card=3,          # 节点取值个数
values=[[0.3, 0.05, 0.9, 0.5],
[0.4, 0.25, 0.08, 0.3],
[0.3, 0.7, 0.02, 0.2]],
evidence=['C', 'D'],       # 该节点的依赖节点
evidence_card=[2, 2]      # 依赖节点的取值个数
)
diffi_cpd = TabularCPD(
    variable='D',
    variable_card=2,
    values=[[0.6, 0.4]]
)
capab_cpd = TabularCPD(
    variable='C',
    variable_card=2,
    values=[[0.7, 0.3]]
)
scholar_cpd = TabularCPD(
    variable='S',
    variable_card=2,
    values=[[0.1, 0.4, 0.99],
    [0.9, 0.6, 0.01]],
    evidence=['G'],
    evidence_card=[3]
)
exam_cpd = TabularCPD(
    variable='E',
    variable_card=2,
    values=[[0.95, 0.2],
    [0.05, 0.8]],
    evidence=['C'],
    evidence_card=[2]
)
```

3) 将包含概率表的各节点添加到模型中。

```
student_model.add_cpds(
    grade_cpd,
    diffi_cpd,
    capab_cpd,
    scholar_cpd,
    exam_cpd
)
```

4) 获取模型的条件概率分布和模型各节点之间的依赖关系。

```
student_model.get_cpds()
student_model.get_independencies()
```

5) 进行贝叶斯推断。