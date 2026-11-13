![](bbox=[0.293, 0.089, 0.78, 0.349])

# 图 9-13 城市初始状态结果图

(3) 运行 update()方法，绘制每个邻居种族相似比阈值的最终分布。代码如下。

```
schelling_1.update()
schelling_2.update()
schelling_3.update()
schelling_1.plot('Schelling Model with 2 colors: Final State with Similarity Threshold
30%','schelling_2_30_final.png')
schelling_2.plot('Schelling Model with 2 colors: Final State with Similarity Threshold
50%','schelling_2_50_final.png')
schelling_3.plot('Schelling Model with 2 colors: Final State with Similarity Threshold
80%','schelling_2_80_final.png')
```

其结果如图 9-14 所示。

![](bbox=[0.29, 0.605, 0.78, 0.865])

# (a) similarity_threshold=30%分布图

# 图 9-14 最终分布的结果图