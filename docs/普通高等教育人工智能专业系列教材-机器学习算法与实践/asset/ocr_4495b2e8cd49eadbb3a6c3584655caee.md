# (3) 生成距离矩阵和密度矩阵

代码 11-9 是对之前计算的距离矩阵做进一步处理，并计算样本的密度估计值、高密度样本的最小距离以及截断点。calculate_density() 函数计算了密度和距离高密度点的最小距离。首先，通过两个 for 循环计算了每个数据点的密度。其中，distance_cutoff_i 存储了将距离减去截断值后的距离。然后，找到密度数组中的最大值 Max，并使用一个列表 MaxIndexList 存储具有最大密度的数据点的索引。接下来，依次遍历每个数据点，并判断它是否为最大密度点。如果是，则将该数据点与其他数据点的距离中的最大值存储到 distance_higherDensity 数组中。对于不是最大密度点的数据点，在它与其他数据点的距离中找到满足密度和距离的条件下最小值，并将其存储到 distance_higherDensity 数组中。最后，返回计算得到的密度数组、距离高密度点的最小距离数组和截断值。

# 代码 11-9   calculate_density 函数代码

```
def calculate_density( test_data, cutoff):
    density = np.zeros( len( labels) )               # 初始化密度数组
    for index_i in range( len( labels) ):
        distance_cutoff_i = distance[ index_i ] - cutoff
        for index_j in range( 1, len( labels) ):
            density[ index_i ] += chi( distance_cutoff_i[ index_j ] ) # 通过 chi() 函数计算密度值
    Max = np.max( density)                           # 密度最大值
    MaxIndexList = []                                # 存储密度最大值的索引列表
    for index_i in range( len( labels) ):
        if density[ index_i ] == Max:
            MaxIndexList.extend( [ index_i ] )        # 将密度最大值的索引加入列表中
    Min = 0
    for index_i in range( len( labels) ):
        if index_i in MaxIndexList:
            distance_higherDensity[ index_i ] = np.max( distance[ index_i ] )
            continue
        else:
            Min = np.max( distance[ index_i ] )
            for index_j in range( 1, len( labels) ):
                if density[ index_i ] < density[ index_j ] and distance[ index_i, index_j ] < Min:
                    Min = distance[ index_i, index_j ]
                else:
                    continue
            distance_higherDensity[ index_i ] = Min    # 存储距离高密度点的最小距离
    return density, distance_higherDensity, cutoff
```

# (4) 初始值设定，距离比对

代码 11-10 的功能是根据计算得到的密度估计值和高密度样本的最小距离，计算一个参数 theta，并打印输出。

# 代码 11-10   距离比对代码

```
import numpy as np
# 完善 fit 函数的定义
def fit( test_data, t ):
    # 假设这里是 fit 函数的实现
```