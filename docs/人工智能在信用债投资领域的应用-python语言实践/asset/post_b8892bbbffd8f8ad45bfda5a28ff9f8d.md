
pd.read_sql(query, connection_object): 从 SQL 表/库导入数据
pd.read_json(json_string): 从 JSON 格式的字符串导入数据
pd.read_html(url): 解析 URL、字符串或者 HTML 文件, 抽取其中的 tables 表格
pd.read_clipboard(): 从你的粘贴板获取内容, 并传给 read_table()
pd.DataFrame(dict): 从字典对象导入数据, Key 是列名, Value 是数据
### 2) 导出数据
df.to_csv(filename): 导出数据到 CSV 文件
df.to_excel(filename): 导出数据到 Excel 文件
df.to_sql(table_name, connection_object): 导出数据到 SQL 表
df.to_json(filename): 以 Json 格式导出数据到文本文件
df.to_html(filename): 导出数据到 HTML 文件
df.to_clipboard(): 导出数据到粘贴板
### 3) 创建测试对象
pd.DataFrame(np.random.rand(20,5)): 创建 20 行 5 列的随机数组成的 DataFrame 对象
pd.Series(my_list): 从可迭代对象 my_list 创建一个 Series 对象
df.index = pd.date_range('1900/1/30', periods=df.shape[0]): 增加一个日期索引
### 4) 查看、检查数据
df.head(n): 查看 DataFrame 对象的前 n 行
df.tail(n): 查看 DataFrame 对象的最后 n 行
df.shape(): 查看行数和列数
df.info(): 查看索引、数据类型和内存信息
df.describe(): 查看数值型列的汇总统计
s.value_counts(dropna=False): 查看 Series 对象的唯一值和计数
df.apply(pd.Series.value_counts): 查看 DataFrame 对象中每一列的唯一值和计数
### 5) 数据选取
df[col]: 根据列名, 并以 Series 的形式返回列
df[[col1, col2]]: 以 DataFrame 的形式返回多列
s.iloc[0]: 按位置选取数据
s.loc['index_one']: 按索引选取数据
df.iloc[0,:]: 返回第一行
df.iloc[0,0]: 返回第一列的第一个元素
### 6) 数据清理
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
### 7) 数据处理：Filter、Sort 和 GroupBy
df[df[col] > 0.5]: 选择 col 列的值大于 0.5 的行
df[(df[col]>0.5) & (df[col]<0.7)]: 选择 col 列的值大于 0.5 且小于 0.7 的行
df.sort_values(col1): 按照列 col1 排序数据,默认升序排列
df.sort_values(col2, ascending=False): 按照列 col2 降序排列数据
df.sort_values([col1,col2], ascending=[True,False]): 先按列 col1 升序排列,后按 col2 降序排列数据
df.groupby(col): 返回一个按列 col 进行分组的 Groupby 对象
df.groupby([col1,col2]): 返回一个按多列进行分组的 Groupby 对象
df.groupby(col1)[col2].mean(): 返回按列 col1 进行分组后,列 col2 的均值
df.pivot_table(index=col1, values=[col2,col3], aggfunc=max): 创建一个按列 col1 进行分组,并计算 col2 和 col3 的最大值的 数据透视表
df.groupby(col1).agg(np.mean): 返回按列 col1 分组的所有列的均值
data.apply(np.mean): 对 DataFrame 中的每一列应用函数 np.mean
data.apply(np.max, axis=1): 对 DataFrame 中的每一行应用函数 np.max
### 8) 数据合并
df1.append(df2): 将 df2 中的行添加到 df1 的尾部
pd.concat([df1, df2],axis=1): 将 df2 中的列添加到 df1 的尾部
df1.join(df2,on=col1,how='inner'): 对 df1 的列和 df2 的列执行 SQL 形式的 join
pd.merge(df1,df2): 合并 df1 和 df2
### 9) 数据统计
- `df.describe()`: 查看数据值列的汇总统计
- `df.mean()`: 返回所有列的均值
- `df.corr()`: 返回列与列之间的相关系数
- `df.count()`: 返回每一列中的非空值的个数
- `df.max()`: 返回每一列的最大值
- `df.min()`: 返回每一列的最小值
- `df.median()`: 返回每一列的中位数
- `df.std()`: 返回每一列的标准差
## 3. Matplotlib
Matplotlib 是一个用于创建高质量图表的绘图包（主要是 2D 图形）。如果需要绘制 3D 图形,需要使用 mplot3d 的插件。
### 1) 准备数据
```
```python
# 一维数据
import numpy as np
x=np.linspace(0, 10, 100)
y=np.cos(x)
z=np.sin(x)
# 二维数据或图像
data=2 * np.random.random((10, 10))
data2=3 * np.random.random((10, 10))
Y, X=np.mgrid[-3:3:100j, -3:3:100j]
U=-1 -X**2 +Y
V=1 +X -Y**2
from matplotlib.cbook import get_sample_data
img=np.load(get_sample_data('axes_grid/bivariate_normal.jpg'))
```
### 2) 创建绘图
```
```python
import matplotlib.pyplot as plt
#图形 (figure)
fig=plt.figure()
fig2=plt.figure(figsize=plt.figaspect(2.0))
#坐标轴
fig.add_axes()
```
```
ax1=fig.add_subplot(221)
ax3=fig.add_subplot(212)
fig3, axes=plt.subplots(nrows=2,ncols=2)
fig4, axes2=plt.subplots(ncols=3)
```
### 3) 绘图的范例
```
#一维数据绘图
fig, ax=plt.subplots()
lines=ax.plot(x,y)
ax.scatter(x,y)
axes[0,0].bar([1,2,3],[3,4,5])
axes[1,0].barh([0.5,1,2.5],[0,1,2])
axes[1,1].axhline(0.45)
axes[0,1].axvline(0.65)
ax.fill(x,y,color='blue')
ax.fill_between(x,y,color='yellow')
#向量绘图
axes[0,1].arrow(0,0,0.5,0.5)
axes[1,1].quiver(y,z)
axes[0,1].streamplot(X,Y,U,V)
#数据分布图
ax1.hist(y)
ax3.boxplot(y)
ax3.violinplot(z)
#绘制二维数据图或图像
fig, ax=plt.subplots()
im=ax.imshow(img,cmap='gist_earth',interpolation='nearest',vmin=-2,vmax=2)
axes2[0].pcolor(data2)
axes2[0].pcolormesh(data)
CS=plt.contour(Y,X,U)
axes2[2].contourf(data1)
axes2[2]=ax.clabel(CS)
```
### 4) 定制化绘图
```
#颜色
plt.plot(x, x, x, x**2, x, x**3)
ax.plot(x, y, alpha=0.4)
ax.plot(x, y, c='k')
fig.colorbar(im, orientation='horizontal')
im=ax.imshow(img,cmap='seismic')
#标记
```
```
fig, ax=plt.subplots()
ax.scatter(x,y,marker=".")
ax.plot(x,y,marker="o")
#线型
plt.plot(x,y,linewidth=4.0)
plt.plot(x,y,ls='solid')
plt.plot(x,y,ls='--')
plt.plot(x,y,'--',x**2,y**2,'-.')
plt.setp(lines,color='r',linewidth=4.0)
#标注
ax.text(1,-2.1,'Example Graph',style='italic')
ax.annotate("Sine", xy=(8,0), xycoords='data', xytext=(10.5,0), textcoords='data',arrowprops=dict(arrowstyle="->", connectionstyle="arc3"),)
#数字文本
plt.title(r'$ sigma_i=15$ ', fontsize=20)
#坐标范围、比例缩放
ax.margins(x=0.0,y=0.1)
ax.axis('equal')
ax.set(xlim=[0,10.5],ylim=[-1.5,1.5])
ax.set_xlim(0,10.5)
#标题
ax.set(title='An Example Axes',ylabel='Y-Axis',xlabel='X-Axis')
ax.legend(loc='best')
#标记
ax.xaxis.set(ticks=range(1,5),ticklabels=[3,100,-12,"foo"])
ax.tick_params(axis='y',direction='inout',length=10)
#绘制子图
fig3.subplots_adjust(wspace=0.5,hspace=0.3,left=0.125,right=0.9, top=0.9,bottom=0.1)
fig.tight_layout()
#坐标轴分区
ax1.spines['top'].set_visible(False)
ax1.spines['bottom'].set_position(('outward',10))
#保存图像
plt.savefig('foo.png')
#保存为透明的图像
plt.savefig('foo.png', transparent=True)
```
### 5) 保存绘图
### 6）显示绘图、清空和关闭窗口
```
plt.show()
plt.cla() Clear an axis
plt.clf() Clear the entire figure
plt.close() Close a window
```
## 4. Scikit-learn
Scikit-learn 是用于机器学习的开源 Python 库,它集成了大量的机器学习算法,还包括数据预处理、交叉验证和可视化算法等。
### 1）加载数据
```
import numpy as np
X=np.random.random((10,5))
y=np.array(['M','M','F','F','M','F','M','M','F','F','F'])
X[X<0.7]=0
```
### 2）训练样本和测试样本
```
from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test=train_test_split(X,y,random_state=0)
```
### 3）数据预处理
```
#标准化 (Standardization)
from sklearn.preprocessing import StandardScaler
scaler=StandardScaler().fit(X_train)
standardized_X=scaler.transform(X_train)
standardized_X_test=scaler.transform(X_test)
#归一化 (Normalization)
from sklearn.preprocessing import Normalizer
scaler=Normalizer().fit(X_train)
normalized_X=scaler.transform(X_train)
normalized_X_test=scaler.transform(X_test)
#特征二值化 (Binarization)
from sklearn.preprocessing import Binarizer
binarizer=Binarizer(threshold=0.0).fit(X)
binary_X=binarizer.transform(X)
#编码分类特征
from sklearn.preprocessing import LabelEncoder
enc=LabelEncoder()
y=enc.fit_transform(y)
#填充缺失值
```
```
from sklearn.preprocessing import Imputer
imp=Imputer(missing_values=0, strategy='mean', axis=0)
imp.fit_transform(X_train)
#产生多项式特征
from sklearn.preprocessing import PolynomialFeatures
poly=PolynomialFeatures(5)
poly.fit_transform(X)
```
### 4) 构建模型
```
#有监督机器学习-线性回归
from sklearn.linear_model import LinearRegression
lr=LinearRegression(normalize=True)
#有监督机器学习-支持向量机(SVM)
from sklearn.svm import SVC
svc=SVC(kernel='linear')
#有监督机器学习-朴素贝叶斯
from sklearn.naive_bayes import GaussianNB
gnb=GaussianNB()
#有监督机器学习-K近邻(KNN)
from sklearn import neighbors
knn=neighbors.KNeighborsClassifier(n_neighbors=5)
#无监督机器学习-主成分分析(PCA)
from sklearn.decomposition import PCA
pca=PCA(n_components=0.95)
#无监督机器学习-K均值(K-Means)
from sklearn.cluster import KMeans
k_means=KMeans(n_clusters=3, random_state=0)
```
### 5) 拟合模型
```
#有监督学习
lr.fit(X, y)
knn.fit(X_train, y_train)
svc.fit(X_train, y_train)
#无监督学习
k_means.fit(X_train)
pca_model=pca.fit_transform(X_train)
```
### 6) 预测
```
#有监督机器学习评估
y_pred=svc.predict(np.random.random((2,5)))
y_pred=lr.predict(X_test)
```
### 7) 模型检验
```
y_pred=knn.predict_proba(X_test)
# 无监督机器学习评估
y_pred=k_means.predict(X_test)
# 分类模型检验标准
# (1)准确度分数
knn.score(X_test, y_test)
from sklearn.metrics import accuracy_score
accuracy_score(y_test, y_pred)
# (2)分类模型检验报告
from sklearn.metrics import classification_report
print(classification_report(y_test, y_pred))
# (3)混淆矩阵
from sklearn.metrics import confusion_matrix
print(confusion_matrix(y_test, y_pred))
# 回归模型检验标准
# (1)平均绝对误差
from sklearn.metrics import mean_absolute_error
y_true=[3, -0.5, 2]
mean_absolute_error(y_true, y_pred)
# (2)均方误差
from sklearn.metrics import mean_squared_error
mean_squared_error(y_test, y_pred)
# (3)R2 分数
from sklearn.metrics import r2_score
r2_score(y_true, y_pred)
# 聚类模型检验标准
# (1)兰德指数 (Adjusted Rand Index)
from sklearn.metrics import adjusted_rand_score
adjusted_rand_score(y_true, y_pred)
# (2)因子分布的同质性,均一性 (Homogeneity)
from sklearn.metrics import homogeneity_score
homogeneity_score(y_true, y_pred)
# (3)V-Measure (均一性和完整性的加权平均)
from sklearn.cross_validation import cross_val_score
print(cross_val_score(knn, X_train, y_train, cv=4))
print(cross_val_score(lr, X, y, cv=2))
# (4)交叉验证 (Cross-Validation)
from sklearn.cross_validation import cross_val_score
print(cross_val_score(knn, X_train, y_train, cv=4))
print(cross_val_score(lr, X, y, cv=2))
```
