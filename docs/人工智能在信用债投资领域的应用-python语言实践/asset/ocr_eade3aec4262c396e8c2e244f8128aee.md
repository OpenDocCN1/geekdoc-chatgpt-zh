df.columns=['a','b','c']: 重命名列名
pd.isnull(): 检查 DataFrame 对象中的空值,并返回一个 Boolean 数组
pd.notnull(): 检查 DataFrame 对象中的非空值,并返回一个 Boolean 数组
df.dropna(): 删除所有包含空值的行
df.dropna(axis=1): 删除所有包含空值的列
df.dropna(axis=1,thresh=n): 删除所有小于 n 个非空值的行
df.fillna(x): 用 x 替换 DataFrame 对象中所有的空值
s.fillna(s.mean()): 用均值填充所有的 na
s.astype(float): 将 Series 中的数据类型更改为 float 类型
s.replace(1,'one'): 用‘one’代替所有等于 1 的值
s.replace([1,3],['one','three']): 用‘one’代替 1,用‘three’代替 3
df.rename(columns=lambda x: x + 1): 批量更改列名
df.rename(columns={'old_name': 'new_name'}): 选择性更改列名
df.set_index('column_one'): 更改索引列
df.rename(index=lambda x: x + 1): 批量重命名索引
7) 数据处理：Filter、Sort 和 GroupBy
df[df[col] > 0.5]: 选择 col 列的值大于 0.5 的行
df[(df[col]>0.5) & (df[col]<0.7)]: 选择 col 列的值大于 0.5 且小于 0.7 的行
df.sort_values(col1): 按照列 col1 排序数据,默认升序排列
df.sort_values(col2, ascending=False): 按照列 col1 降序排列数据
df.sort_values([col1,col2], ascending=[True,False]): 先按列 col1 升序排列,后按 col2 降序排列数据
df.groupby(col): 返回一个按列 col 进行分组的 Groupby 对象
df.groupby([col1,col2]): 返回一个按多列进行分组的 Groupby 对象
df.groupby(col1)[col2].mean(): 返回按列 col1 进行分组后,列 col2 的均值
df.pivot_table(index=col1, values=[col2,col3], aggfunc=max): 创建一个按列 col1 进行分组,并计算 col2 和 col3 的最大值的數據透视表
df.groupby(col1).agg(np.mean): 返回按列 col1 分组的所有列的均值
data.apply(np.mean): 对 DataFrame 中的每一列应用函数 np.mean
data.apply(np.max, axis=1): 对 DataFrame 中的每一行应用函数 np.max
8) 数据合并
df1.append(df2): 将 df2 中的行添加到 df1 的尾部
pd.concat([df1, df2],axis=1): 将 df2 中的列添加到 df1 的尾部