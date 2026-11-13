```
Field = ea.crtfld(Encoding, problem.varTypes, problem.ranges, problem.borders)
population = ea.Population(Encoding, Field, NIND)
myAlgorithm = ea.soea_SEGA_templet(problem, population)
myAlgorithm.MAXGEN = algorithm # 最大进化代数
myAlgorithm.drawing = 0 # 不画图
""""""""""""""""""""""调用算法模板进行种群进化
""""""""""""""""""""""""""""
[population, obj_trace, var_trace] = myAlgorithm.run() # 执行算法模板
population.save() # 把最后一代种群的信息保存到文件中
# 输出结果
best_gen = np.argmin(problem.maxormins * obj_trace[:, 1]) # 记录最优种群个体是
体是在哪一代
best_ObjV = obj_trace[best_gen, 1]
x_pre = var_trace[best_gen, :].reshape(1, -1)
y_pre = best_ObjV
return x_pre, y_pre
```

在子函数 “Optimization_antenna_3models”, 设置遗传算法优化变量、目标、约束等。利用低保真度全波仿真模型对利用遗传算法得到参数组合进行仿真，仿真后更新数据集并进行再训练，再预测，并对其中最好的一组参数组合进行高保真度全波仿真：

```
# 带入高保真度全波仿真进行验证
S11_f_pre, fre_S_pre = cst_tri_sample(data_fine_pre[0, 0:dim_opti].reshape(1,
-1), para_select_name, dim_opti, 1, model_path_fine, fullname_temp, soft_path_
1, soft_path_2)
```